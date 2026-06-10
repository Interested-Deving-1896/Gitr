[update-readmes]   Mode: rewrite — migrating to template structure...
# Gitr

[![Built with Ona](https://ona.com/build-with-ona.svg)](https://app.ona.com/#https://github.com/Interested-Deving-1896/Gitr)

<!-- AI:start:what-it-does -->
This project provides a tool for managing and synchronizing multiple Git repositories and forks across various Git hosting platforms, including GitHub, GitLab, and Gitea. It automates the process of pulling updates and pushing changes, streamlining workflows for developers and teams working with large collections of repositories.
<!-- AI:end:what-it-does -->

## Architecture

<!-- AI:start:architecture -->
The architecture consists of modular crates, each responsible for specific functionality. These crates interact through shared dependencies and a workspace configuration defined in `Cargo.toml`. The key components are:

- `gitr-core`: Implements core logic for repository management.
- `gitr-auth`: Handles authentication across supported Git hosts.
- `gitr-db`: Manages local database storage for repository metadata.
- `gitr-host`: Abstracts interactions with different Git hosting services.
- `gitr-discover`: Discovers repositories across local and remote sources.
- `gitr-sync`: Executes pull/push operations across repositories.
- `gitr-cli`: Provides the command-line interface for user interaction.

The crates use shared libraries such as `serde` for serialization, `tokio` for asynchronous operations, and `rusqlite` for database access. The directory structure is organized as follows:

```plaintext
.
├── Cargo.lock
├── Cargo.toml
├── LICENSE
├── README.md
├── .gitignore
└── crates
    ├── gitr-core
    ├── gitr-auth
    ├── gitr-db
    ├── gitr-host
    ├── gitr-discover
    ├── gitr-sync
    └── gitr-cli
```

Each crate is self-contained and contributes to the overall functionality of the tool. The workspace configuration ensures consistent dependency management and builds.
<!-- AI:end:architecture -->

## Install

<!-- Add installation instructions here. This section is yours — the AI will not modify it. -->

```bash
git clone https://github.com/Interested-Deving-1896/Gitr.git
cd Gitr
```

## Usage


### Configuration

```bash
gitr config init    # creates ~/.gitr/ with config.toml + gitr.db
gitr config show    # print current config
```

Config lives at `~/.gitr/config.toml`:

```toml
default_merge_strategy = "ff"
sync_concurrency = 8
scan_paths = []
max_scan_depth = 4
```

### Host Management

```bash
gitr host add <label> --provider github --user <username>   # prompts for token
gitr host add gh --provider github --user alice --token ghp_...
gitr host list              # table of registered hosts
gitr host info <label>      # details + repo counts
gitr host verify <label>    # test credentials + show rate limit
gitr host remove <label>    # delete host + credentials
```

Tokens are stored in the OS keychain (Windows Credential Manager / macOS Keychain / Linux Secret Service) and never touch disk in plaintext.

### Scanning & Discovery

```bash
gitr scan --host gh              # discover repos via GitHub API
gitr scan --path ~/projects      # also scan local filesystem
gitr scan                        # scan all hosts + configured paths
```

Scan reconciles local repos with remote APIs:
- **Matched** — found both locally and on the host
- **Local-only** — on disk but not on any registered host
- **Remote-only** — on the host but not cloned locally

Discovered repos are automatically tracked in the local database.

### Repo Management

```bash
gitr repo list                   # all tracked repos
gitr repo list --forks           # only forks
gitr repo list --host gh         # filter by host
gitr repo info <name>            # full details + branch status
```

### Syncing

```bash
gitr sync <repo>                 # sync a single fork (fast-forward by default)
gitr sync <repo> --strategy merge      # merge instead of ff
gitr sync <repo> --strategy rebase     # rebase instead of ff
gitr sync <repo> --dry-run             # show behind count without changing anything
gitr sync all                    # sync all tracked forks in parallel
gitr sync all --dry-run          # preview all
```

**Fork sync flow:**
1. Clone if not already local
2. Add `upstream` remote pointing to parent repo
3. `git fetch upstream --prune`
4. Checkout default branch
5. Apply strategy (fast-forward / merge / rebase)
6. `git push origin`
7. Record result in database

Parallel sync uses a configurable concurrency limit (default 8).

### Status Dashboard

```bash
gitr status                      # all hosts
gitr status --host gh            # single host
```

```
HOST / REPO              BRANCH   BEHIND  AHEAD  STRATEGY  LAST SYNC  STATUS
gh (github)
  linux                  main        3       0   ff        12:00      behind
  rust                   main        0       0   ff        12:00      synced
  react                  main        0       2   ff        11:45      ahead

Summary: 298 synced | 35 behind | 2 ahead | 0 errors
```

### Sync History

```bash
gitr history                     # recent sync operations
gitr history <repo>              # history for a specific repo
gitr history --limit 50          # show more records
```

## Configuration

<!-- Document configuration options here. This section is yours — the AI will not modify it. -->

## CI

<!-- AI:start:ci -->
- **`ci.yml`**: Runs on every push and pull request. It checks formatting using `cargo fmt`, runs linter checks with `cargo clippy`, and executes tests across all workspace crates using `cargo test`. No secrets are required.

- **`release.yml`**: Triggers on new Git tags. Builds release binaries for Linux, macOS, and Windows using `cargo build --release`. Uploads the artifacts to the GitHub release. No secrets are required.

- **`audit.yml`**: Runs on a weekly schedule. Executes `cargo audit` to check for vulnerabilities in dependencies. No secrets are required.
<!-- AI:end:ci -->

## Mirror chain

<!-- AI:start:mirror-chain -->
This repo is maintained in [`Interested-Deving-1896/Gitr`](https://github.com/Interested-Deving-1896/Gitr) and mirrored through:

```
Interested-Deving-1896/Gitr  ──►  OpenOS-Project-OSP/Gitr  ──►  OpenOS-Project-Ecosystem-OOC/Gitr
```

Changes flow downstream automatically via the hourly mirror chain in
[`fork-sync-all`](https://github.com/Interested-Deving-1896/fork-sync-all).
Direct commits to OSP or OOC are detected and opened as PRs back to `Interested-Deving-1896`.
<!-- AI:end:mirror-chain -->

## Contributors

<!-- AI:start:contributors -->
_Contributors pending._
<!-- AI:end:contributors -->

## Origins

<!-- AI:start:origins -->
_Original project — no upstream fork._
<!-- AI:end:origins -->

## Resources

<!-- AI:start:resources -->
_No additional resource files found._
<!-- AI:end:resources -->

## License

<!-- AI:start:license -->
[GPL-3.0](https://github.com/Interested-Deving-1896/Gitr/blob/main/LICENSE) © 2026 [Interested-Deving-1896](https://github.com/Interested-Deving-1896)
<!-- AI:end:license -->
