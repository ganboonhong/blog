---
title: "How Does Commit and Rollback Work"
date: 2018-01-07T14:02:55+08:00
summary: "The traditional rollback journal works by writing a copy of the original unchanged database content into a separate rollback journal file and then writing changes directly into the database file. In the event of a crash or rollback, ..."
author: "Ben"
thumbnail: "/refs/images/database/db-transaction.webp"

tags:
    - database
---

{{< imgborder "images/database/db-transaction.webp">}}

The traditional rollback journal works by writing a copy of the original unchanged database content into a separate rollback journal file and then writing changes directly into the database file.

In the event of a crash or rollback, the original content contained in the rollback journal is played back into the database file to revert the database file to its original state. The commit occurs when the rollback journal is deleted.


Example:
 

Let's say the original database file is named `name.db`.

When transaction begins, now we have another file created, let’s say `name.db-journal`, basically this is just a backup file of original one.

From now on, whatever changes will be done within name.db. After the changes, there will be 2 possible scenerios:


### Case 1: Commit

When user **commits** (which mean we want to **keep** the changes)

We discard the "backup" (`name.db-journal`), now the only file left is `name.db`.

 

### Case 2: Rollback

When user **rollbacks** (which mean we want to **discard** the changes)

We put the content in the `name.db-journal` back into `name.db`, then remove `name.db-journal`. So the only file left is `name.db`.

