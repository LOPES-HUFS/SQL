# Head First SQL

## 1. Data and tables

A **database** is a container that holds tables and other SQL structures related to those tables.

The information inside the database is organized into **tables**.

All tables are created with a **CREATE TABLE** statement, containing column names and their corresponding data types.

## 4. Smart table design: Why be normal?

### A table is all about relationalships

SQL is known as Relational Database Management System, or RDBMS. Don't bother memorizing it. We only care about word **RELATIONAL**. All this means to you is that to design a killer table, you need th consider *how* the **columns** *relate* to each other **to describe a thing**.

The challenge is to describe the thing using columns in a way that makes getting the information out of it easy. This depends on what you need from the table, but there are some very broad steps you follow when you're creating a table.

1. Pick your thing, **the one thing** you want your table to describe.
2. Make a **list of the information** you need to know about your one thing when your're **using the table**.
3. Using a list, **break down the information** about your thing into pices you can use for **organizing** your table.

### Atomic data

When it's **ATOMIC**, that means that it's been broken down into the **smallest pieces of data** *that can't or shouln't be divided*.

### Primary key rules

A primary key is a column in your table that makes each record unique.
