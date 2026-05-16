Generate an Ethereum EIP daily digest for the past 24 hours by collecting data from all sources below, then formatting a unified **Chinese** Markdown report.

---

## Step 0 — GitHub Token Check

Before collecting any data, run:

```bash
if [ -z "$GITHUB_TOKEN" ]; then
  echo "NOT_SET"
else
  echo "SET"
fi
```

**If the result is `NOT_SET`**, inform the user in Chinese:

> ⚠️ **未检测到 GITHUB_TOKEN**
>
> 没有 Token 时，GitHub API 限额为 **60 次/小时**，通常不够完成一次完整日报。
>
> **获取方式（免费，1 分钟完成）：**
> 1. 打开 https://github.com/settings/tokens/new
> 2. Note 填写任意名称（如 `every-day-ethereum`）
> 3. Expiration 选择 No expiration 或自定义
> 4. **不需要勾选任何权限**（公开仓库无需额外权限）
> 5. 点击 Generate token，复制生成的 token
>
> **设置方式：**
> ```bash
> export GITHUB_TOKEN=your_token_here
> ```
>
> 是否现在输入 Token？请直接粘贴（或输入 `skip` 跳过，使用匿名限额继续）：

Wait for the user's response:
- If the user pastes a token string (starts with `ghp_` or `github_pat_`), run `export GITHUB_TOKEN=<token>` and confirm "✅ Token 已设置，开始采集数据。"
- If the user types `skip` or anything else, proceed without a token and note that GitHub data may be incomplete.

**If the result is `SET`**, silently continue to data collection.

---

## Data Collection

Run all steps below. If a source is unavailable, note it and continue.

### 1. ethereum/EIPs — PR & Issue activity

```bash
SINCE=$(date -u -v-1d +"%Y-%m-%dT%H:%M:%SZ" 2>/dev/null || date -u -d "yesterday" +"%Y-%m-%dT%H:%M:%SZ")
AUTH=${GITHUB_TOKEN:+-H "Authorization: Bearer $GITHUB_TOKEN"}

echo "=== EIPs PRs ==="
curl -s "https://api.github.com/repos/ethereum/EIPs/pulls?state=all&sort=updated&direction=desc&per_page=30" \
  -H "Accept: application/vnd.github+json" $AUTH

echo "=== EIPs Issues ==="
curl -s "https://api.github.com/repos/ethereum/EIPs/issues?state=all&sort=updated&direction=desc&per_page=30&since=$SINCE" \
  -H "Accept: application/vnd.github+json" $AUTH
```

Filter to items with `updated_at` within the past 24 hours.

### 2. revm — commits, PRs, releases

```bash
SINCE=$(date -u -v-1d +"%Y-%m-%dT%H:%M:%SZ" 2>/dev/null || date -u -d "yesterday" +"%Y-%m-%dT%H:%M:%SZ")
AUTH=${GITHUB_TOKEN:+-H "Authorization: Bearer $GITHUB_TOKEN"}

echo "=== revm commits ==="
curl -s "https://api.github.com/repos/bluealloy/revm/commits?since=$SINCE&per_page=30" \
  -H "Accept: application/vnd.github+json" $AUTH

echo "=== revm PRs ==="
curl -s "https://api.github.com/repos/bluealloy/revm/pulls?state=all&sort=updated&direction=desc&per_page=20" \
  -H "Accept: application/vnd.github+json" $AUTH

echo "=== revm releases ==="
curl -s "https://api.github.com/repos/bluealloy/revm/releases?per_page=5" \
  -H "Accept: application/vnd.github+json" $AUTH
```

For commits, extract: SHA (short), message (first line), author. For releases, check if `published_at` is within 24h.

### 3. reth — commits, PRs, releases

```bash
SINCE=$(date -u -v-1d +"%Y-%m-%dT%H:%M:%SZ" 2>/dev/null || date -u -d "yesterday" +"%Y-%m-%dT%H:%M:%SZ")
AUTH=${GITHUB_TOKEN:+-H "Authorization: Bearer $GITHUB_TOKEN"}

echo "=== reth commits ==="
curl -s "https://api.github.com/repos/paradigmxyz/reth/commits?since=$SINCE&per_page=30" \
  -H "Accept: application/vnd.github+json" $AUTH

echo "=== reth PRs ==="
curl -s "https://api.github.com/repos/paradigmxyz/reth/pulls?state=all&sort=updated&direction=desc&per_page=20" \
  -H "Accept: application/vnd.github+json" $AUTH

echo "=== reth releases ==="
curl -s "https://api.github.com/repos/paradigmxyz/reth/releases?per_page=5" \
  -H "Accept: application/vnd.github+json" $AUTH
```

