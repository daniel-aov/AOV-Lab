# aov-lab development

## AOV.ai Context

AOV.ai is a Shopify app brand focused on helping merchants increase Average Order Value.
All skills should be evaluated through the lens of Shopify app development.

**Current app portfolio:**
- Bundle (Bundle Builder, Bundle Offers, Volume Discounts)
- Free Gift (Gift with Purchase, BOGO)
- Checkout Upsell / Checkout Widget
- Cart Drawer
- Pre-order
- Post Purchase Upsell

**Strategy:** Each app is a separate Shopify app listing. Shared boilerplate across apps.
Free apps (like Color Swatch) serve as top-of-funnel to drive installs for paid apps.

## Shopify App Development Knowledge

### Architecture Patterns
- **Theme App Extension** (App Blocks): widget trên storefront, no code injection vào theme.
  Dùng Liquid + JS + CSS. Merchant drag-drop trong Theme Editor.
- **Embedded App** (Admin): React + Polaris + App Bridge. Chạy trong iframe Shopify Admin.
- **Shopify Functions** (Backend logic): Rust/WASM, chạy trên Shopify servers, <5ms.
  Dùng cho: custom discounts, cart transform, order routing.
- **Webhooks**: product/create, product/update, app/uninstalled, GDPR mandatory hooks.
- **Metafields**: key-value storage gắn vào product/shop/customer. Theme Extension đọc bằng Liquid.

### Shopify APIs
- **Admin API (GraphQL)**: CRUD products, orders, customers, metafields. Required GraphQL từ Apr 2025.
- **Storefront API**: Public API cho custom storefronts, headless.
- **Billing API / Managed Pricing**: Xử lý subscription charges. Managed Pricing là default mới.
- **Cart Transform API**: Modify cart items server-side (bundles, warranties).
- **Checkout UI Extensions**: App blocks tại checkout page.

### App Store Requirements (CRITICAL)
- GDPR webhooks MANDATORY (Customer Data Request, Customer Redact, Shop Redact)
- Session tokens required — KHÔNG dùng third-party cookies
- HTTPS/TLS mandatory
- GraphQL Admin API required cho new apps (REST is legacy)
- Latest App Bridge version required
- Theme modifications CHỈ qua Theme App Extension — KHÔNG sửa theme code trực tiếp
- Không dùng "Shopify" trong app name (VD: "Shopify SEO" bị cấm, "SEO for Shopify" OK)
- Không fake reviews, fake purchase notifications, không claim "best" / "first" / "only"
- Billing phải qua Shopify Billing API hoặc Managed Pricing — KHÔNG off-platform billing
- App review: 5-10 business days, >60% rejections do missing GDPR hooks hoặc broken billing
- Demo screencast required (English or subtitled)
- Mỗi screenshot phải unique, hiện actual app UI, không logo-only

### App Store Optimization (ASO)
- >70% downloads từ search → keywords trong app name + subtitle quan trọng nhất
- Target long-tail keywords: "color swatch variant image" thay vì chỉ "swatch"
- Category selection phải match intent
- Minimum 4.0 rating để được featured
- Encourage reviews từ happy users (nhưng KHÔNG fake)
- Track: impressions, CTR, conversion rate hàng tuần

### StoreLead API (market intelligence)
- API cho data chính xác về Shopify apps + stores: install counts, reviews, pricing, technologies
- Config key: `aov-lab-config set storeleads_api_key "KEY"` (đăng ký tại storeleads.app/api)
- `/research` skill tự động dùng StoreLead khi có key — ưu tiên hơn web search cho số liệu cứng
- Endpoints chính: search apps, app details, app reviews, domain lookup, domain apps
- Rate limit: 5 req/s (Pro), 20 req/s (Enterprise)

