# Contributing to skegdb projects

Thanks for thinking about contributing. This file is the organization
default and applies to every public repo under
[`skegdb`](https://github.com/skegdb) that does not ship its own
`CONTRIBUTING.md`.

## How to contribute

1. **Open an issue first** for anything bigger than a typo or a
   one-line fix. Describe what you want to change and why; we'll align
   on the approach before code review.
2. **Fork** the relevant repo and create a topic branch.
3. **Run the local checks** before opening a PR:
   - Rust repos: `cargo fmt --all -- --check`, `cargo clippy
     --workspace --all-targets -- -D warnings`, `cargo test --workspace`.
   - Python repos: `ruff check .`, `pytest`.
4. **One change per PR.** If you fix two unrelated things, open two
   PRs. Reviewers can land them faster.
5. **Sign your commits** (`git commit -S`). Org rulesets require it on
   protected branches.
6. **Write commit messages that explain the why**, not the what. The
   diff already shows the what.

## Code style

Conventions vary per language; the project's `rustfmt.toml`,
`.editorconfig`, and `pyproject.toml` are the source of truth. CI will
fail on style violations.

Avoid em-dash (`—`) in source code comments. Use a normal hyphen or
restructure the sentence. (Project-wide preference.)

## Tests

Add a test for any behaviour you change. Integration tests live under
`tests/` (Rust) or `tests/` (Python). Bench changes go through
`skegdb/skeg-testing`.

## Filing a bug

Use the GitHub issue tracker on the relevant repo. Include:

- skeg version (`skeg --version`)
- Operating system and architecture
- Steps to reproduce
- Expected vs actual behaviour

## Security issues

Do **not** open a public issue. See `SECURITY.md`.

## License

By contributing you agree your work is licensed under
[Apache-2.0](LICENSE-APACHE) (the project license), unless the
specific repo states otherwise.