### 4. ethereum/execution-specs — spec commits & PRs

```bash
SINCE=$(date -u -v-1d +"%Y-%m-%dT%H:%M:%SZ" 2>/dev/null || date -u -d "yesterday" +"%Y-%m-%dT%H:%M:%SZ")
AUTH=${GITHUB_TOKEN:+-H "Authorization: Bearer $GITHUB_TOKEN"}

echo "=== execution-specs commits ==="
curl -s "https://api.github.com/repos/ethereum/execution-specs/commits?since=$SINCE&per_page=20" \
  -H "Accept: application/vnd.github+json" $AUTH

echo "=== execution-specs PRs ==="
curl -s "https://api.github.com/repos/ethereum/execution-specs/pulls?state=all&sort=updated&direction=desc&per_page=20" \
  -H "Accept: application/vnd.github+json" $AUTH
```

Focus on changes to fork spec files (e.g. `src/ethereum/cancun/`, `prague/`, `osaka/`). Note which EIPs are affected.

### 5. ethereum/consensus-specs — spec commits & PRs

```bash
SINCE=$(date -u -v-1d +"%Y-%m-%dT%H:%M:%SZ" 2>/dev/null || date -u -d "yesterday" +"%Y-%m-%dT%H:%M:%SZ")
AUTH=${GITHUB_TOKEN:+-H "Authorization: Bearer $GITHUB_TOKEN"}

echo "=== consensus-specs commits ==="
curl -s "https://api.github.com/repos/ethereum/consensus-specs/commits?since=$SINCE&per_page=20" \
  -H "Accept: application/vnd.github+json" $AUTH

echo "=== consensus-specs PRs ==="
curl -s "https://api.github.com/repos/ethereum/consensus-specs/pulls?state=all&sort=updated&direction=desc&per_page=20" \
  -H "Accept: application/vnd.github+json" $AUTH
```

### 6. ethereum/execution-apis — API spec changes

```bash
SINCE=$(date -u -v-1d +"%Y-%m-%dT%H:%M:%SZ" 2>/dev/null || date -u -d "yesterday" +"%Y-%m-%dT%H:%M:%SZ")
AUTH=${GITHUB_TOKEN:+-H "Authorization: Bearer $GITHUB_TOKEN"}

echo "=== execution-apis commits ==="
curl -s "https://api.github.com/repos/ethereum/execution-apis/commits?since=$SINCE&per_page=20" \
  -H "Accept: application/vnd.github+json" $AUTH

echo "=== execution-apis PRs ==="
curl -s "https://api.github.com/repos/ethereum/execution-apis/pulls?state=all&sort=updated&direction=desc&per_page=20" \
  -H "Accept: application/vnd.github+json" $AUTH
```

Flag any changes to Engine API (`engine_`) or new JSON-RPC methods.

### 7. ethereum/devp2p — networking protocol changes

```bash
SINCE=$(date -u -v-1d +"%Y-%m-%dT%H:%M:%SZ" 2>/dev/null || date -u -d "yesterday" +"%Y-%m-%dT%H:%M:%SZ")
AUTH=${GITHUB_TOKEN:+-H "Authorization: Bearer $GITHUB_TOKEN"}

echo "=== devp2p commits ==="
curl -s "https://api.github.com/repos/ethereum/devp2p/commits?since=$SINCE&per_page=20" \
  -H "Accept: application/vnd.github+json" $AUTH

echo "=== devp2p PRs ==="
curl -s "https://api.github.com/repos/ethereum/devp2p/pulls?state=all&sort=updated&direction=desc&per_page=20" \
  -H "Accept: application/vnd.github+json" $AUTH
```

### 8. alloy-rs/alloy — commits, PRs, releases

