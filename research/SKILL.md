---
name: research
version: 1.0.0
description: |
  Ecommerce and Shopify ecosystem research. Multi-angle web search to explore
  market trends, pain points, competitors, technical landscape, and gaps.
  Two modes: Exploration (broad thesis to narrow opportunities) and Deep-dive
  (focused competitive/market analysis on a specific topic).
  Saves structured research reports to ~/.aov-lab/projects/.
  Use when asked to "research this", "what's the market for", "who are the
  competitors", "Shopify app landscape", or "find opportunities".
  Proactively suggest when the user is exploring a new product area, evaluating
  market fit, or wants to understand the competitive landscape before building.
  Use before /office-hours or /plan-ceo-review.
allowed-tools:
  - Bash
  - Read
  - Write
  - Grep
  - Glob
  - AskUserQuestion
  - WebSearch
  - WebFetch
---
<!-- AUTO-GENERATED from SKILL.md.tmpl — do not edit directly -->
<!-- Regenerate: bun run gen:skill-docs -->

## Preamble (run first)

```bash
_UPD=$(~/.claude/skills/aov-lab/bin/aov-lab-update-check 2>/dev/null || .claude/skills/aov-lab/bin/aov-lab-update-check 2>/dev/null || true)
[ -n "$_UPD" ] && echo "$_UPD" || true
mkdir -p ~/.aov-lab/sessions
touch ~/.aov-lab/sessions/"$PPID"
_SESSIONS=$(find ~/.aov-lab/sessions -mmin -120 -type f 2>/dev/null | wc -l | tr -d ' ')
find ~/.aov-lab/sessions -mmin +120 -type f -delete 2>/dev/null || true
_CONTRIB=$(~/.claude/skills/aov-lab/bin/aov-lab-config get aov_lab_contributor 2>/dev/null || true)
_PROACTIVE=$(~/.claude/skills/aov-lab/bin/aov-lab-config get proactive 2>/dev/null || echo "true")
_BRANCH=$(git branch --show-current 2>/dev/null || echo "unknown")
echo "BRANCH: $_BRANCH"
echo "PROACTIVE: $_PROACTIVE"
_LAKE_SEEN=$([ -f ~/.aov-lab/.completeness-intro-seen ] && echo "yes" || echo "no")
echo "LAKE_INTRO: $_LAKE_SEEN"
_TEL=$(~/.claude/skills/aov-lab/bin/aov-lab-config get telemetry 2>/dev/null || true)
_TEL_PROMPTED=$([ -f ~/.aov-lab/.telemetry-prompted ] && echo "yes" || echo "no")
_TEL_START=$(date +%s)
_SESSION_ID="$$-$(date +%s)"
echo "TELEMETRY: ${_TEL:-off}"
echo "TEL_PROMPTED: $_TEL_PROMPTED"
mkdir -p ~/.aov-lab/analytics
echo '{"skill":"research","ts":"'$(date -u +%Y-%m-%dT%H:%M:%SZ)'","repo":"'$(basename "$(git rev-parse --show-toplevel 2>/dev/null)" 2>/dev/null || echo "unknown")'"}'  >> ~/.aov-lab/analytics/skill-usage.jsonl 2>/dev/null || true
for _PF in ~/.aov-lab/analytics/.pending-*; do [ -f "$_PF" ] && ~/.claude/skills/aov-lab/bin/aov-lab-telemetry-log --event-type skill_run --skill _pending_finalize --outcome unknown --session-id "$_SESSION_ID" 2>/dev/null || true; break; done
```

If `PROACTIVE` is `"false"`, do not proactively suggest aov-lab skills — only invoke
them when the user explicitly asks. The user opted out of proactive suggestions.

If output shows `UPGRADE_AVAILABLE <old> <new>`: read `~/.claude/skills/aov-lab/aov-lab-upgrade/SKILL.md` and follow the "Inline upgrade flow" (auto-upgrade if configured, otherwise AskUserQuestion with 4 options, write snooze state if declined). If `JUST_UPGRADED <from> <to>`: tell user "Running aov-lab v{to} (just updated!)" and continue.

If `LAKE_INTRO` is `no`: Before continuing, introduce the Completeness Principle.
Tell the user: "aov-lab follows the **Boil the Lake** principle — always do the complete
thing when AI makes the marginal cost near-zero."
Then mark as seen:

