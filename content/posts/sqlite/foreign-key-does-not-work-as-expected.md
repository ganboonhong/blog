---
title: "Foreign Key In SQLite Does Not Work As Expected"
date: 2022-10-16T22:18:51+08:00
description: "Foreign key constraints are disabled by default"
summary: When I was writing unit tests for my side project, I found that the foreign key cascade funtionality didn't work as expected.
thumbnail: "/blog/images/sqlite/foreign-key-does-not-work-as-expected.png"
tags: 
    - sqlite
---

{{< imgborder "images/sqlite/foreign-key-does-not-work-as-expected.png">}}

{{% notice warning "Problem" %}}
Sometimes, writing unit tests that have operations on database are required to clean up tables after each test (or several tests).
To garantee the unit tests don't affect each other, having a clean and isolated environment is critical.
When I was writing unit tests for my side project, I found that the foreign key cascade funtionality didn't work as expected.
{{% /notice %}}


In my SQLite database, I have the following tables created:

```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    username TEXT NOT NULL,
    password TEXT NOT NULL,
    created_at INTEGER
)

CREATE TABLE parameters (
    user_id INTEGER NOT NULL,
    secret TEXT NOT NULL,
    issuer TEXT NOT NULL,
    account TEXT NOT NULL,
	FOREIGN KEY (user_id) REFERENCES users(id)
		ON DELETE CASCADE ON UPDATE CASCADE,
	PRIMARY KEY (user_id, secret)
)
```

After populated some data into these tables, I got:
```shell
db.sqlite> SELECT * FROM users;
+----+-------------+--------------+------------+
| id | username    | password     | created_at |
+----+-------------+--------------+------------+
| 1  | John        | fE7aG1nX4cT3 | 1665933898 |
+----+-------------+--------------+------------+
1 row in set
Time: 0.025s


db.sqlite> SELECT * FROM parameters;
+---------+--------------------+--------+-----------------+
| user_id | secret             | issuer | account         |
+---------+--------------------+--------+-----------------+
| 1       | 3tffbcdf8523cb5c29 | Google | user@google.com |
+---------+--------------------+--------+-----------------+
1 row in set
Time: 0.015s
```
To delete all the rows in these two tables, we could execute these statements:

```sql
DELETE FROM users;
DELETE FROM parameters;
```

The second statement is actually redundant in this case since deleting rows in `users` table
will also clean up `parameters` table since `parameters` table has a foreign key `user_id` that is referenced to `users.id`.

So I skipped the second statement and here is what happened:

```shell
db.sqlite> DELETE FROM users;
You are about to run a destructive command.
Do you want to proceed? (y/n): y
Your call!
Query OK, 1 row affected
Time: 0.003s

db.sqlite> select * from parameters;
+---------+--------------------+--------+-----------------+
| user_id | secret             | issuer | account         |
+---------+--------------------+--------+-----------------+
| 1       | 3tffbcdf8523cb5c29 | Google | user@google.com |
+---------+--------------------+--------+-----------------+
1 row in set
Time: 0.014s
```

Why is that? `parameters` table is supposed to be empty. Which means there might be something wrong in the foreign key.
After some researches on internet, I can confirm that SQLite does support [foreign key constraint](https://www.sqlite.org/foreignkeys.html#fk_enable).

But, it's disabled by default according to the docs:
> Foreign key constraints are disabled by default (for backwards compatibility), so **must be enabled separately for each database connection**. (Note, however, that future releases of SQLite might change so that foreign key constraints enabled by default. Careful developers will not make any assumptions about whether or not foreign keys are enabled by default but will instead enable or disable them as necessary.) 

To check whether it's enabled, we can use `PRAGMA foreign_keys` to find out:
```shell
db.sqlite> PRAGMA foreign_keys;
+--------------+
| foreign_keys |
+--------------+
| 0            |
+--------------+
1 row in set
Time: 0.014s
```

So, here we go, we know who the culprit is. Now just execute `PRAGMA foreign_keys = ON` to enable it.
```shell
db.sqlite> PRAGMA foreign_keys = ON;
Query OK, 0 rows affected
Time: 0.001s
```

Try `PRAGMA foreign_keys` again to confirm it's enabled:
```shell
db.sqlite> PRAGMA foreign_keys;
+--------------+
| foreign_keys |
+--------------+
| 1            |
+--------------+
1 row in set
Time: 0.013s
```

Now the foreign key constraints cascade functionality should work as expected.

But one thing to notice, the documentation mentions that it **must be enabled for each database connection**.
Which means this setting is not persistent and we need to enable it every time we connect to SQLite database.
All of the pragma statements in SQLite are non-persistent, except [WAL journaling mode](https://www.sqlite.org/pragma.html#pragma_journal_mode).