```bash
SINCE=$(date -u -v-1d +"%Y-%m-%dT%H:%M:%SZ" 2>/dev/null || date -u -d "yesterday" +"%Y-%m-%dT%H:%M:%SZ")
AUTH=${GITHUB_TOKEN:+-H "Authorization: Bearer $GITHUB_TOKEN"}

echo "=== alloy commits ==="
curl -s "https://api.github.com/repos/alloy-rs/alloy/commits?since=$SINCE&per_page=20" \
  -H "Accept: application/vnd.github+json" $AUTH

echo "=== alloy PRs ==="
curl -s "https://api.github.com/repos/alloy-rs/alloy/pulls?state=all&sort=updated&direction=desc&per_page=20" \
  -H "Accept: application/vnd.github+json" $AUTH

echo "=== alloy releases ==="
curl -s "https://api.github.com/repos/alloy-rs/alloy/releases?per_page=5" \
  -H "Accept: application/vnd.github+json" $AUTH
```

### 9. foundry-rs/foundry — commits, PRs, releases

```bash
SINCE=$(date -u -v-1d +"%Y-%m-%dT%H:%M:%SZ" 2>/dev/null || date -u -d "yesterday" +"%Y-%m-%dT%H:%M:%SZ")
AUTH=${GITHUB_TOKEN:+-H "Authorization: Bearer $GITHUB_TOKEN"}

echo "=== foundry commits ==="
curl -s "https://api.github.com/repos/foundry-rs/foundry/commits?since=$SINCE&per_page=20" \
  -H "Accept: application/vnd.github+json" $AUTH

echo "=== foundry PRs ==="
curl -s "https://api.github.com/repos/foundry-rs/foundry/pulls?state=all&sort=updated&direction=desc&per_page=20" \
  -H "Accept: application/vnd.github+json" $AUTH

echo "=== foundry releases ==="
curl -s "https://api.github.com/repos/foundry-rs/foundry/releases?per_page=5" \
  -H "Accept: application/vnd.github+json" $AUTH
```

### 10. Ethereum Magicians — new & bumped topics

```bash
curl -s "https://ethereum-magicians.org/latest.json?order=created" \
  -H "Accept: application/json"
```

Filter topics where `created_at` or `bumped_at` is within 24h. Include all categories.

### 11. ethresear.ch — new & active posts

```bash
curl -s "https://ethresear.ch/latest.json?order=created" \
  -H "Accept: application/json"
```

Filter topics with `created_at` or `bumped_at` within 24h. Prioritize EIPs, EVM, consensus, and execution layer topics.

### 12. Forkcast — calls and decisions

Use the WebFetch tool to retrieve:
- `https://forkcast.org/calls` — list any calls published or updated in the past 7 days
- `https://forkcast.org/decisions` — list any decisions recorded in the past 7 days

If the page requires JavaScript and returns empty, note it.

### 13. Ethereum Blog

Use the WebFetch tool to retrieve:
- `https://blog.ethereum.org/en/rss.xml`

List any posts published within the past 24 hours. If RSS unavailable, fetch `https://blog.ethereum.org/` directly.

### 14. EthPandaOps

Use the WebFetch tool to retrieve:
- `https://ethpandaops.io/`

Look for blog posts or tooling updates in the past 7 days.

### 15. Vitalik's Blog

Use the WebFetch tool to retrieve:
- `https://vitalik.eth.limo/`

Check for any new posts in the past 7 days. If a new post exists, fetch its content and summarize the key arguments in 2-3 sentences.

### 16. Ethereum Cat Herders

Use the WebFetch tool to retrieve:
- `https://www.ethereumcatherders.com/`

Look for new meeting notes, EIP process updates, or community calls published in the past 7 days.

### 17. Week in Ethereum News

Use the WebFetch tool to retrieve:
- `https://weekinethereumnews.com/`

This is a weekly newsletter. Check if a new issue was published this week and extract the top EIP-relevant headlines.

### 18. L2Beat

Use the WebFetch tool to retrieve:
- `https://l2beat.com/`

Look for updates related to L2 technical upgrades, EIP dependencies, or governance decisions in the past 7 days.

### 19. AllCoreDevs meeting notes (ethereum/pm)

