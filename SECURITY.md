# Security Policy

## Supported versions

Only the latest version on the `main` branch is supported. Fixes are not backported.

## What counts as a security issue

These skills are Markdown instructions that AI coding agents load into their context. A security issue here is anything that could make an agent act unsafely, for example:

- instructions that could lead an agent to run harmful commands or leak secrets
- content that could be used for prompt injection
- setup commands in the README that could overwrite or delete a user's files

## Reporting a vulnerability

Please don't open a public issue for security problems.

Report it privately through [GitHub's vulnerability reporting](https://github.com/lennonpetrick/android-engineering-skills/security/advisories/new) instead. Include what you found, which skill or file it affects, and how to reproduce it.

You can expect a first reply within 7 days. If the report is confirmed, I'll fix it on `main` and credit you in the advisory, unless you'd rather stay anonymous.
