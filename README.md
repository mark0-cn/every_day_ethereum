# Every Day Ethereum `v0.1.0`

**[中文文档](README-zh.md)**

Claude Code slash commands for Ethereum EIP developers. Run `/get-info-zh` or `/get-info-en` to generate a daily digest of EIP activity, spec changes, toolchain updates, and community discussions — aggregated from 19 sources.

## Commands

| Command | Output |
|---------|--------|
| `/get-info-zh` | Chinese daily digest |
| `/get-info-en` | English daily digest |

## Installation

### Via Claude Plugin Marketplace (recommended)

In Claude Code, run:
```
/plugin marketplace add mark0-cn/every_day_ethereum
/plugin install every-day-ethereum@mark0-cn
```

### Manual

**Project-level** (current project only):
```bash
mkdir -p .claude/commands
cp commands/get-info-zh.md .claude/commands/
cp commands/get-info-en.md .claude/commands/
```

**Global** (all projects):
```bash
cp commands/get-info-zh.md ~/.claude/commands/
cp commands/get-info-en.md ~/.claude/commands/
```

Then restart Claude Code and type `/get-info-zh` or `/get-info-en`.

## Data Sources

Aggregates from 19 sources across 4 categories:

**EIP & Specs**
- [ethereum/EIPs](https://github.com/ethereum/EIPs) — PR & Issue activity
- [ethereum/execution-specs](https://github.com/ethereum/execution-specs) — execution layer spec changes
- [ethereum/consensus-specs](https://github.com/ethereum/consensus-specs) — consensus layer spec changes
- [ethereum/execution-apis](https://github.com/ethereum/execution-apis) — Engine API / JSON-RPC spec changes
- [ethereum/devp2p](https://github.com/ethereum/devp2p) — networking protocol changes
- [ethereum/pm](https://github.com/ethereum/pm) — AllCoreDevs meeting notes

**Toolchain**
- [bluealloy/revm](https://github.com/bluealloy/revm) — EVM implementation in Rust
- [paradigmxyz/reth](https://github.com/paradigmxyz/reth) — Ethereum execution client in Rust
- [alloy-rs/alloy](https://github.com/alloy-rs/alloy) — Rust Ethereum libraries
- [foundry-rs/foundry](https://github.com/foundry-rs/foundry) — Ethereum development framework

**Community & Research**
- [Ethereum Magicians](https://ethereum-magicians.org/) — EIP discussions
- [ethresear.ch](https://ethresear.ch/) — research posts
- [Forkcast](https://forkcast.org/) — AllCoreDevs calls & decisions
- [Ethereum Cat Herders](https://www.ethereumcatherders.com/) — EIP process coordination

**Official & Ecosystem**
- [Ethereum Blog](https://blog.ethereum.org/) — official announcements
- [EthPandaOps](https://ethpandaops.io/) — client testing & devops
- [Vitalik's Blog](https://vitalik.eth.limo/) — research & essays
- [Week in Ethereum News](https://weekinethereumnews.com/) — weekly digest
- [L2Beat](https://l2beat.com/) — L2 technical tracking

## Changelog

### v0.1.0 — 2026-05-16
- Initial release
- `/get-info-zh` and `/get-info-en` commands
- 19 data sources: EIPs, execution-specs, consensus-specs, execution-apis, devp2p, revm, reth, alloy, foundry, Ethereum Magicians, ethresear.ch, Forkcast, Ethereum Blog, EthPandaOps, Vitalik's Blog, Ethereum Cat Herders, Week in Ethereum News, L2Beat, AllCoreDevs meeting notes

## Optional: GitHub Token

Without a token, GitHub API allows 60 requests/hour. Set a personal access token to raise the limit to 5000/hour:

```bash
export GITHUB_TOKEN=your_token_here
```