```bash
SINCE=$(date -u -v-1d +"%Y-%m-%dT%H:%M:%SZ" 2>/dev/null || date -u -d "yesterday" +"%Y-%m-%dT%H:%M:%SZ")
AUTH=${GITHUB_TOKEN:+-H "Authorization: Bearer $GITHUB_TOKEN"}

curl -s "https://api.github.com/repos/ethereum/pm/commits?since=$SINCE&per_page=10" \
  -H "Accept: application/vnd.github+json" $AUTH
```

If any commit touches files matching `AllCoreDevs`, `ACDE`, `ACDC`, or `Interop`, fetch the file and summarize key decisions.

---

## Output Format

Produce a single Chinese Markdown document structured as follows:

```
# Ethereum 日报 · {YYYY-MM-DD}

## 概览
| 来源 | 更新数 |
|------|--------|
| EIP PR/Issue | X |
| revm | X commits / X PR |
| reth | X commits / X PR |
| execution-specs | X commits / X PR |
| consensus-specs | X commits / X PR |
| execution-apis | X commits / X PR |
| devp2p | X commits / X PR |
| alloy | X commits / X PR |
| foundry | X commits / X PR |
| Ethereum Magicians | X |
| ethresear.ch | X |
| Forkcast | X |
| Ethereum Blog | X |
| EthPandaOps | X |
| Vitalik Blog | 有/无 |
| Cat Herders | 有/无 |
| Week in Ethereum | 有/无 |
| L2Beat | X |
| AllCoreDevs | 有/无 |

---

## EIP 动态

### 新增 / 重要更新 PR
- **[EIP-XXXX]** 标题 — 作者 | 当前状态
  > 核心变更一句话

### Issue 讨论热点
- **[#编号]** 标题 — 讨论焦点简述

---

## 规范变动

### execution-specs
- **[#编号]** 标题 — 影响的 fork（如 Prague / Osaka）及涉及 EIP

### consensus-specs
- **[#编号]** 标题 — 变更摘要

### execution-apis（Engine API / JSON-RPC）
- **[#编号]** 标题 — 新增或变更的方法名

### devp2p
- **[#编号]** 标题 — 影响的协议（如 discv5 / eth / snap）

---

## 工具链

### revm
#### 新版本
（如有）vX.X.X — 发布时间 — 主要变更
#### 合并 PR / 关键 Commits
- **[#编号]** 标题 — 影响模块（interpreter / primitives / precompile）

### reth
#### 新版本
（如有）vX.X.X — 发布时间 — 主要变更
#### 合并 PR / 关键 Commits
- **[#编号]** 标题 — 影响模块（engine / network / txpool / rpc）

### alloy
#### 新版本
（如有）vX.X.X — 发布时间 — 主要变更
#### 合并 PR / 关键 Commits
- **[#编号]** 标题 — 影响模块

### foundry
#### 新版本
（如有）vX.X.X — 发布时间 — 主要变更
#### 合并 PR / 关键 Commits
- **[#编号]** 标题 — 影响模块（forge / cast / anvil / chisel）

---

## 社区研究讨论

### Ethereum Magicians
- **[标题](链接)** — 核心议题，参与人数/回复数

### ethresear.ch
- **[标题](链接)** — 核心议题，参与人数/回复数

---

## Forkcast
### 最新 Calls
- 标题 — 时间 — 关键议题

### 最新 Decisions
- 决策内容 — 相关 EIP（如有）

---

## 官方资讯

### Ethereum Blog
- **[标题](链接)** — 一句话摘要

### EthPandaOps
- **[标题](链接)** — 一句话摘要（无更新则注明"本周暂无新内容"）

### AllCoreDevs 会议
（有则列出会议类型、关键决定、涉及 EIP；无则写"过去 24 小时无新会议纪要"）

---

## 生态观察

### Vitalik Blog
（有新文章则写标题 + 2-3 句核心论点摘要；无则写"本周暂无新文章"）

### Ethereum Cat Herders
（有新内容则列出；无则注明）

### Week in Ethereum News
（有新期刊则列出 EIP 相关要点；无则注明）

### L2Beat
- 关键 L2 技术动态（与 EIP 相关的升级、依赖变更等）

---

## 今日重点关注
列出 3-5 条最值得 EIP 开发者关注的动态，每条说明为什么重要。
```

Keep each item to 1-2 sentences. Use Chinese for all prose. Keep technical terms (EIP numbers, function names, module names, PR numbers, version tags) in their original form.