### Shopify Native Features (biết để không build trùng)
- Native color swatches (Products 2.0): swatch.color, swatch.image — cơ bản, thiếu collection page
- Native bundles: Shopify Bundles app — basic, không custom UI
- Native discounts: automatic + manual discounts — OK cho simple cases
- Variant limit: 2,048 variants per product (từ Oct 2025)
- Combined Listings: chỉ Plus, parent-child model, không kết hợp được bundles
- Shopify Magic/Sidekick: AI assistant — chỉ giúp dùng features gốc, không thêm features mới

### Common Pitfalls
- Rate limiting: Admin API có rate limits, cần retry with backoff
- Webhook reliability: webhooks có thể miss — cần reconciliation job
- Theme compatibility: mỗi theme render khác nhau, test 15-20 themes trước launch
- App conflicts: merchants cài nhiều apps → CSS/JS conflicts. Namespace mọi thứ.
- Performance: collection page load nhiều products → lazy-load mandatory
- Billing: merchant uninstall không trigger refund tự động — handle gracefully

## Commands

```bash
bun install          # install dependencies
bun test             # run free tests (browse + snapshot + skill validation)
bun run test:evals   # run paid evals: LLM judge + E2E (diff-based, ~$4/run max)
bun run test:evals:all  # run ALL paid evals regardless of diff
bun run test:e2e     # run E2E tests only (diff-based, ~$3.85/run max)
bun run test:e2e:all # run ALL E2E tests regardless of diff
bun run eval:select  # show which tests would run based on current diff
bun run dev <cmd>    # run CLI in dev mode, e.g. bun run dev goto https://example.com
bun run build        # gen docs + compile binaries
bun run gen:skill-docs  # regenerate SKILL.md files from templates
bun run skill:check  # health dashboard for all skills
bun run dev:skill    # watch mode: auto-regen + validate on change
bun run eval:list    # list all eval runs from ~/.aov-lab-dev/evals/
bun run eval:compare # compare two eval runs (auto-picks most recent)
bun run eval:summary # aggregate stats across all eval runs
```

`test:evals` requires `ANTHROPIC_API_KEY`. Codex E2E tests (`test/codex-e2e.test.ts`)
use Codex's own auth from `~/.codex/` config — no `OPENAI_API_KEY` env var needed.
E2E tests stream progress in real-time (tool-by-tool via `--output-format stream-json
--verbose`). Results are persisted to `~/.aov-lab-dev/evals/` with auto-comparison
against the previous run.

**Diff-based test selection:** `test:evals` and `test:e2e` auto-select tests based
on `git diff` against the base branch. Each test declares its file dependencies in
`test/helpers/touchfiles.ts`. Changes to global touchfiles (session-runner, eval-store,
llm-judge, gen-skill-docs) trigger all tests. Use `EVALS_ALL=1` or the `:all` script
variants to force all tests. Run `eval:select` to preview which tests would run.

## Testing

```bash
bun test             # run before every commit — free, <2s
bun run test:evals   # run before shipping — paid, diff-based (~$4/run max)
```

`bun test` runs skill validation, gen-skill-docs quality checks, and browse
integration tests. `bun run test:evals` runs LLM-judge quality evals and E2E
tests via `claude -p`. Both must pass before creating a PR.

## Project structure

