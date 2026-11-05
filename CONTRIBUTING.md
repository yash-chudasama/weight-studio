# Contributing to Weight Studio

Thanks for helping. Issues and pull requests are welcome.

## Before you open a pull request

```sh
cargo test
cargo clippy --all-targets
cargo fmt --check
```

Keep tests in each crate's `tests/` folder. See the [README](README.md#project-structure) for where new work belongs.

## Licensing your contribution

By submitting a contribution, you confirm that:

- You wrote it, or otherwise have the right to submit it
- It may be distributed under the project's [MIT License](LICENSE)
- It does not include code, model weights or data copied from a source whose license is incompatible with MIT
- Any third-party material it includes is clearly identified, with its license

Do not add model weights or benchmark data to this repository.

Please read [LEGAL.md](LEGAL.md). Contributions that mainly serve the uses it rules out may be declined.
