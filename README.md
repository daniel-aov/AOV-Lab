# aov-lab

Bộ AI skills cho Claude Code, chuyên cho Shopify app. Giúp team nghĩ đúng trước khi code.

## Vấn đề gì?

**Không biết nên build gì.** Đối thủ có bao nhiêu installs? Merchants đang chê họ ở điểm nào? Pricing gap nào chưa ai lấp? Nếu không biết thì đang đoán — và đoán sai thì mất mấy tháng dev.
→ `/research` search thị trường từ 6 góc, pull data thật từ StoreLead, cho ra bảng đối thủ có số liệu + bảng cơ hội xếp hạng.

**Ý tưởng hay nhưng chưa ai challenge.** "Feature này merchants chắc cần" — nhưng bằng chứng ở đâu? Họ đang giải quyết bằng gì? Version nhỏ nhất có thể ship tuần này là gì?
→ `/office-hours` hỏi 6 câu khó, buộc nghĩ rõ trước khi code. Viết ra design doc có problem, target user, approach.

**Kiến trúc sai từ đầu.** Chọn sai cách lưu data, quên xử lý rate limits, thiếu GDPR webhooks, App Store reject.
→ `/plan-eng-review` chốt kiến trúc qua Shopify checklist 10 điểm. Mỗi quyết định có options + tradeoffs. Ra khỏi session với test plan sẵn.

**Ship rồi mới biết lỗi.** Widget không hiện trên theme X, session hết hạn sau 30 phút, billing sai currency.
→ `/review` bắt 10 lỗi Shopify hay reject. `/qa` mở browser thật click qua app. `/ship` chạy checklist 12 bước trước khi push.

Mỗi skill đọc output skill trước — research → design doc → kiến trúc → test plan → code review → QA → ship. Không bước nào bị bỏ qua.

## Điểm mạnh nhất: Research → Think → Plan

Code review, QA, ship — AI tool nào cũng làm được. Phần **nghĩ trước khi code** mới hiếm.

`/research` không phải Google rồi đọc. Search 6 góc, đọc trang thật, cross-reference, cho ra bảng cơ hội có điểm số. Khi có StoreLead API key thì pull data chính xác — số installs thật, full reviews (sort 1-star trước = pain points), stores đang dùng app nào.

`/office-hours` không phải "kể idea rồi AI khen hay." Nó hỏi ngược: bằng chứng demand ở đâu? Merchants đang giải quyết bằng gì? Nếu không trả lời được — đó là thứ cần tìm trước khi viết code.

`/plan-ceo-review` là skill mạnh nhất trong bộ. Nó không review code — nó review **quyết định**. Khi đã có plan (từ `/office-hours` hoặc tự viết), skill này đóng vai CEO/founder challenge lại toàn bộ: đây có phải đúng vấn đề cần giải không? Shopify sẽ build native cái này trong 12 tháng tới không? Merchant segment nào đang nhắm — SMB, mid-market, hay Plus? Cross-sell với 6 app hiện tại ra sao?

Bốn chế độ review tùy theo giai đoạn:
- **SCOPE EXPANSION** — feature mới, greenfield. "Nếu 10x tham vọng hơn thì trông thế nào?" Mỗi ý tưởng mở rộng đều hỏi bạn approve/defer/skip — không tự ý thêm scope.
- **SELECTIVE EXPANSION** — cải tiến app cũ. Giữ scope hiện tại làm baseline, nhưng đồng thời surface mọi cơ hội mở rộng để bạn cherry-pick từng cái.
- **HOLD SCOPE** — bug fix, refactor. Scope đã đúng, chỉ cần bulletproof — kiến trúc, security, edge cases, observability, rollback plan.
- **SCOPE REDUCTION** — plan quá lớn. Cắt tới minimum viable, tách "must ship" vs "nice to ship."

Quy trình review đi qua 10 section: architecture (vẽ dependency graph, data flow 4 đường — happy/nil/empty/error), error & rescue map (mọi method có thể fail → exception class → rescue action → user thấy gì), security & threat model, data flow tracing, test plan, observability, deployment strategy, và UI review nếu có frontend.

Mỗi section dừng lại hỏi bạn — không batch câu hỏi, không tự quyết. Mọi issue đi kèm recommendation + lý do. Nếu không có issue hoặc fix rõ ràng thì tự xử và đi tiếp, không hỏi thừa.

Tư duy đằng sau skill này lấy từ Bezos (one-way/two-way doors, Day 1 proxy skepticism), Grove (paranoid scanning), Munger (inversion), Jobs (focus as subtraction), Horowitz (wartime/peacetime), Altman (willfulness as strategy, leverage obsession). Không phải checklist rập khuôn — mà là cognitive patterns áp dụng đúng chỗ: inversion khi đánh giá kiến trúc, subtraction khi challenge scope, speed calibration khi đánh giá timeline.

Output cuối: CEO Plan file — ghi lại vision, scope decisions (accepted/deferred/skipped kèm lý do), và accepted scope. File này survive qua nhiều conversation, dùng làm source of truth cho `/plan-eng-review` tiếp theo.

`/plan-eng-review` chuyển design doc thành quyết định cụ thể. Lưu vào metafields hay database? Render bằng Liquid hay JavaScript? Mỗi quyết định có lựa chọn, đánh đổi, và gợi ý. Xong session là có kiến trúc chốt + test plan sẵn.

Ba cái này nối thành pipeline — research → design doc → CEO review → kiến trúc → test plan. Chưa viết dòng code nào mà đã biết build gì, cho ai, bằng cách nào, và đã challenge xong mọi giả định.

## 15 Skills

### Tìm hiểu & Lên ý tưởng

