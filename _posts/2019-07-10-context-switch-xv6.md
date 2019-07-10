---
title: "Context Switch in Xv6"
categories:
  - OS
tags:
  - OS
  - debug
  - assembly
  - C
  - notes
toc: true
---

Last weekend I wanted to refresh my memory about *assembly*, I took the context switching code in Xv6 as an example. It was quite interesting to walk through the code, here is my debugging process

# Assembly illustrated

{% capture fig_cw %}
![context_switch]({{ "/assets/images/context_switch.png" }})
{% endcapture %}

<figure>
  {{ fig_cw | markdownify | remove: "<p>" | remove: "</p>" }}
</figure>

`%esp` holds the address of the top stack element and the stack grows downward. In the beginning, the value in `%esp` is `0x8010b54c`. The two arguments (`context **old` and `context *new`) of `swtch` are just next to `%esp`, for a 32 bits machine, each of them takes 4 bytes. The idea behind this function is quite simple

- First, we copy the current *context* from register to the kernel stack in memory;
- Then we save the address of the top kernel stack in `*old`;
- In the end, the value held by `%esp` is changed to `new` and we can restore the context in the same format as we just left. The context switch is done.

# Callers

The `swtch` function is called twice in the code.

One is in the function `scheduler`, which scans all the process array. When it finds one process `p` `RUNNABLE`, it calls `swtch` to save the current context in `scheduler` and restore the process `p` (this is the case shown in the figure).

This is where the magic happens, before you enter into the function `swtch`, you are inside the method `scheduler`, you have your call stack all in the function `scheduler`, everything looks normal. While once you pass through `swtch` (more specifically after you change the top of the stack `%esp` and restored the stack next to it to register), suddenly it looks like you were calling `swtch` from the process `p` itself. In this way, the control is handed from `scheduler` to process.

The second place where `swtch` is called is in `sched`. `sched` is called is `yield`, `sleep` and `exit` etc. In general, it is called in places where the process wants to give the control back in OS.

The mode that `sched` and `scheduler` work cooperatively with each other can be referred to as *coroutine*. One works to some point then give control to the other and vice versa. I wonder if the `yield` keyword in Python is implemented in the same way.

## Context Switch in Xv6

Therefore, the context switch in Xv6 is not directly from process A to process B but via an intermediate scheduler. Each cpu has one scheduler attached.