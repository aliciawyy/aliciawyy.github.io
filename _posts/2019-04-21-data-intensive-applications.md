---
title: "Data-Intensive Applications"
categories:          
  - technical
  - scalability
tags:
  - scalability
  - notes
toc: true
---

There are three concerns in most software systems

**Reliability**

The system should tolerate system (hardware/software/humain) faults. To make system more fault tolerant, it makes sense to deliberately induce fault into the system. The netflix [Chaos Monkey](https://github.com/Netflix/chaosmonkey) is a resiliency tool that helps applications tolerate random instance failures.

**Scalability**

There should be reasonable ways to deal with system grows (data volume, traffic, complexity).

**Maintainability**

The system should be easy to maintain with different engineers (growing number, turnover).
