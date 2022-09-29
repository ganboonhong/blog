---
title: "SQLite Temporary Files"
date: 2018-01-07T21:36:22+08:00
thumbnail: "/refs/images/sqlite/temp-toilet.webp"

tags:
    - sqlite
---

{{< imgborder "images/sqlite/temp-toilet.webp">}}

I guess the name SQLite means it's a (relatively) light weight database.


All the data will be stored in just one database file. In this case, the backup task would be pretty easy. Basically just copy and paste, then end of story.

### Why we don't see those temporary files normally?

Although it has only one file in the **end** of process, it doesn't mean there's no other file **during** the process.
Those files are **created during the process** and **removed at the end of process**.

Normally, you won't see the *-journal file when you try to list the directory. But if you try to concurrent read and write request (I use JMeter to perfom concurrent request), you'll find that SQLite does generate temporary file to handle the process. When I stop sending the request, the ts.db-journal will disappear. 

### What happened under the hood?

Normally, the journal file will be generated when the transaction begins and be removed when the transaction is commited or rollbacked.

### The journal file won't be removed in the following scnerios:

When SQLite is crashed or power loss happened, the journal file won't be removed. The next time you open the database file (with command: sqlite3 ts.db), SQLite will notice that there is abandoned journal file (it's caled Hot Journal) exists in current directory, SQLite will try to restore the database to its state prior to the start of the incomplete transaction.
