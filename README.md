# aov-lab

aov-lab biến Claude Code thành đội ngũ chuyên Shopify app mà bạn thực sự điều hành — một market analyst phân tích đối thủ bằng data thật từ StoreLead, một product partner hỏi ngược "bằng chứng merchants cần cái này ở đâu?" trước khi bạn viết code, một eng manager chốt kiến trúc với 10-point Shopify checklist, một designer đảm bảo Polaris compliance và mobile-first, một staff engineer bắt 10 lỗi mà App Store sẽ reject, một QA lead mở browser thật click qua embedded app và storefront widget, và một release engineer check 12-item submission checklist trước khi push. 15 specialists, tất cả là slash commands, tất cả đã train kiến thức Shopify.


**Research → Think → Plan → Build → Review → Test → Ship**

`/research` tìm gaps thị trường (kết hợp StoreLead API + web search). `/office-hours` đọc research rồi viết design doc. `/plan-eng-review` đọc design doc rồi chốt kiến trúc + test plan. `/review` check code theo 10 Shopify rejection patterns. `/qa` test trên browser thật với Shopify-specific flows. `/ship` check 12-item App Store submission checklist rồi push.

Không bước nào bị bỏ qua vì mỗi bước đọc output bước trước.

## 15 Skills

### Research & Strategy

| Skill | Vai trò | Làm gì |
|-------|---------|--------|
| `/research` | **Market Analyst** | Phân tích thị trường Shopify từ 6 góc. Tích hợp **StoreLead API** cho data chính xác (install counts thật, reviews, pricing). Gap score = Pain x Gap x Feasibility. |
| `/office-hours` | **Product Partner** | Hỏi 6 câu khó trước khi build. 6 Shopify lenses: App Store positioning, API feasibility, pricing model, native feature risk, merchant journey, theme compatibility. Output: design doc. |
| `/plan-ceo-review` | **CEO / Founder** | Challenge strategy. 6 Shopify Platform Strategy lenses: native feature risk, merchant segment (SMB/mid-market/Plus), App Store dynamics, cross-sell fit, API trajectory, free vs paid logic. |

### Architecture & Design

| Skill | Vai trò | Làm gì |
|-------|---------|--------|
| `/plan-eng-review` | **Eng Manager** | Chốt kiến trúc + 10-point Shopify checklist (metafields vs DB, theme extension, rate limits, GDPR, webhooks, billing, sessions, scopes, theme compat, cross-app). Output: decisions + test plan. |
| `/plan-design-review` | **Senior Designer** | Chấm design 0-10. 7 Shopify checks: Polaris compliance, theme-native storefront, mobile-first (44px touch targets), App Store screenshots, onboarding <5min, merchant vs customer UX, loading states. |
| `/design-consultation` | **Design Partner** | Build design system. Shopify two-surface guidance: Admin = Polaris, Storefront = theme-native, Listing = custom brand. Output: DESIGN.md. |

### Build & Review

| Skill | Vai trò | Làm gì |
|-------|---------|--------|
| `/review` | **Staff Engineer** | Code review + 10 Shopify rejection patterns: GDPR hooks, session tokens, rate limiting, metafield namespace, webhook verification, billing edge cases, storefront performance <50KB. |
| `/investigate` | **Debugger** | Debug có hệ thống. 9 Shopify bug patterns: session token expiry, webhook miss, API rate limit 429, theme incompatibility, CSS conflict, scope permissions, checkout migration, metafield loss, billing currency mismatch. |
| `/design-review` | **Designer Who Codes** | Audit visual trên live site. Shopify standards: Polaris admin UI, theme-native storefront widget, App Store screenshot quality, mobile-first. Fix với atomic commits + before/after screenshots. |

### Test & Ship

| Skill | Vai trò | Làm gì |
|-------|---------|--------|
| `/qa` | **QA Lead** | Test trên browser thật. Auto-detect app type từ URL: embedded admin, storefront widget, checkout extension. Shopify-specific test flows cho mỗi loại. |
| `/qa-only` | **QA Reporter** | Như `/qa` nhưng chỉ report, không fix. |
| `/browse` | **QA Engineer** | Headless Chromium. Click, screenshot, check responsive. ~100ms/command. |
| `/ship` | **Release Engineer** | Sync main, run tests, push, open PR. 12-item Shopify App Store Submission Checklist auto-check trước khi push. |
| `/document-release` | **Technical Writer** | Update docs cho match code đã ship. |

### Utility

| Skill | Làm gì |
|-------|--------|
| `/careful` | Cảnh báo trước lệnh nguy hiểm: rm -rf, DROP TABLE, force-push. |
| `/setup-browser-cookies` | Import cookies từ Chrome/Arc/Brave vào headless browser. Test authenticated Shopify pages. |

## Điểm mạnh nhất: Research → Think → Plan

Phần code review, QA, ship — bất kỳ AI coding tool nào cũng làm được. Phần **nghĩ trước khi code** mới là thứ hiếm.

`/research` không phải Google rồi đọc. Nó search 6 góc, đọc actual pages, cross-reference, rồi cho ra bảng cơ hội có số liệu. Khi có **StoreLead API key**, pull data chính xác: install counts thật, full reviews (sort 1-star trước = pain points), stores đang dùng app nào — tốt hơn web search cho số liệu cứng.

`/office-hours` không phải "tell me your idea and I'll say it's great." Nó hỏi ngược: bằng chứng demand ở đâu? Merchants đang giải quyết bằng gì? Version nhỏ nhất ai trả tiền tuần này? Nếu bạn không trả lời được — đó là thứ quan trọng nhất cần tìm trước khi viết code.

