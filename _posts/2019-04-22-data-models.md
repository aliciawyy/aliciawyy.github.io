---
title: "Data Models"
categories:
  - scalability
tags:
  - scalability
  - notes
toc: true
---

# How we think about data models ?

Layer 1: As an application developer, we model the real world functional requirements into objects or data structures, and APIs to manipulate those data structures.

Layer 2: These data are then expressed as general-purpose data model, such as JSON or XML documents, tables in a relational database to be stored.

Layer 3: The engineers who built the database then decide on a way of representing the data on disk or on a network. The representation will allow the data to be queried and manipulated in different ways.

## Relational Model

This is the model for SQL. Data is organized into *relations*. A relation (table) is a collection of tuples (rows).

As most application development today is in OOP, for data stored in relational tables, a translation layer is required between the objects in tha application code and the database model of tables, rows and columns.


## Document Model

Document Model is appropriate when the data has mostly one-to-many relationships and no relationship between record.

### One-to-many and Many-to-many relationship

With SQL database, an one-to-many relationship is usually interpreted with separate tables, and one primary key is used to query all the information. We then perform a multi-way join between the main table and its subordinate tables (like user table and user's information tables). With a JSON representation, one query is sufficient to ge the tree structure explicit. However, the document representation can make many-to-many relationship difficult.

## Graph-based Mode
