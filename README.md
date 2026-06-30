# Rust CLI Template

> A production-minded Rust CLI starter with clap, config files, completions,
> CI guardrails, release automation, and agent-ready contributor docs.

[![GitHub stars](https://shieldcn.dev/github/stars/christopher-kapic/rust-cli-template.svg?variant=branded&mode=light&size=sm)](https://github.com/christopher-kapic/rust-cli-template/stargazers)
[![GitHub forks](https://shieldcn.dev/github/forks/christopher-kapic/rust-cli-template.svg?variant=branded&mode=light&size=sm)](https://github.com/christopher-kapic/rust-cli-template/forks)
[![Rust 1.85+](https://shieldcn.dev/badge/rust-1.85+-ef7d00.svg?variant=branded&mode=light&size=sm&logo=rust)](Cargo.toml)
[![License](https://shieldcn.dev/badge/license-MIT_Apache-22c55e.svg?variant=branded&mode=light&size=sm)](#license)
[![X](https://shieldcn.dev/x/follow/kapicode.svg?variant=branded&mode=light&size=sm)](https://x.com/kapicode)

This is a template repository for building and shipping polished Rust command
line tools. It starts green, stays small, and includes the boring release and
quality machinery you usually have to assemble by hand.

## Start Here

Create a new repository from this template, clone it, then initialize the
placeholder project metadata:

```sh
cd rust-cli-template

# Preview the rewrite without touching files.
cargo xtask init --name zap --owner your-github-username --dry-run

# Rename the crate/binary and fill in public metadata.
cargo xtask init --name zap --owner your-github-username \
  --author "Ada Lovelace <ada@example.com>" \
  --description "Zaps things quickly."

# Refresh generated agent docs and run the same gates as CI.
cargo xtask sync-docs
cargo fmt --all --check
cargo clippy --workspace --all-targets --all-features -- -D warnings
cargo test --workspace --all-targets --locked
cargo test --workspace --doc --locked
cargo xtask sync-docs --check
```

The template intentionally ships as `mycli`. After `cargo xtask init`, that name
and the `OWNER` placeholder are replaced across the text files that should move
with your new project.

## What You Get

- A small clap-based CLI with `hello`, `config`, and `completions` commands.
- Clean stdout/stderr boundaries, JSON output support, structured logging, and
  stable process exit codes.
- Cross-platform config paths and TOML config load/save helpers.
- Focused black-box CLI tests plus room for unit tests next to pure logic.
- CI for fmt, clippy, tests, docs drift, dependency policy, typos, MSRV, and
  lightweight repository policy checks.
- Cross-platform releases through `dist`, including shell, PowerShell, and
  Homebrew installer artifacts.
- One canonical `AGENTS.md` for humans and AI coding agents, with generated
  mirrors checked for drift.

## Included CLI

The default commands are deliberately simple, but complete enough to demonstrate
the project conventions:

```sh
mycli hello                 # Hello, world!
mycli hello Ada             # Hello, Ada!
mycli hello Ada --json      # {"name":"Ada","message":"Hello, Ada!"}
mycli config path           # where the config file lives
mycli config --json show    # {"greeting_name":"world"}
mycli config init           # write a default config
mycli completions zsh       # shell completions
```

Configuration is stored in a TOML file. `mycli config path` prints the resolved
path for the current platform. Logs go to stderr; pass `-v`/`-vv` for more,
`--quiet` for less, or set `MYCLI_LOG`.

### Exit Codes

| Code | Meaning |
| ---- | ------- |
| 0 | success |
| 1 | runtime error |
| 2 | usage error |
| 3 | not found |

## Install After Release

These commands are for projects created from the template. They work after your
first public GitHub release.

**Shell:**

```sh
curl --proto '=https' --tlsv1.2 -LsSf https://github.com/OWNER/mycli/releases/latest/download/mycli-installer.sh | sh
```

**PowerShell:**

```powershell
irm https://github.com/OWNER/mycli/releases/latest/download/mycli-installer.ps1 | iex
```

**Homebrew:**

```sh
brew install OWNER/tap/mycli
```

**From source:**

```sh
cargo install --git https://github.com/OWNER/mycli
```

## Development

```sh
cargo build
cargo test --workspace --all-targets --locked
cargo clippy --workspace --all-targets --all-features -- -D warnings
cargo fmt
cargo run -- hello Ada
```

Before considering a change done, run the full local gate:

```sh
cargo fmt --all --check
cargo clippy --workspace --all-targets --all-features -- -D warnings
cargo test --workspace --all-targets --locked
cargo test --workspace --doc --locked
cargo xtask sync-docs --check
```

See [AGENTS.md](AGENTS.md) for repository conventions and
[CONTRIBUTING.md](CONTRIBUTING.md) for the PR checklist.

## Guides

The default dependency set is intentionally small. Add capabilities only when
you need them, following the focused guides in `docs/`:

- [Adding async with tokio](docs/adding-tokio.md)
- [Adding local storage with SQLite](docs/adding-sqlite.md)
- [Adding a TUI with ratatui](docs/adding-ratatui.md)
- [Error handling](docs/error-handling.md)
- [Releasing](docs/releasing.md)

## Project Layout

```text
src/
  lib.rs         shared implementation modules
  main.rs        entry: parse -> log -> dispatch -> exit code
  cli.rs         clap argument definitions
  commands/      one file per subcommand
  config.rs      TOML config load/save
  paths.rs       cross-platform config/data dirs
  logging.rs     tracing setup; logs go to stderr
  exit.rs        stable exit codes
tests/cli.rs     black-box CLI tests
xtask/           project automation: init, sync-docs
docs/            optional capability and release guides
tap/             notes for publishing a Homebrew tap
```

## Template Maintenance

`AGENTS.md` is the source of truth for contributor and agent instructions.
`CLAUDE.md`, `.cursorrules`, and `.github/copilot-instructions.md` are generated
mirrors. Edit `AGENTS.md`, then run:

```sh
cargo xtask sync-docs
```

Do not hand-edit generated mirrors or `.github/workflows/release.yml`.

## License

Licensed under either of [Apache License 2.0](LICENSE-APACHE) or
[MIT license](LICENSE-MIT) at your option.