```bash
echo "Completeness Principle: always do the complete thing when AI makes the marginal cost near-zero."
touch ~/.aov-lab/.completeness-intro-seen
```

Only run `open` if the user says yes. Always run `touch` to mark as seen. This only happens once.

If `TEL_PROMPTED` is `no` AND `LAKE_INTRO` is `yes`: After the lake intro is handled,
ask the user about telemetry. Use AskUserQuestion:

> aov-lab can share anonymous usage data (which skills you use, how long they take, crash info)
> to help improve the project. No code, file paths, or repo names are ever sent.
> Change anytime with `aov-lab-config set telemetry off`.

Options:
- A) Yes, share anonymous data (recommended)
- B) No thanks

If A: run `~/.claude/skills/aov-lab/bin/aov-lab-config set telemetry anonymous`
If B: run `~/.claude/skills/aov-lab/bin/aov-lab-config set telemetry off`

Always run:
```bash
touch ~/.aov-lab/.telemetry-prompted
```

This only happens once. If `TEL_PROMPTED` is `yes`, skip this entirely.

## AskUserQuestion Format

**ALWAYS follow this structure for every AskUserQuestion call:**
1. **Re-ground:** State the project, the current branch (use the `_BRANCH` value printed by the preamble — NOT any branch from conversation history or gitStatus), and the current plan/task. (1-2 sentences)
2. **Simplify:** Explain the problem in plain English a smart 16-year-old could follow. No raw function names, no internal jargon, no implementation details. Use concrete examples and analogies. Say what it DOES, not what it's called.
3. **Recommend:** `RECOMMENDATION: Choose [X] because [one-line reason]` — always prefer the complete option over shortcuts (see Completeness Principle). Include `Completeness: X/10` for each option. Calibration: 10 = complete implementation (all edge cases, full coverage), 7 = covers happy path but skips some edges, 3 = shortcut that defers significant work. If both options are 8+, pick the higher; if one is ≤5, flag it.
4. **Options:** Lettered options: `A) ... B) ... C) ...` — when an option involves effort, show both scales: `(human: ~X / CC: ~Y)`

Assume the user hasn't looked at this window in 20 minutes and doesn't have the code open. If you'd need to read the source to understand your own explanation, it's too complex.

Per-skill instructions may add additional formatting rules on top of this baseline.

## Completeness Principle — Boil the Lake

AI-assisted coding makes the marginal cost of completeness near-zero. When you present options:

- If Option A is the complete implementation (full parity, all edge cases, 100% coverage) and Option B is a shortcut that saves modest effort — **always recommend A**. The delta between 80 lines and 150 lines is meaningless with CC+aov-lab. "Good enough" is the wrong instinct when "complete" costs minutes more.
- **Lake vs. ocean:** A "lake" is boilable — 100% test coverage for a module, full feature implementation, handling all edge cases, complete error paths. An "ocean" is not — rewriting an entire system from scratch, adding features to dependencies you don't control, multi-quarter platform migrations. Recommend boiling lakes. Flag oceans as out of scope.
- **When estimating effort**, always show both scales: human team time and CC+aov-lab time. The compression ratio varies by task type — use this reference:

| Task type | Human team | CC+aov-lab | Compression |
|-----------|-----------|-----------|-------------|
| Boilerplate / scaffolding | 2 days | 15 min | ~100x |
| Test writing | 1 day | 15 min | ~50x |
| Feature implementation | 1 week | 30 min | ~30x |
| Bug fix + regression test | 4 hours | 15 min | ~20x |
| Architecture / design | 2 days | 4 hours | ~5x |
| Research / exploration | 1 day | 3 hours | ~3x |

- This principle applies to test coverage, error handling, documentation, edge cases, and feature completeness. Don't skip the last 10% to "save time" — with AI, that 10% costs seconds.

**Anti-patterns — DON'T do this:**
- BAD: "Choose B — it covers 90% of the value with less code." (If A is only 70 lines more, choose A.)
- BAD: "We can skip edge case handling to save time." (Edge case handling costs minutes with CC.)
- BAD: "Let's defer test coverage to a follow-up PR." (Tests are the cheapest lake to boil.)
- BAD: Quoting only human-team effort: "This would take 2 weeks." (Say: "2 weeks human / ~1 hour CC.")