```
aov-lab/
├── browse/          # Headless browser CLI (Playwright)
│   ├── src/         # CLI + server + commands
│   │   ├── commands.ts  # Command registry (single source of truth)
│   │   └── snapshot.ts  # SNAPSHOT_FLAGS metadata array
│   ├── test/        # Integration tests + fixtures
│   └── dist/        # Compiled binary
├── scripts/         # Build + DX tooling
│   ├── gen-skill-docs.ts  # Template → SKILL.md generator
│   ├── skill-check.ts     # Health dashboard
│   └── dev-skill.ts       # Watch mode
├── test/            # Skill validation + eval tests
│   ├── helpers/     # skill-parser.ts, session-runner.ts, llm-judge.ts, eval-store.ts
│   ├── fixtures/    # Ground truth JSON, planted-bug fixtures, eval baselines
│   ├── skill-validation.test.ts  # Tier 1: static validation (free, <1s)
│   ├── gen-skill-docs.test.ts    # Tier 1: generator quality (free, <1s)
│   ├── skill-llm-eval.test.ts   # Tier 3: LLM-as-judge (~$0.15/run)
│   └── skill-e2e.test.ts         # Tier 2: E2E via claude -p (~$3.85/run)
├── qa-only/         # /qa-only skill (report-only QA, no fixes)
├── plan-design-review/  # /plan-design-review skill (report-only design audit)
├── design-review/    # /design-review skill (design audit + fix loop)
├── ship/            # Ship workflow skill
├── review/          # PR review skill
├── plan-ceo-review/ # /plan-ceo-review skill
├── plan-eng-review/ # /plan-eng-review skill
├── office-hours/    # /office-hours skill (YC Office Hours — startup diagnostic + builder brainstorm)
├── investigate/     # /investigate skill (systematic root-cause debugging)
├── retro/           # Retrospective skill
├── document-release/ # /document-release skill (post-ship doc updates)
├── setup            # One-time setup: build binary + symlink skills
├── SKILL.md         # Generated from SKILL.md.tmpl (don't edit directly)
├── SKILL.md.tmpl    # Template: edit this, run gen:skill-docs
└── package.json     # Build scripts for browse
```

## SKILL.md workflow

SKILL.md files are **generated** from `.tmpl` templates. To update docs:

1. Edit the `.tmpl` file (e.g. `SKILL.md.tmpl` or `browse/SKILL.md.tmpl`)
2. Run `bun run gen:skill-docs` (or `bun run build` which does it automatically)
3. Commit both the `.tmpl` and generated `.md` files

To add a new browse command: add it to `browse/src/commands.ts` and rebuild.
To add a snapshot flag: add it to `SNAPSHOT_FLAGS` in `browse/src/snapshot.ts` and rebuild.

## Platform-agnostic design

Skills must NEVER hardcode framework-specific commands, file patterns, or directory
structures. Instead:

1. **Read CLAUDE.md** for project-specific config (test commands, eval commands, etc.)
2. **If missing, AskUserQuestion** — let the user tell you or let aov-lab search the repo
3. **Persist the answer to CLAUDE.md** so we never have to ask again

This applies to test commands, eval commands, deploy commands, and any other
project-specific behavior. The project owns its config; aov-lab reads it.

## Writing SKILL templates

SKILL.md.tmpl files are **prompt templates read by Claude**, not bash scripts.
Each bash code block runs in a separate shell — variables do not persist between blocks.

Rules:
- **Use natural language for logic and state.** Don't use shell variables to pass
  state between code blocks. Instead, tell Claude what to remember and reference
  it in prose (e.g., "the base branch detected in Step 0").
- **Don't hardcode branch names.** Detect `main`/`master`/etc dynamically via
  `gh pr view` or `gh repo view`. Use `{{BASE_BRANCH_DETECT}}` for PR-targeting
  skills. Use "the base branch" in prose, `<base>` in code block placeholders.
- **Keep bash blocks self-contained.** Each code block should work independently.
  If a block needs context from a previous step, restate it in the prose above.
- **Express conditionals as English.** Instead of nested `if/elif/else` in bash,
  write numbered decision steps: "1. If X, do Y. 2. Otherwise, do Z."

## Browser interaction

When you need to interact with a browser (QA, dogfooding, cookie setup), use the
`/browse` skill or run the browse binary directly via `$B <command>`. NEVER use
`mcp__claude-in-chrome__*` tools — they are slow, unreliable, and not what this
project uses.

## Vendored symlink awareness

When developing aov-lab, `.claude/skills/aov-lab` may be a symlink back to this
working directory (gitignored). This means skill changes are **live immediately** —
great for rapid iteration, risky during big refactors where half-written skills
could break other Claude Code sessions using aov-lab concurrently.

