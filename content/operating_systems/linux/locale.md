---
title: Locale (Linux) 
tags:
- linux
- locale
---

This page provides information about locales in Linux.
<!--more-->

## LC_COLLATE

`LC_COLLATE` is a locale category variable used in Unix-like operating systems and database systems like PostgreSQL to 
define the sort order (collation) of strings. 
It determines how characters are compared, affecting the behavior of `ORDER BY` clauses and the integrity of text-based indexes.

Commonly used options for `LC_COLLATE` include:

* **C / POSIX** - The simplest and fastest option. It sorts strings byte-by-byte based on their raw numerical values (ASCII order). 
                In this mode, uppercase letters (A-Z) typically come before lowercase letters (a-z).
* **Language-Specific Locales** - Used for culturally correct sorting in specific languages. Examples include:
  * `en_US.UTF-8`: Standard English (United States) sorting using UTF-8 encoding.
  * `fr_FR.UTF-8`: French sorting rules (e.g., handling accented characters like 'é').
  * `de_DE.UTF-8`: German sorting rules.
* C.UTF-8 - A more modern variant that provides the "C" sort order (byte-by-byte) but allows for UTF-8 character handling, 
   which is useful for maintaining performance while supporting multi-byte characters.

### Key Usage Considerations

* Immutability in Databases - In PostgreSQL, the `LC_COLLATE` setting is established when a database is created and cannot be changed without recreating the database.
* Environment Variables - In a shell, you can check your current settings by running the locale command or list all available options on your system with `locale -a`.
* Precedence - When determining collation, the system typically looks at environment variables in this order: `LC_ALL`, `LC_COLLATE`, and then `LANG`.

## LC_CTYPE

`LC_CTYPE` is a locale environment variable in Unix-like systems that defines character classification, case conversion, 
and character attributes (e.g., determining what constitutes a letter, digit, whitespace, or upper/lower case).

Common `LC_CTYPE` Options include:

* `en_US.UTF-8` - The standard setting for US English, using Unicode (UTF-8) character encoding.
* `C` or `POSIX` - The default, minimalist locale. Character classification is limited to basic ASCII.
* `en_GB.UTF-8` - English for Great Britain (UTF-8).
* `de_DE.UTF-8` - German for Germany (UTF-8).
* `ja_JP.UTF-8` - Japanese for Japan (UTF-8).

