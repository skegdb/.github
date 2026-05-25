# Security policy

## Supported versions

| version | supported |
|---------|-----------|
| 0.1.x   | yes       |
| < 0.1   | no        |

`0.1.x` is the current supported line. Older pre-release tags are not
patched.

## Reporting a vulnerability

**Do not open a public issue with the exploit details.** Instead, open
a "Security" issue in the affected repo with a brief, non-actionable
description ("possible isolation issue in RESP3 handler, reach out for
details") and we will move the conversation to a private channel.

> **Note:** a dedicated `security@` mailbox will be activated in the
> coming days. Until then, the GitHub issue tracker is the only entry
> point. Maintainers monitor it.

Include in the private follow-up:

- A description of the vulnerability and its potential impact.
- Steps to reproduce. Proof-of-concept code is welcome but not
  required.
- The affected version(s).
- Any suggested mitigation.

We will acknowledge receipt within 72 hours and aim to ship a fix
within 14 days for high-severity issues. Coordinated disclosure: we
will agree on a public disclosure date with you before announcing.

## Scope

In scope:

- Code in any `skegdb/skeg*` public repository.
- Wire protocol attacks (RESP3, binary).
- Multi-tenant isolation bypass.
- Cryptographic flaws in `skeg-tenant` (auth, token store).
- Disk-format corruption leading to RCE / DoS.

Out of scope:

- Issues already reported in the public tracker.
- Findings that require physical access to the disk.
- Theoretical timing attacks against `cargo install`.
- Bugs in third-party dependencies (report upstream).

## Hall of fame

Contributors who have responsibly disclosed security issues are listed
in the release notes.
