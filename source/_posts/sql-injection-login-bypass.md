---
title: 'Writeup: Bypassing a Login Form with SQL Injection'
date: 2026-11-10 12:00:00
categories:
  - CTF Writeups
tags:
  - Web Security
  - SQL Injection
description: How a classic admin' -- payload bypasses a naive login query, and how to fix it with parameterized queries.
---

> This is a sample post to show the layout. Replace it with your own writeups!

## The challenge

The lab has a login form that builds its SQL query by gluing strings together:

```python
query = f"SELECT * FROM users WHERE username = '{username}' AND password = '{password}'"
```

<!-- more -->

## The payload

Entering this as the username:

```text
admin' --
```

turns the query into:

```sql
SELECT * FROM users WHERE username = 'admin' --' AND password = '...'
```

Everything after `--` is a comment, so the password check disappears.

## The fix

Use parameterized queries so user input is never treated as SQL:

```python
cur.execute(
    "SELECT * FROM users WHERE username = ? AND password_hash = ?",
    (username, password_hash),
)
```

## Takeaways

1. Never build SQL with string formatting.
2. Use an ORM or placeholders (`?` / `%s`).
3. Store password **hashes**, not plaintext.