| Skill | Làm gì |
|-------|--------|
| `/research` | Phân tích thị trường Shopify — đối thủ có bao nhiêu installs, merchants đang chê gì, giá bao nhiêu, gap nào chưa ai lấp. Dùng StoreLead API lấy data thật. |
| `/office-hours` | Hỏi 6 câu khó trước khi build — ai cần cái này, bằng chứng ở đâu, version nhỏ nhất là gì. Viết design doc. |
| `/plan-ceo-review` | **Skill mạnh nhất.** CEO-mode challenge toàn bộ plan — đúng vấn đề chưa, Shopify sẽ build native không, merchant segment nào, cross-sell ra sao. 4 chế độ (Expansion/Selective/Hold/Reduction), 10 section review, vẽ architecture + error map + threat model. Lưu CEO Plan file làm source of truth. |

### Thiết kế & Kiến trúc

| Skill | Làm gì |
|-------|--------|
| `/plan-eng-review` | Chốt kiến trúc trước khi code — lưu data ở đâu, xử lý rate limits thế nào, GDPR cần gì, theme nào cần test. Cho ra test plan. |
| `/plan-design-review` | Chấm điểm thiết kế — admin UI có đúng chuẩn Shopify không, storefront widget có đẹp trên mobile không, onboarding có dễ không. |
| `/design-consultation` | Tạo hệ thống thiết kế — admin theo chuẩn Polaris, storefront theo theme merchant, listing theo brand riêng. |

### Code & Review

| Skill | Làm gì |
|-------|--------|
| `/review` | Review code — bắt 10 lỗi Shopify hay reject: thiếu GDPR hooks, dùng cookies sai, quên xử lý rate limit, widget nặng quá 50KB. |
| `/investigate` | Debug có hệ thống — không fix lung tung mà tìm nguyên nhân gốc trước. Biết sẵn 9 lỗi Shopify hay gặp. |
| `/design-review` | Kiểm tra giao diện trên site thật — widget có đẹp không, có bị vỡ trên mobile không, có xung đột theme không. Sửa luôn. |

### Test & Ship

| Skill | Làm gì |
|-------|--------|
| `/qa` | Mở browser thật, click qua app, tìm lỗi, sửa, verify lại. Tự nhận biết đang test admin app, storefront widget, hay checkout. |
| `/qa-only` | Như `/qa` nhưng chỉ báo lỗi, không sửa. |
| `/browse` | Điều khiển browser — click, screenshot, check responsive. Nhanh (~100ms/lệnh). |
| `/ship` | Push code lên — tự chạy tests, review diff, check 12 bước trước khi submit App Store. |
| `/document-release` | Cập nhật docs cho khớp với code vừa ship. |

### Tiện ích

| Skill | Làm gì |
|-------|--------|
| `/careful` | Cảnh báo trước khi chạy lệnh nguy hiểm (xóa file, force push...). |
| `/setup-browser-cookies` | Import cookies từ Chrome/Arc/Brave để test trang cần đăng nhập. |

## Research app mới

```
/research
> "Thị trường [category] trên Shopify — đối thủ, pricing, gaps"
→ Data thật: install counts, reviews, pricing từng app
→ Pain points merchants (từ forums, reddit, 1-star reviews)
→ Gap analysis + opportunity scoring

/office-hours
> "Muốn build app [idea] dựa trên research"
→ Bằng chứng demand ở đâu?
→ Shopify API hỗ trợ không?
→ Output: Design doc

/plan-eng-review
→ Chốt kiến trúc với Shopify checklist
→ Test plan sẵn cho mọi flow
```

## Optimize app cũ

```
/research
> "App [category] của mình so với đối thủ thế nào?"
→ So sánh installs, reviews, pricing vs đối thủ
→ 1-star reviews đối thủ = cơ hội cho mình
→ Shopify API mới mở ra tính năng gì?

/office-hours
> "Muốn thêm [feature] vào app hiện tại"
→ Merchants nào yêu cầu? Bao nhiêu tickets?
→ Version nhỏ nhất ship trước?
→ Có break merchants đang dùng không?

/plan-eng-review
→ Data mới conflict với data cũ không?
→ Cần thêm quyền gì? App Store có chấp nhận không?
→ Xử lý lỗi hiện tại cần fix trước không?

/review → /qa → /ship
→ Check 10 lỗi Shopify hay reject
→ Test trên browser thật
→ Check 12 bước trước khi submit
```

## Tech stack

| Component | Công nghệ | Vai trò |
|-----------|-----------|---------|
| Runtime | Bun | Chạy scripts, build, test |
| Language | TypeScript | Toàn bộ source |
| Browser | Playwright + Chromium | Browser cho /browse, /qa |
| AI Engine | Claude Code | Chạy skills |
| Market Data | StoreLead API (optional) | Data Shopify apps + stores |
| Skills | Markdown (.tmpl) | Mỗi skill = 1 file prompt |

Muốn sửa skill: edit file `.tmpl` → chạy `bun run gen:skill-docs`.

## Cài đặt

```bash
git clone https://github.com/daniel-aov/AOV-Lab.git ~/.claude/skills/aov-lab
cd ~/.claude/skills/aov-lab && ./setup
```

Cho cả team (cài vào repo):
```bash
cp -Rf ~/.claude/skills/aov-lab .claude/skills/aov-lab
rm -rf .claude/skills/aov-lab/.git
cd .claude/skills/aov-lab && ./setup
```

Thêm vào `CLAUDE.md` của project:
```markdown
## aov-lab
Use /browse from aov-lab for all web browsing.
Available skills: /research, /office-hours, /plan-ceo-review, /plan-eng-review,
/plan-design-review, /design-consultation, /review, /ship, /qa, /qa-only, /browse,
/investigate, /design-review, /document-release, /setup-browser-cookies, /careful.
```

StoreLead API (optional):
```bash
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
