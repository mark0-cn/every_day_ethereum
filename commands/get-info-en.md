Generate an Ethereum EIP daily digest for the past 24 hours by collecting data from all sources below, then formatting a unified **English** Markdown report.

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

**If the result is `NOT_SET`**, inform the user:

> ⚠️ **GITHUB_TOKEN not detected**
>
> Without a token, the GitHub API limit is **60 requests/hour** — usually not enough for a full digest run.
>
> **How to get one (free, takes ~1 minute):**
> 1. Go to https://github.com/settings/tokens/new
> 2. Set any Note (e.g. `every-day-ethereum`)
> 3. Set Expiration to "No expiration" or a custom date
> 4. **No scopes needed** — public repos require no permissions
> 5. Click "Generate token" and copy it
>
> **How to set it:**
> ```bash
> export GITHUB_TOKEN=your_token_here
> ```
>
> Would you like to enter your token now? Paste it below (or type `skip` to continue with the anonymous rate limit):

Wait for the user's response:
- If the user pastes a token string (starts with `ghp_` or `github_pat_`), run `export GITHUB_TOKEN=<token>` and confirm "✅ Token set. Starting data collection."
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

Produce a single English Markdown document structured as follows:

```
# Ethereum Daily Digest · {YYYY-MM-DD}

## Summary
| Source | Updates |
|--------|---------|
| EIP PRs/Issues | X |
| revm | X commits / X PRs |
| reth | X commits / X PRs |
| execution-specs | X commits / X PRs |
| consensus-specs | X commits / X PRs |
| execution-apis | X commits / X PRs |
| devp2p | X commits / X PRs |
| alloy | X commits / X PRs |
| foundry | X commits / X PRs |
| Ethereum Magicians | X |
| ethresear.ch | X |
| Forkcast | X |
| Ethereum Blog | X |
| EthPandaOps | X |
| Vitalik Blog | yes/no |
| Cat Herders | yes/no |
| Week in Ethereum | yes/no |
| L2Beat | X |
| AllCoreDevs | yes/no |

---

## EIP Activity

### New / Updated PRs
- **[EIP-XXXX]** Title — Author | Status
  > One-line summary of the core change

### Issue Highlights
- **[#number]** Title — Key discussion point

---

## Spec Changes

### execution-specs
- **[#number]** Title — Affected fork (e.g. Prague / Osaka) and related EIPs

### consensus-specs
- **[#number]** Title — Change summary

### execution-apis (Engine API / JSON-RPC)
- **[#number]** Title — Added or modified method names

### devp2p
- **[#number]** Title — Affected protocol (e.g. discv5 / eth / snap)

---

## Toolchain

### revm
#### New Release
(if any) vX.X.X — Published at — Key changes
#### Merged PRs / Notable Commits
- **[#number]** Title — Affected module (interpreter / primitives / precompile)

### reth
#### New Release
(if any) vX.X.X — Published at — Key changes
#### Merged PRs / Notable Commits
- **[#number]** Title — Affected module (engine / network / txpool / rpc)

### alloy
#### New Release
(if any) vX.X.X — Published at — Key changes
#### Merged PRs / Notable Commits
- **[#number]** Title — Affected module

### foundry
#### New Release
(if any) vX.X.X — Published at — Key changes
#### Merged PRs / Notable Commits
- **[#number]** Title — Affected module (forge / cast / anvil / chisel)

---

## Community & Research

### Ethereum Magicians
- **[Title](link)** — Core topic, replies/participants count

### ethresear.ch
- **[Title](link)** — Core topic, replies/participants count

---

## Forkcast
### Latest Calls
- Title — Date — Key agenda items

### Latest Decisions
- Decision summary — Related EIP(s) if any

---

## Official

### Ethereum Blog
- **[Title](link)** — One-line summary

### EthPandaOps
- **[Title](link)** — One-line summary (if no updates: "No new content this week")

### AllCoreDevs Meeting Notes
(If new notes: list meeting type, key decisions, and EIPs discussed. If none: "No new meeting notes in the past 24 hours.")

---

## Ecosystem

### Vitalik Blog
(If new post: title + 2-3 sentence summary of key arguments. If none: "No new posts this week.")

### Ethereum Cat Herders
(List new content if any, otherwise note no updates.)

### Week in Ethereum News
(If new issue: list top EIP-relevant headlines. If none: note the latest issue date.)

### L2Beat
- Key L2 technical updates relevant to EIPs (upgrades, EIP dependencies, governance)

---

## Top Picks
List 3-5 items most worth an EIP developer's attention today, with a brief explanation of why each matters.
```

Keep each item to 1-2 sentences. All prose should be in English. Keep technical identifiers (EIP numbers, function names, module names, PR numbers, version tags) in their original form.
