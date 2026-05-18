# Every Day Ethereum `v0.3.0`

**[English](README.md)**

面向以太坊 EIP 开发者的 Claude Code slash command 集合。运行 `/get-info-zh` 或 `/get-info-en`，自动聚合 19 个数据源，生成过去 24 小时的 EIP 动态、规范变更、工具链更新与社区讨论日报。

## 命令列表

| 命令 | 输出 |
|------|------|
| `/get-info-zh` | 中文日报 |
| `/get-info-en` | 英文日报 |

## 安装方式

### 插件市场安装（推荐）

在 Claude Code 中依次运行：
```
/plugin marketplace add mark0-cn/every_day_ethereum
/plugin install every-day-ethereum@mark0-cn
```

### 更新

```
/plugin update every-day-ethereum@mark0-cn
```

如果上述命令不支持，重新 add 一次即可拉取最新版本：

```
/plugin marketplace add mark0-cn/every_day_ethereum
```

### 手动安装

**项目级**（仅在当前项目中可用）：
```bash
mkdir -p .claude/commands
cp commands/get-info-zh.md .claude/commands/
cp commands/get-info-en.md .claude/commands/
```

**全局**（在所有项目中可用）：
```bash
cp commands/get-info-zh.md ~/.claude/commands/
cp commands/get-info-en.md ~/.claude/commands/
```

重启 Claude Code 后，直接输入 `/get-info-zh` 或 `/get-info-en` 即可使用。

## 数据来源

共聚合 19 个数据源，分为 4 类：

**EIP & 规范**
- [ethereum/EIPs](https://github.com/ethereum/EIPs) — PR 与 Issue 动态
- [ethereum/execution-specs](https://github.com/ethereum/execution-specs) — 执行层规范变更
- [ethereum/consensus-specs](https://github.com/ethereum/consensus-specs) — 共识层规范变更
- [ethereum/execution-apis](https://github.com/ethereum/execution-apis) — Engine API / JSON-RPC 规范变更
- [ethereum/devp2p](https://github.com/ethereum/devp2p) — 网络层协议变更
- [ethereum/pm](https://github.com/ethereum/pm) — AllCoreDevs 会议纪要

**工具链**
- [bluealloy/revm](https://github.com/bluealloy/revm) — Rust 实现的 EVM
- [paradigmxyz/reth](https://github.com/paradigmxyz/reth) — Rust 实现的以太坊执行层客户端
- [alloy-rs/alloy](https://github.com/alloy-rs/alloy) — Rust 以太坊基础库
- [foundry-rs/foundry](https://github.com/foundry-rs/foundry) — 以太坊开发框架

**社区 & 研究**
- [Ethereum Magicians](https://ethereum-magicians.org/) — EIP 讨论论坛
- [ethresear.ch](https://ethresear.ch/) — 以太坊研究论坛
- [Forkcast](https://forkcast.org/) — AllCoreDevs 会议录像与决策记录
- [Ethereum Cat Herders](https://www.ethereumcatherders.com/) — EIP 流程协调组织

**官方 & 生态**
- [Ethereum Blog](https://blog.ethereum.org/) — 以太坊官方博客
- [EthPandaOps](https://ethpandaops.io/) — 客户端测试与 DevOps 团队
- [Vitalik's Blog](https://vitalik.eth.limo/) — Vitalik 个人博客
- [Week in Ethereum News](https://weekinethereumnews.com/) — 以太坊生态周报
- [L2Beat](https://l2beat.com/) — L2 技术跟踪

## 版本变动

### v0.3.0 — 2026-05-18
- 支持 `gh` CLI 作为认证方式（优先级高于 GITHUB_TOKEN）
- 认证检测顺序：gh CLI → GITHUB_TOKEN → 提示用户输入

### v0.2.0 — 2026-05-16
- 新增启动时 GitHub Token 检查，未配置时提供获取引导和交互式输入
- 修复 Ethereum Blog RSS 地址（`/en/rss.xml` → `/en/feed.xml`）
- 修复 Ethereum Cat Herders 域名（`ethereumcatherders.com` → `ethcatherders.com`）
- Week in Ethereum News 新增 Substack 备用地址（绕过 TLS 证书问题）
- README 新增更新方式说明

### v0.1.0 — 2026-05-16
- 首次发布
- 新增 `/get-info-zh` 和 `/get-info-en` 命令
- 接入 19 个数据源：EIPs、execution-specs、consensus-specs、execution-apis、devp2p、revm、reth、alloy、foundry、Ethereum Magicians、ethresear.ch、Forkcast、Ethereum Blog、EthPandaOps、Vitalik 博客、Ethereum Cat Herders、Week in Ethereum News、L2Beat、AllCoreDevs 会议纪要

## 可选：配置 GitHub Token

不配置 Token 时，GitHub API 限额为 60 次/小时；配置后提升至 5000 次/小时：

```bash
export GITHUB_TOKEN=your_token_here
```