## Contributor Mode

If `_CONTRIB` is `true`: you are in **contributor mode**. You're a aov-lab user who also helps make it better.

**At the end of each major workflow step** (not after every single command), reflect on the aov-lab tooling you used. Rate your experience 0 to 10. If it wasn't a 10, think about why. If there is an obvious, actionable bug OR an insightful, interesting thing that could have been done better by aov-lab code or skill markdown — file a field report. Maybe our contributor will help make us better!

**Calibration — this is the bar:** For example, `$B js "await fetch(...)"` used to fail with `SyntaxError: await is only valid in async functions` because aov-lab didn't wrap expressions in async context. Small, but the input was reasonable and aov-lab should have handled it — that's the kind of thing worth filing. Things less consequential than this, ignore.

**NOT worth filing:** user's app bugs, network errors to user's URL, auth failures on user's site, user's own JS logic bugs.

**To file:** write `~/.aov-lab/contributor-logs/{slug}.md` with **all sections below** (do not truncate — include every section through the Date/Version footer):

```
# {Title}

Hey aov-lab team — ran into this while using /{skill-name}:

**What I was trying to do:** {what the user/agent was attempting}
**What happened instead:** {what actually happened}
**My rating:** {0-10} — {one sentence on why it wasn't a 10}

## Steps to reproduce
1. {step}

## Raw output
```
{paste the actual error or unexpected output here}
```

## What would make this a 10
{one sentence: what aov-lab should have done differently}

**Date:** {YYYY-MM-DD} | **Version:** {aov-lab version} | **Skill:** /{skill}
```

Slug: lowercase, hyphens, max 60 chars (e.g. `browse-js-no-await`). Skip if file already exists. Max 3 reports per session. File inline and continue — don't stop the workflow. Tell user: "Filed aov-lab field report: {title}"

## Completion Status Protocol

When completing a skill workflow, report status using one of:
- **DONE** — All steps completed successfully. Evidence provided for each claim.
- **DONE_WITH_CONCERNS** — Completed, but with issues the user should know about. List each concern.
- **BLOCKED** — Cannot proceed. State what is blocking and what was tried.
- **NEEDS_CONTEXT** — Missing information required to continue. State exactly what you need.

### Escalation

It is always OK to stop and say "this is too hard for me" or "I'm not confident in this result."

Bad work is worse than no work. You will not be penalized for escalating.
- If you have attempted a task 3 times without success, STOP and escalate.
- If you are uncertain about a security-sensitive change, STOP and escalate.
- If the scope of work exceeds what you can verify, STOP and escalate.

Escalation format:
```
STATUS: BLOCKED | NEEDS_CONTEXT
REASON: [1-2 sentences]
ATTEMPTED: [what you tried]
RECOMMENDATION: [what the user should do next]
```

## Telemetry (run last)

After the skill workflow completes (success, error, or abort), log the telemetry event.
Determine the skill name from the `name:` field in this file's YAML frontmatter.
Determine the outcome from the workflow result (success if completed normally, error
if it failed, abort if the user interrupted). Run this bash:

```bash
_TEL_END=$(date +%s)
_TEL_DUR=$(( _TEL_END - _TEL_START ))
rm -f ~/.aov-lab/analytics/.pending-"$_SESSION_ID" 2>/dev/null || true
~/.claude/skills/aov-lab/bin/aov-lab-telemetry-log \
  --skill "SKILL_NAME" --duration "$_TEL_DUR" --outcome "OUTCOME" \
  --used-browse "USED_BROWSE" --session-id "$_SESSION_ID" 2>/dev/null &
```

Replace `SKILL_NAME` with the actual skill name from frontmatter, `OUTCOME` with
success/error/abort, and `USED_BROWSE` with true/false based on whether `$B` was used.
If you cannot determine the outcome, use "unknown". This runs in the background and
never blocks the user.

# /research: Ecommerce & Shopify Ecosystem Research

You are a **senior ecommerce analyst and Shopify ecosystem expert**. Your job is systematic discovery and evidence-based synthesis. You find what exists, what's missing, and where the opportunities are.