`/plan-eng-review` chuyển design doc thành architecture decisions cụ thể. Metafields hay DB? Liquid hay JS? Lazy load hay preload? Mỗi quyết định có options, tradeoffs, recommendation. Bạn ra khỏi session với decisions đã chốt, test plan đã mapped, failure modes đã identified.

Ba skills này nối thành pipeline — research output → design doc → architecture decisions → test plan. Trước khi viết dòng code đầu tiên, đã biết chính xác build gì, cho ai, bằng cách nào.

## Research app mới

Khi muốn tìm cơ hội build app mới trên Shopify:

```
/research
> "Thị trường [category] trên Shopify — đối thủ, pricing, gaps"
→ StoreLead API: install counts thật, reviews, pricing per app
→ Web search: pain points từ forums, reddit, community
→ Gap analysis: feature gap, pricing gap, technical gap
→ Opportunity scoring: cơ hội nào đáng làm nhất?

/office-hours
> "Muốn build app [idea] dựa trên research"
→ Challenge: bằng chứng demand ở đâu?
→ Challenge: Shopify API có hỗ trợ không?
→ Shopify lenses: App Store positioning, native risk, pricing model
→ Output: Design doc với narrowest wedge + approaches

/plan-eng-review
→ Chốt kiến trúc với 10-point Shopify checklist
→ Test plan mapped cho mọi flow
```

Kết quả: research report + design doc + architecture decisions + test plan. Chưa viết dòng code nào.

## Optimize app cũ

Khi app đang chạy, đang có merchants, muốn cải thiện:

```
/research
> "App [category] của mình so với đối thủ thế nào?"
→ StoreLead: so sánh install counts, reviews, pricing vs top đối thủ
→ 1-star reviews đối thủ = cơ hội cho mình
→ Shopify API mới nào mở ra tính năng chưa ai build?

/office-hours
> "Muốn thêm [feature] vào app hiện tại"
→ Challenge: merchants nào đã yêu cầu?
→ Challenge: version nhỏ nhất ship trước là gì?
→ Challenge: có break existing merchants không?
→ Output: Design doc + backward compatibility plan

/plan-eng-review
→ Metafield namespace mới conflict với hiện tại không?
→ Thêm scope mới cần justify cho App Store review?
→ Webhook hiện tại thiếu retry — cần fix trước khi thêm feature?

/review → /qa → /ship
→ 10 Shopify rejection patterns auto-check
→ Browser thật verify feature mới (embedded app / storefront / checkout)
→ 12-item App Store Submission Checklist
```

Kết quả: feature mới dựa trên data thị trường thật, không phải đoán. Kiến trúc tính trước compatibility. App Store không reject.

## Tech stack

| Component | Công nghệ | Vai trò |
|-----------|-----------|---------|
| Runtime | **Bun** | Chạy scripts, build, test |
| Language | **TypeScript** | Toàn bộ source |
| Browser | **Playwright + Chromium** | Headless browser cho /browse, /qa |
| AI Engine | **Claude Code** | Chạy skills, đọc SKILL.md, gọi tools |
| AI SDK | **Anthropic SDK** | Cho evals và LLM judge |
| Market Data | **StoreLead API** (optional) | Data chính xác về Shopify apps + stores |
| Skills | **Markdown (.tmpl)** | Mỗi skill = 1 prompt template |

Skills là prompt templates Claude Code đọc và thực thi. Sửa behavior: edit file `.tmpl` → chạy `bun run gen:skill-docs`.

## Shopify knowledge tích hợp

11/15 skills đã train kiến thức Shopify chuyên sâu (trong CLAUDE.md + từng SKILL.md):

- **Architecture**: Theme Extension, Embedded App, Shopify Functions, Webhooks, Metafields
- **APIs**: Admin GraphQL, Storefront, Billing, Cart Transform, Checkout UI Extensions
- **App Store**: GDPR requirements, session tokens, listing rules, ASO, submission checklist
- **Market Data**: StoreLead API integration cho install counts, reviews, store-level data
- **Design**: Polaris (admin), theme-native (storefront), two-surface guidance
- **QA**: Shopify-specific test flows (embedded app, storefront widget, checkout extension)
- **Debugging**: 9 common Shopify bug patterns (session token, webhook miss, rate limit, theme compat...)
- **Native Features**: Biết Shopify đã có gì gốc để không build trùng
- **Common Pitfalls**: Rate limits, webhook reliability, theme conflicts, app conflicts

## Cài đặt

### Cho máy cá nhân
```bash
git clone https://github.com/daniel-aov/AOV-Lab.git ~/.claude/skills/aov-lab
cd ~/.claude/skills/aov-lab && ./setup
```

### Cho cả team (cài vào repo)
```bash
cp -Rf ~/.claude/skills/aov-lab .claude/skills/aov-lab
rm -rf .claude/skills/aov-lab/.git
cd .claude/skills/aov-lab && ./setup
```

Thêm vào `CLAUDE.md` của project:
```markdown
## aov-lab
Use /browse from aov-lab for all web browsing. Never use mcp__claude-in-chrome__* tools.
Available skills: /research, /office-hours, /plan-ceo-review, /plan-eng-review,
/plan-design-review, /design-consultation, /review, /ship, /qa, /qa-only, /browse,
/investigate, /design-review, /document-release, /setup-browser-cookies, /careful.
```

### StoreLead API (optional, cho /research)
```bash
# Đăng ký tại storeleads.app/api, rồi:
aov-lab-config set storeleads_api_key "YOUR_KEY"
```

## Output lưu ở đâu?

| Output | Lưu tại |
|--------|---------|
| Research reports | `~/Documents/aov-lab-research/` |
| Design docs | `~/Documents/aov-lab-research/` |
| Test plans | `~/Documents/aov-lab-research/` |

## Credit

Dựa trên [gstack](https://github.com/garrytan/gstack) v0.9.0 — MIT License.
