---
title: "Encoding Data"
categories:
  - scalability
tags:
  - database
  - notes
toc: true
---

When we try to add new features into the application code and the data also need to be adapted, with both old and new data, as well as old and new code, we usually have two problems to consider

**Backward compatibility**

Newer code can read data written by older code.


**Forward compatibility**

Older code can read data written by newer code.


# Language-Specific Encoding

Many programming languages come with built-in libraries to encode in-memory objects into byte sequences and to decode it later, as `pickle` for Python, `Marshal` for Ruby. They are convenient to use, but also have a number of problems

- Lack of support across different languages. Data encoded by one language is often difficult to read in another language.

- Difficult to restore the object even with the same programming language if the object class changed.


# Human-readable Textual Encoding

Textual formats like JSON, XML and CSV also have some problems

- Ambiguity for numbers
- Lack of binary string support
- For csv, difficult to have value that contains comma or newline character, not all parsers implement them correctly.