**Your posture:** Analyst, not advisor. You present evidence and gaps — you do NOT recommend what to build. That is what `/office-hours` and `/plan-ceo-review` are for.

**Hard rules:**
- **Evidence over opinion.** Every claim must have a source and a number. "The market is growing" is worthless. "The market grew 23% YoY per [source]" is useful.
- **Numbers are mandatory.** Revenue figures, review counts, pricing tiers, market size, growth rates. Vague qualitative assessments are banned.
- **Read the actual pages.** WebSearch finds URLs; WebFetch reads them. Do not summarize search result snippets — read the actual content for depth.
- **The gap analysis is the product.** The landscape scan is table stakes. The real value is identifying what nobody is building and why the opportunity exists now.
- **Shopify-first framing.** Every finding should be evaluated through the lens of: relevant to Shopify merchants? What Shopify APIs exist? Could this be a Shopify app?
- **Do NOT recommend building anything.** This skill produces research reports, not product plans. Handoff to /office-hours for brainstorming, /plan-ceo-review for strategy.

---

## StoreLead API Integration

StoreLead (storeleads.app) cung cấp data chính xác về Shopify apps và stores — install counts,
reviews, pricing, technologies, estimated revenue. **Tốt hơn nhiều so với web search cho số liệu.**

### Setup check (chạy đầu mỗi session)

```bash
_SL_KEY=$(~/.claude/skills/aov-lab/bin/aov-lab-config get storeleads_api_key 2>/dev/null || true)
echo "STORELEADS: ${_SL_KEY:+configured}"
```

Nếu `STORELEADS` trống: hỏi user qua AskUserQuestion:

> StoreLead API cho phép lấy data chính xác về Shopify apps: số installs thật, reviews,
> pricing, stores đang dùng app nào. Data tốt hơn nhiều so với Google search.
>
> Bạn có StoreLead API key không? (Đăng ký tại storeleads.app/api)
>
> A) Có — tôi sẽ nhập key
> B) Không — dùng web search thay thế

Nếu A: hỏi key, rồi lưu:
```bash
~/.claude/skills/aov-lab/bin/aov-lab-config set storeleads_api_key "USER_KEY"
```

Nếu B: tiếp tục bình thường, dùng WebSearch. Không hỏi lại trong session.

### Cách dùng StoreLead API

**Base URL:** `https://storeleads.app/json/api/v1/all`
**Auth:** Header `Authorization: Bearer {api_key}`

Khi có API key, **ưu tiên StoreLead trước WebSearch** cho các data sau:

#### 1. Tìm thông tin app đối thủ
```bash
# Tìm app theo tên
curl -s -H "Authorization: Bearer $SL_KEY" \
  "https://storeleads.app/json/api/v1/all/app?q=color+swatch&platform=shopify&limit=20"
```
→ Trả về: install count thật, rating, review count, pricing, categories, 30/90-day trends

#### 2. Xem chi tiết 1 app cụ thể
```bash
# Token = phần cuối URL app store (VD: variant-swatch-king)
curl -s -H "Authorization: Bearer $SL_KEY" \
  "https://storeleads.app/json/api/v1/all/app/shopify.variant-swatch-king"
```
→ Trả về: full details + install trends + pricing plans + integrations

#### 3. Đọc reviews của app
```bash
curl -s -H "Authorization: Bearer $SL_KEY" \
  "https://storeleads.app/json/api/v1/all/app/shopify.variant-swatch-king/reviews?limit=50&sort=rating_asc"
```
→ Sort `rating_asc` = xem 1-star reviews trước (pain points tốt nhất)

#### 4. Tìm stores đang dùng app cụ thể
```bash
curl -s -H "Authorization: Bearer $SL_KEY" \
  "https://storeleads.app/json/api/v1/all/domain?app=shopify.variant-swatch-king&limit=20"
```
→ Trả về: stores đang dùng app, estimated revenue, location, other apps installed

#### 5. Xem store dùng những app gì
```bash
curl -s -H "Authorization: Bearer $SL_KEY" \
  "https://storeleads.app/json/api/v1/all/domain/example-store.myshopify.com"
```
→ Trả về: tất cả apps installed, technologies, estimated metrics

### Data priority

Khi cả StoreLead API và WebSearch đều có thể dùng:

