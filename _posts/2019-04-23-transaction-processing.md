---
title: "Transaction and Analytic processing"
categories:
  - scalability
tags:
  - database
  - notes
toc: true
---

*Transaction Processing* means allowing clients to make low-latency reads and writes, in contrast with *batch processing* that runs periodically. Usually a small number of records are looked up, inserted or updated with some key. This access pattern is also known as *online transaction processing (OLTP)*.

A different access pattern is *data analytics*, which is an analytic query that needs to scan over a huge number of records, but only read a few columns per record to calculate aggregate statistics. This is usually called *online analytic processing (OLAP)*. These operations can be huge and expensive.

# Data Warehousing

A *data warehouse* is a separate database that analysts can query their content without affecting OLTP operations. The data warehouse contains a *read-only* copy of the data in all the various OLTP systems in the company.

Data warehouse is quite common for large companies as they have many different OLTP systems and a lot of data to process. The advantage of using a separate data warehouse, rather than querying OLTP systems directly is that the data warehouse can be optimized for analytic access.
