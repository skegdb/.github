# skegdb/.github

Organization-wide defaults for the [skegdb](https://github.com/skegdb)
GitHub org.

## What lives here

| path | purpose |
|---|---|
| `profile/README.md` | shown on the org landing page (github.com/skegdb) |
| `.github/workflows/rust-ci.yml` | reusable Rust CI (build + test + clippy + fmt) |
| `.github/workflows/python-ci.yml` | reusable Python CI (ruff + pytest) |
| `.github/workflows/gleam-ci.yml` | reusable Gleam CI |
| `.github/workflows/release-cargo.yml` | reusable crates.io publisher |
| `.github/workflows/release-pypi.yml` | reusable PyPI publisher |
| `CONTRIBUTING.md` | org-default contributor guide |
| `SECURITY.md` | org-default security policy |
| `CODE_OF_CONDUCT.md` | org-default code of conduct |

Repos under `skegdb/*` inherit `CONTRIBUTING.md`, `SECURITY.md`, and
`CODE_OF_CONDUCT.md` from this repo unless they ship their own.

## Calling a reusable workflow

```yaml
# .github/workflows/ci.yml in a consumer repo
name: CI
on: [push, pull_request]
jobs:
  ci:
    uses: skegdb/.github/.github/workflows/rust-ci.yml@main
    with:
      workspace: true
      msrv: "1.86"
```

For releases, the consumer passes its crates.io / PyPI secret in
through `secrets: inherit` or explicitly:

```yaml
jobs:
  publish:
    uses: skegdb/.github/.github/workflows/release-cargo.yml@main
    secrets:
      CARGO_REGISTRY_TOKEN: ${{ secrets.CARGO_REGISTRY_TOKEN }}
```

## Versioning

Currently `@main`. Switch to `@v1` once the workflows stabilise; bump
to `@v2` for breaking changes.