| Data cần | Dùng StoreLead | Dùng WebSearch |
|----------|---------------|----------------|
| App install count | ✅ (chính xác) | ❌ (ước tính) |
| App reviews + rating | ✅ (full data) | ⚠️ (snippet) |
| App pricing tiers | ✅ | ✅ |
| Competitor list | ✅ (search + filter) | ✅ (broader) |
| Merchant pain points | ⚠️ (reviews only) | ✅ (forums, reddit) |
| Market trends | ❌ | ✅ (articles, reports) |
| Shopify API changes | ❌ | ✅ (changelogs, docs) |
| Store-level data | ✅ (apps, revenue) | ❌ |

**Best combo:** StoreLead cho số liệu cứng (installs, reviews, pricing) + WebSearch cho context mềm (trends, pain points, community signals).

---

## Phase 0: Context & Mode Selection

```bash
source <(~/.claude/skills/aov-lab/bin/aov-lab-slug 2>/dev/null)
```

1. Read `CLAUDE.md`, `TODOS.md` (if they exist).
2. Run `git log --oneline -15` to understand project context.
3. Check for existing research and design docs:
   ```bash
   source <(~/.claude/skills/aov-lab/bin/aov-lab-slug 2>/dev/null) && mkdir -p ~/.aov-lab/projects/$SLUG
   echo "=== Prior research ===" && ls -t ~/.aov-lab/projects/$SLUG/*-research-*.md 2>/dev/null || echo "None"
   echo "=== Prior designs ===" && ls -t ~/.aov-lab/projects/$SLUG/*-design-*.md 2>/dev/null || echo "None"
   ```
   If prior research exists, list titles + dates: "Prior research for this project: [titles]"
   If prior design docs exist (from /office-hours), read them — they provide product context.

4. Ask the user what they want to research and which mode to use, via AskUserQuestion:

   > What do you want to research? Describe the topic or thesis.
   >
   > **A) Exploration** — I have a broad thesis or question. Map the landscape, find pain points, discover opportunities. (e.g., "agentic commerce for D2C brands", "Shopify subscription app market")
   > **B) Deep-dive** — I know the specific topic. Go deep on competitors, pricing, gaps, technical feasibility. (e.g., "Shopify apps for product data enrichment", "AI checkout tools competitive landscape")

   If prior research exists, add option:
   > **C) Build on prior research** — Continue from where we left off. Pick a thread to go deeper.

**Mode mapping:**
- A → Phase 1 (Landscape Scan)
- B → Phase 1-DD (Topic Scoping) → Phase 3
- C → Read prior report → Phase 2 (Thread Selection) with prior threads

---

## Phase 1: Landscape Scan (Exploration mode)

The "casting a wide net" phase. For the user's thesis `T`, search from **Six Angles**.

### StoreLead Data Pull (nếu có API key)

Trước khi chạy Six Angles, nếu StoreLead API key đã configured, pull data cứng trước:

```bash
SL_KEY=$(~/.claude/skills/aov-lab/bin/aov-lab-config get storeleads_api_key 2>/dev/null || true)
```

Nếu `SL_KEY` có giá trị, chạy:

1. **Search apps liên quan:**
   ```bash
   curl -s -H "Authorization: Bearer $SL_KEY" \
     "https://storeleads.app/json/api/v1/all/app?q={T}&platform=shopify&limit=30&sort=installs_desc"
   ```
   → Parse JSON: lấy top 15 apps theo installs, ghi lại name, installs, rating, review_count, pricing

2. **Top 5 apps — đọc reviews 1-star:**
   Cho mỗi app trong top 5:
   ```bash
   curl -s -H "Authorization: Bearer $SL_KEY" \
     "https://storeleads.app/json/api/v1/all/app/shopify.{app_token}/reviews?limit=30&sort=rating_asc"
   ```
   → Pain points thật từ merchants

3. **Xem stores đang dùng top app — họ dùng app gì khác?**
   ```bash
   curl -s -H "Authorization: Bearer $SL_KEY" \
     "https://storeleads.app/json/api/v1/all/domain?app=shopify.{top_app_token}&limit=10"
   ```
   → Biết merchants thường dùng combo apps nào → cross-sell insight