**Check once per session:** Run `ls -la .claude/skills/aov-lab` to see if it's a
symlink or a real copy. If it's a symlink to your working directory, be aware that:
- Template changes + `bun run gen:skill-docs` immediately affect all aov-lab invocations
- Breaking changes to SKILL.md.tmpl files can break concurrent aov-lab sessions
- During large refactors, remove the symlink (`rm .claude/skills/aov-lab`) so the
  global install at `~/.claude/skills/aov-lab/` is used instead

**For plan reviews:** When reviewing plans that modify skill templates or the
gen-skill-docs pipeline, consider whether the changes should be tested in isolation
before going live (especially if the user is actively using aov-lab in other windows).

## Commit style

**Always bisect commits.** Every commit should be a single logical change. When
you've made multiple changes (e.g., a rename + a rewrite + new tests), split them
into separate commits before pushing. Each commit should be independently
understandable and revertable.

Examples of good bisection:
- Rename/move separate from behavior changes
- Test infrastructure (touchfiles, helpers) separate from test implementations
- Template changes separate from generated file regeneration
- Mechanical refactors separate from new features

When the user says "bisect commit" or "bisect and push," split staged/unstaged
changes into logical commits and push.

## CHANGELOG style

CHANGELOG.md is **for users**, not contributors. Write it like product release notes:

- Lead with what the user can now **do** that they couldn't before. Sell the feature.
- Use plain language, not implementation details. "You can now..." not "Refactored the..."
- **Never mention TODOS.md, internal tracking, eval infrastructure, or contributor-facing
  details.** These are invisible to users and meaningless to them.
- Put contributor/internal changes in a separate "For contributors" section at the bottom.
- Every entry should make someone think "oh nice, I want to try that."
- No jargon: say "every question now tells you which project and branch you're in" not
  "AskUserQuestion format standardized across skill templates via preamble resolver."

## AI effort compression

When estimating or discussing effort, always show both human-team and CC+aov-lab time:

| Task type | Human team | CC+aov-lab | Compression |
|-----------|-----------|-----------|-------------|
| Boilerplate / scaffolding | 2 days | 15 min | ~100x |
| Test writing | 1 day | 15 min | ~50x |
| Feature implementation | 1 week | 30 min | ~30x |
| Bug fix + regression test | 4 hours | 15 min | ~20x |
| Architecture / design | 2 days | 4 hours | ~5x |
| Research / exploration | 1 day | 3 hours | ~3x |

Completeness is cheap. Don't recommend shortcuts when the complete implementation
is a "lake" (achievable) not an "ocean" (multi-quarter migration). See the
Completeness Principle in the skill preamble for the full philosophy.

## Local plans

Contributors can store long-range vision docs and design documents in `~/.aov-lab-dev/plans/`.
These are local-only (not checked in). When reviewing TODOS.md, check `plans/` for candidates
that may be ready to promote to TODOs or implement.

## E2E eval failure blame protocol

When an E2E eval fails during `/ship` or any other workflow, **never claim "not
related to our changes" without proving it.** These systems have invisible couplings —
a preamble text change affects agent behavior, a new helper changes timing, a
regenerated SKILL.md shifts prompt context.

**Required before attributing a failure to "pre-existing":**
1. Run the same eval on main (or base branch) and show it fails there too
2. If it passes on main but fails on the branch — it IS your change. Trace the blame.
3. If you can't run on main, say "unverified — may or may not be related" and flag it
   as a risk in the PR body

"Pre-existing" without receipts is a lazy claim. Prove it or don't say it.

## Deploying to the active skill

The active skill lives at `~/.claude/skills/aov-lab/`. After making changes:

1. Push your branch
2. Fetch and reset in the skill directory: `cd ~/.claude/skills/aov-lab && git fetch origin && git reset --hard origin/main`
3. Rebuild: `cd ~/.claude/skills/aov-lab && bun run build`

Or copy the binary directly: `cp browse/dist/browse ~/.claude/skills/aov-lab/browse/dist/browse`
