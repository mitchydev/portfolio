+++
draft = false
title = 'Medium Scale Abstraction'
status = 'pending'
+++

> Choose a part of the project that you (co-)wrote that employs medium-scale abstraction.
>
> - What functionality does your code sample abstract away?
> - How does it mechanical achieve abstraction?
> - What purpose does this abstraction serve in your larger program?

## Medium Scale Abstraction Sample

### Location

`backend/src/passwords.py` lines 59-69

### My contributions

### Generative AI use

Claude Code developed the code with me using open source libraries.

### Competency Demonstration

`passwords.py` hides the whole password-handling process behind two functions. A website must never store passwords as plain text because anyone who got the database file could read them all. Instead it stores a hash (the scrambled one-way version of the password that cannot be turned back into the original).

Argon2id is the hashing algorithm I chose:

- It is deliberately slow and memory-hungry, which makes guessing millions of passwords expensive for an attacker.
- Each hash also includes a “salt,” a random value mixed in so that two users with the same password get different hashes. The salt is stored inside the hash string, so no other code has to keep track of it.

All of this comes from the `argon2-cffi` library, which we use through its `PasswordHasher` class with the library’s default settings. The rest of the app never sees any of it, and only calls `hash_password(password)` at sign-up and `check_password(stored_hash, password)` at login.

The second thing the abstraction hides is how the library reports a wrong password. `argon2-cffi` doesn’t return false. It raises an exception called `VerificationError`, so every caller would otherwise need its own `try/except`. This function catches it once and turns it into a plain true or false.

```python
def check_password(stored_hash, password):
    """Return True if password matches stored_hash, otherwise False."""
    try:
        return hasher.verify(stored_hash, password)
    except VerificationError:  # the library raises this for a wrong password
        return False
```

This matters because `app.py` only calls these functions in `register()` and `login()`, so switching to another hashing library would only change `passwords.py` and `requirements.txt`. The database works the same way. Moving from SQLite would only need a new URL and a driver.

The limit is that this only covers my tables. The room code calls `sqlite3` directly, so it isn’t abstracted yet. This is known issue #20.