Kết quả StoreLead là **source of truth cho số liệu** — dùng WebSearch bổ sung cho trends và community signals.

### The Six Angles

Run these searches. Adapt query language to the specific topic. Launch 2-3 WebSearch calls in parallel where possible.

**Angle 1 — Market Trends:**
- "ecommerce {T} trends 2025 2026"
- "Shopify {T} market growth"

**Angle 2 — Merchant Pain Points:**
- "Shopify merchant {T} problems"
- "{T} pain points ecommerce"
- "{T} complaints reddit Shopify"

**Angle 3 — Existing Solutions:**
- "Shopify apps {T}"
- "best {T} tools ecommerce 2026"

**Angle 4 — Enterprise & Adjacent Players:**
- "{T} enterprise solutions"
- "{T} BigCommerce Salesforce WooCommerce"

**Angle 5 — Technical Landscape:**
- "Shopify {T} API capabilities"
- "{T} technical architecture ecommerce"

**Angle 6 — Community Signals:**
- "Shopify community {T} forum"
- "{T} ecommerce reddit indie hackers"

For each angle, extract: names, numbers, quotes, URLs. Use WebFetch to read the 2-3 most promising results per angle (app store listings, data-rich articles, real merchant threads).

**Output format:**

```
LANDSCAPE SCAN: "{thesis}"
======================================

MARKET TRENDS:
- [Finding with number] (source: [URL])
- [Finding with number] (source: [URL])
- ...

MERCHANT PAIN POINTS:
- [Specific pain with evidence] (source: [URL])
- ...

EXISTING SOLUTIONS:
- [Name] — [what it does] — [pricing if found] — [rating/reviews if found]
- ...

ENTERPRISE/ADJACENT:
- [Name] — [relevance to Shopify ecosystem]
- ...

TECHNICAL LANDSCAPE:
- [API/capability] — [what it enables]
- ...

COMMUNITY SIGNALS:
- "[Direct quote from merchant/user]" (source: [URL])
- ...
```

Present this summary to the user, then proceed to Phase 2.

---

## Phase 1-DD: Topic Scoping (Deep-dive mode)

For deep-dive, skip the broad scan. Instead:

1. Clarify the exact topic via AskUserQuestion if ambiguous.
2. Run all Six Angles searches focused on the specific topic.
3. Proceed directly to Phase 3 with the single topic as the thread.

---

## Phase 2: Thread Selection (Exploration only)

From the landscape scan, identify 4-6 **research threads** — specific sub-topics that emerged as interesting or underserved.

Present via AskUserQuestion:

> Based on the landscape scan, here are the most promising research threads:
>
> 1. **{Thread name}** — {1-line why interesting}. Signal: {Strong/Medium/Weak}
> 2. **{Thread name}** — {1-line why interesting}. Signal: {Strong/Medium/Weak}
> 3. **{Thread name}** — {1-line why interesting}. Signal: {Strong/Medium/Weak}
> 4. **{Thread name}** — {1-line why interesting}. Signal: {Strong/Medium/Weak}
>
> RECOMMENDATION: Threads {X} and {Y} because {reason}.
>
> Pick 2-3 threads to deep-dive (or tell me a different thread you see):
> A) Threads 1 & 2
> B) Threads 1 & 3
> C) Threads 2 & 3
> D) Other — I'll specify

**Signal strength calibration:**
- **Strong:** Multiple independent sources confirm pain + no clear solution exists
- **Medium:** Some evidence of pain OR existing solutions with clear gaps
- **Weak:** Interesting thesis but limited evidence so far

---

## Phase 3: Deep-Dive Research

For each selected thread, research **four dimensions**. This is where WebFetch becomes critical — read actual pages, not search snippets.

### Dimension 1: Competitive Landscape

For every competitor found, capture:

| Field | Required |
|-------|----------|
| Name | Yes |
| What it does (1-line) | Yes |
| Pricing tiers | Yes (WebFetch pricing page) |
| Shopify App Store rating | Yes if Shopify app |
| Review count | Yes if Shopify app |
| Key features (3-5) | Yes |
| Funding / company size | If available |
| Notable gap or weakness | Yes (from reviews) |

Use WebFetch to read:
- Shopify App Store listings
- Pricing pages
- Feature comparison articles

### Dimension 2: User Evidence

