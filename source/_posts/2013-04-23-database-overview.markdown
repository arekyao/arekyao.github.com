---
layout: post
title: "Database Overview"
date: 2013-04-23 22:46
comments: true
categories: Tools
---

Preface
-----


What's the most important?
Infomation!

How to get and save infomation?
Database.

Here is some story about database.

Generally, database can be divided into two arm, SQL and NoSQL.

[SQL](http://en.wikipedia.org/wiki/Structured_Query_Language)
------

SQL (Structured Query Language) is very common.  
mysql ,sybase and oracle are well-known as database, which is used a lot

|---
|Source  |  Common name | Full name
|-|:-|:-:
|Microsoft / Sybase    | T-SQL |	Transact-SQL
|MySQL	| SQL/PSM |	SQL/Persistent Stored Module (implements SQL/PSM)
|Oracle	| PL/SQL |	Procedural Language/SQL (based on Ada)
|Sybase	| Watcom-SQL	SQL Anywhere |Watcom-SQL Dialect
{: .mytable}


[NoSQL](http://en.wikipedia.org/wiki/Nosql)
-----


Feature:
It provides mechanism for storage and retrieval of data that use looser consistency models than traditional relational databases  
 
Achieve horizontal scaling and higher availability

* #####Document Store

Document is encoded to some 
In general, they all assume that documents **encapsulate** and **encode** data (or information) in some standard formats or encodings.
Such as, XML, Jason, YAML etc.

|---
|Name  |  Language|	Notes
|-|:-|:-:
|BaseX	|Java, XQuery	|XML database
|Couchbase Server|	Erlang, C++	|Support for JSON and binary documents
|Apache CouchDB	|Erlang|	JSON store
|MongoDB|	C++, C#	|BSON store (binary format JSON)
|SimpleDB|Erlang	|
|Oracle NoSQL Database	|Java	|
{: .mytable}


* #####Graph 

The kind of data could be social relations, public transport links, road maps or network topologies, for example.

* #####Key Value Store
kv cache in RAM  

Radis 

Memcached

Keyvalue stores on solid state or rotating disk

MemcacheDB

MongoDB


* #####Multivalue database

InfinityDB

* #####[Object database](http://en.wikipedia.org/wiki/Object_database)

* #####Tabular

Apache Accumulo

BigTable

Apache Hbase

Hypertable


Reference

1. [http://nosql-database.org/]()

