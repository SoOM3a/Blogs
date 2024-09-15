---
title: MySql Enumeration
date: 2024-03-13 08:25:41 AM
categories: [RedTeam, Enumeration]
tags: [redteam]     # TAG names should always be lowercase
authors: [0]
media_subpath: /assets/img/services_enumerations/mysql/
---

## SQL 
In its simplest definition, MySQL is a relational database management system (RDBMS) based on Structured Query Language (SQL). Too many acronyms? Let's break it down:

Database:
A database is simply a persistent, organised collection of structured data

RDBMS:
A software or service used to create and manage databases based on a relational model. The word "relational" just means that the data stored in the dataset is organised as tables. Every table relates in some way to each other's "primary key" or other "key" factors.


### when you need to enumerate SQL?

MySQL is likely not going to be the first point of call when getting initial information about the server. You can, as we have in previous tasks, attempt to brute-force default account passwords if you really don't have any other information; however, in most CTF scenarios, this is unlikely to be the avenue you're meant to pursue.


| Metasploit Module |Module Description |
| :---------------- |------------------: |
| mysql_sql| module performs SQL queries on a remote server when provided with a valid set of credentials.|
| mysql_enum| module will connect to a remote MySQL database server with a given set of credentials and perform some basic enumeration on it.|
| mysql_schemadump| module extracts the schema information from a MySQL DB server.|

<script src="https://giscus.app/client.js"
        data-repo="SoOM3a/Blogs"
        data-repo-id="R_kgDOLebVZA"
        data-category="General"
        data-category-id="DIC_kwDOLebVZM4Cd9IX"
        data-mapping="url"
        data-strict="1"
        data-reactions-enabled="1"
        data-emit-metadata="1"
        data-input-position="top"
        data-theme="preferred_color_scheme"
        data-lang="en"
        data-loading="lazy"
        crossorigin="anonymous"
        async>
</script>