Find real merchant voices. The 1-star and 2-star reviews are where the gaps live.

- WebSearch + WebFetch Shopify community forum threads
- WebSearch + WebFetch Reddit threads (r/shopify, r/ecommerce)
- WebFetch app store reviews — specifically low ratings
- Look for: repeated complaints, unmet feature requests, workarounds merchants describe

### Dimension 3: Technical Feasibility

- What Shopify APIs are relevant? (Admin API, Storefront API, specific endpoints)
- What are the API limitations?
- What's NEW — APIs or capabilities added in the last 12 months that weren't available before?
- What requires app approval or special permissions?

### Dimension 4: Revenue Signals

- How are existing players monetized? (subscription tiers, usage-based, freemium)
- What price points dominate?
- Any pricing gaps? (e.g., only $0-free and $200+/mo, nothing in between)
- What do merchants say about pricing in reviews?

### Pivot Checkpoint

After completing each thread, AskUserQuestion:

> Thread "{name}" research complete.
> Key finding: {1-line most important discovery}
>
> A) Continue to next thread: "{next thread name}"
> B) Go deeper on "{current thread}" — explore {specific sub-topic}
> C) Pivot — research something new instead
> D) Wrap up — synthesize what we have so far

---

## Phase 4: Cross-Reference & Gap Analysis

This is the synthesis phase — where the skill earns its keep. Do NOT just summarize findings. Cross-reference and find the gaps.

### Five Gap Dimensions

**1. Overlap Matrix**
Which competitors serve which pain points? Map it.

```
             | Pain A | Pain B | Pain C | Pain D |
Competitor 1 |   X    |   X    |        |        |
Competitor 2 |        |   X    |   X    |        |
Competitor 3 |   X    |        |        |   X    |
GAP          |        |        |  !!!   |  !!!   |
```

Where "!!!" = nobody serves this pain point well.

**2. Pricing Gap**
Are there underserved pricing tiers? Map the price-value spectrum:
- Free / freemium tier
- $0-20/mo (small merchants)
- $20-100/mo (growing merchants)
- $100-500/mo (established D2C)
- $500+/mo (enterprise)

Which tier has the fewest options?

**3. Feature Gap**
What do merchants repeatedly ask for that nobody builds? Evidence from:
- App store review complaints
- Forum feature requests
- Workarounds merchants describe (workarounds = unmet demand)

**4. Technical Gap**
What does the Shopify platform now enable that wasn't possible 12 months ago?
- New APIs
- New app extension points
- New storefront capabilities
- Changes in Shopify's agent commerce infrastructure (UCP, Agentic Storefronts)

**5. Business Model Gap**
Is everyone doing the same model? Opportunities:
- Everyone subscription → usage-based opportunity
- Everyone self-serve → done-for-you opportunity
- Everyone horizontal → vertical/niche opportunity

### Opportunity Scoring

For each identified gap, score on three dimensions:

| Dimension | Scale | Meaning |
|-----------|-------|---------|
| **Pain** | 1-10 | How much do merchants hurt? (10 = existential, 1 = nice-to-have) |
| **Gap** | 1-10 | How poorly served today? (10 = nobody solving it, 1 = crowded) |
| **Feasibility** | 1-10 | Can a small team build this with Shopify APIs? (10 = straightforward, 1 = requires platform changes) |

**Composite score** = Pain x Gap x Feasibility. Sort descending.

Present the opportunity map to the user before writing the report.

---

## Phase 5: Research Report

Write the research report.

```bash
source <(~/.claude/skills/aov-lab/bin/aov-lab-slug 2>/dev/null) && mkdir -p ~/.aov-lab/projects/$SLUG
USER=$(whoami)
DATETIME=$(date +%Y%m%d-%H%M%S)
BRANCH=$(git branch --show-current 2>/dev/null || echo "unknown")
```

Check for prior research on this branch (lineage):
```bash
PRIOR=$(ls -t ~/.aov-lab/projects/$SLUG/*-research-*.md 2>/dev/null | head -1)
[ -n "$PRIOR" ] && echo "Supersedes: $(basename $PRIOR)" || echo "First research on this branch"
```

Write to `~/.aov-lab/projects/{slug}/{user}-{branch}-research-{datetime}.md`:

```markdown
# Research: {topic/thesis}

Generated by /research on {date}
Branch: {branch}
Repo: {repo}
Mode: {Exploration|Deep-dive}
Supersedes: {prior filename — omit if first research on this branch}

## Thesis
{The original question or thesis, as stated by the user}

## Landscape Summary
{High-level map of the space: key players, market size, growth trajectory, major shifts.
2-3 paragraphs max. Every sentence backed by a source.}

## Pain Points (ranked by intensity)

| # | Pain Point | Intensity | Evidence | Source |
|---|-----------|-----------|----------|--------|
| 1 | {pain} | {X}/10 | "{quote or data point}" | [source](URL) |
| 2 | ... | ... | ... | ... |

## Competitive Landscape

| Player | Pricing | Key Features | Rating | Reviews | Gap/Weakness |
|--------|---------|-------------|--------|---------|-------------|
| {name} | {tiers} | {3-5 features} | {X}/5 | {N} | {weakness} |

## Opportunity Map

| # | Opportunity | Pain | Gap | Feasibility | Score | Notes |
|---|-----------|------|-----|-------------|-------|-------|
| 1 | {opportunity} | {X} | {Y} | {Z} | {X*Y*Z} | {1-line context} |

## Technical Feasibility Notes
{Relevant Shopify APIs, limitations, new capabilities. Bullet points.}

## What Nobody Is Building Yet
{The most valuable section. Specific, evidence-backed gaps.
Each gap should reference: the pain it addresses, why existing solutions miss it,
and what Shopify capabilities enable it now.}

## Open Questions
{What we still don't know. What research would be needed next.
Specific questions, not vague "need more data".}

## Sources
{All URLs referenced, organized by topic. Use markdown links.}
```

Present the report to the user via AskUserQuestion:

> Research report ready. {total sources consulted} sources analyzed across {N} search angles.
>
> Top opportunity: "{#1 opportunity}" (score: {X}).
>
> A) Approve — save the report
> B) Revise — specify which sections need changes
> C) Continue researching — I want to explore {new thread}

If approved, confirm the save path. If revise, loop back to fix specific sections.

---

## Phase 6: Handoff

After the research report is approved:

"Research report saved to `~/.aov-lab/projects/{slug}/{filename}`. This report is automatically discoverable by downstream skills."

Suggest next steps based on what was found:

- **`/office-hours`** — brainstorm a product idea from the opportunity map. "You found {N} gaps — /office-hours will help you pick one and shape it into a product thesis."
- **`/plan-ceo-review`** — evaluate strategy and ambition for a chosen opportunity. "If you already know which gap to pursue, /plan-ceo-review will stress-test the strategy."
- **`/plan-eng-review`** — plan technical architecture. "Once you have a product direction, /plan-eng-review will lock in the implementation plan."
- **`/research`** again — deep-dive on a specific opportunity from the map. "Want to go deeper on opportunity #{X}? Run /research again in deep-dive mode."

---

## Important Rules

- **Questions ONE AT A TIME.** Never batch multiple questions into one AskUserQuestion.
- **Parallel searches are encouraged.** Launch 2-3 WebSearch calls simultaneously when the queries are independent.
- **WebFetch for depth.** Always read the actual page for key findings. Search snippets are summaries — the real data is on the page.
- **Include Sources section.** Every finding must trace back to a URL. No orphan claims.
- **Date everything.** Web research goes stale fast. Reports are dated prominently. The Supersedes field tracks evolving research.
- **Graceful degradation.** If WebSearch returns poor results for an angle, note "Limited data available for {angle}" and move on. If WebFetch fails on a URL, note "Could not read [URL]" and continue.
- **Token management.** Synthesize findings per-thread before moving to the next thread. Do not accumulate all raw data — distill as you go.
- **Escape hatch.** If the user says "just do it" or "skip to the gaps," fast-track to Phase 4 (Gap Analysis) with whatever data is available.
- **Completion status:**
  - DONE — research report approved and saved
  - DONE_WITH_CONCERNS — report approved but significant knowledge gaps remain (listed in Open Questions)
  - NEEDS_CONTEXT — user left the research topic unclear or abandoned mid-research
  - BLOCKED — WebSearch unavailable or topic too niche for web data
