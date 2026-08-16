# 真實貢獻地圖：從跨 repo 修補到 subsystem 深耕

> 更新日期：2026-08-17  
> GitHub：[`stantheman0128`](https://github.com/stantheman0128)

這份筆記不是把 62 筆 PR 排成戰績表，而是回答三個比較有用的問題：

1. 這些跨 repo 貢獻其實集中在哪些工程方向？
2. 哪幾筆已經能當成完整 case study，而不只是「修過一個 bug」？
3. 下一步怎麼從大量修補，走向對一個 subsystem 有持續 ownership？

## 統計口徑

截至 2026-08-17，GitHub 搜尋可重建出：

- **62 筆 merged PR**
- **21 個非本人名下的 external repositories**
- **18 個 upstream owners**
- 其中 37 筆集中在三個 repo：`taiwan-md` 15、`claude-mem` 14、`OpenWhispr` 8

搜尋口徑：[`author:stantheman0128 is:merged -user:stantheman0128`](https://github.com/search?q=type%3Apr+author%3Astantheman0128+is%3Amerged+-user%3Astantheman0128&type=pullrequests)。

這個數字只代表「已合併的外部 PR」，不代表 62 筆具有相同難度。Open、closed-unmerged、自有 repo、本人 fork 都另外處理。

## 貢獻方向

| 方向 | 代表 repo／案例 | 反覆處理的問題 |
|---|---|---|
| Windows 與跨平台可靠性 | ToolHive、claude-mem、OpenWhispr、taiwan-md、ArozOS、OpenClaw | ACL、owner、路徑、編碼、process spawn、PID／lock、Windows timing |
| Protocol、lifecycle 與 concurrency | Jupyter MCP、Flyte、ToolHive、claude-mem、Zoraxy | keepalive、timeout、取消、輸出順序、requeue、heartbeat、backpressure、redirect loop |
| AI／ML correctness | Microsoft SkillOpt、LyCORIS、sherpa-onnx | train/test leakage、tensor shape、token layout、evaluation contract |
| Trading systems bridge | BBGO | 啟動副作用、notifier／OTP lifecycle、exchange metadata 與 market constraints |
| 繁中、i18n 與資料規則 | taiwan-md、OpenCC、OpenWhispr | CJK filename、cp950、Git quotePath、繁簡邊界、language-scope SSOT |

這些工作看起來分散，實際上有一個共同點：多數 bug 都出現在「單一 happy path 看不見，但跨平台、跨元件或狀態改變後會壞」的邊界。

## 已經做得比較深的案例

### 1. ToolHive #5951：Windows discovery trust boundary

- PR：[#5951](https://github.com/stacklok/toolhive/pull/5951)
- 狀態：merged
- 規模：10 files、`+1097/-11`、7 commits、11 個新測試
- Review：4 輪 `CHANGES_REQUESTED` 後 approved；最終 43 checks success、1 skipped

初版已經有 protected DACL、Current User＋SYSTEM 權限與 adversarial `icacls` 測試，但只保護 leaf，而且 hardening 發生在 lock／discovery 之後。Reviewer 進一步指出 ancestor replacement、hostile owner、upgrade integrity、雙 server record deletion 與 POSIX regression 等問題。

最後由本人把每輪 review 落成：pre-trust hardening、full-chain directory protection、owner pre/post validation、read-path owner check，以及 lock-scoped upgrade reconciliation。這筆最有價值的地方不是「第一次就設計對」，而是能在四輪 security review 中逐步修正 threat model，最後把修正留進 production code 與 regression tests。

同 repo 的 [#5954](https://github.com/stacklok/toolhive/pull/5954) 則從共用 SSE writer 修正 `event: message` framing，涵蓋 POST／GET、progress 與 error paths。它是中型、乾淨的 transport interoperability 案例。

### 2. Jupyter MCP #309：長時間執行不是只調高 timeout

- PR：[#309](https://github.com/datalayer/jupyter-mcp-server/pull/309)
- 狀態：merged
- 規模：7 files、`+633/-108`、4 commits
- 驗證：15 個跨 Ubuntu／macOS／Windows、Python 3.10／3.13 checks 通過

原 issue 看起來只是「cell 跑超過三分鐘，client timeout」。實際上 Jupyter execution timeout 與 MCP client idle timeout 是兩層不同問題。修正因此跨過 FastMCP progress/logging、server execution paths、`asyncio.to_thread` cancellation、kernel interrupt、settle window、late output 與 structured／multimodal output fidelity。

Review 後又處理了 Windows coarse clock、重複 interrupt、stream output ordering，以及 upstream execution path 改寫後的多次 rebase。這筆適合代表 cross-layer lifecycle reasoning。

### 3. Flyte #7770：設定值背後還有 lease invariant

- PR：[#7770](https://github.com/flyteorg/flyte/pull/7770)
- 狀態：merged
- 規模：7 files、`+118/-12`、4 commits
- 驗證：21 個 checks 通過

表面需求只是把 TaskAction controller 固定 10 秒的 requeue interval 改成可設定。真正較深的部分，是追到 cache reservation heartbeat 原本也依賴相同的 10 秒假設；如果只改 polling，reservation 可能在下次 reconcile 前失效。

因此 patch 同步處理 controller 與 cache-service 的 heartbeat coupling，同時保留 deadline path 的不同語意，也公開留下 service clamp／grace window 的限制。這是「小 patch、跨元件 invariant 很深」的代表。

### 4. Flyte #7787：目前 open，但技術上最深

- PR：[#7787](https://github.com/flyteorg/flyte/pull/7787)
- 狀態：open／under review，不計入 62 merged
- 規模：2 files、`+657/-81`；約 450 行測試

這筆把 Postgres NOTIFY pump 從 blocking buffered channel 改成 pending-set＋non-blocking signal，處理 coalescing、context cancellation、retry merge-back、connection backoff、poison payload 與 concurrent writers，並跑過 `go test -race` 與 100k-action workload。

目前最重要的 follow-up 是把 `execNotify` 的 bool 改成 `delivered / connection-failure / payload-failure` 明確結果型別，避免 reconnect 成功後把 connection error 誤算成 payload retry；另外需要同機 main／branch paired benchmark、真實 connection failover test，以及 outage 下的 pending-set size／age metrics。

若這筆合併，它會是目前最完整的 concurrency／backpressure case study。

### 5. claude-mem #3294：slot reservation 修掉 check-then-act race

- PR：[#3294](https://github.com/thedotmack/claude-mem/pull/3294)
- 狀態：merged

`waitForSlot()` 原本只看 process registry；多個 caller 在 process 真正註冊前會同時通過檢查，使 max=2 仍可放行多個 agent。修正以同步 reservation 填補 spawn 前的時間窗，再由 registry 接手 accounting；release 是 idempotent，另有 finally safety net 防止 OAuth／abort／spawn failure 洩漏 slot。

這是 `claude-mem` 14 筆中最適合做深案的一筆。下一步應補 fairness、queued cancellation、timeout、property/stress test 與真 process integration，而不是繼續只修 console window 類的小洞。

### 6. Zoraxy #1239：真人 review 改變 redirect threat model

- PR：[#1239](https://github.com/tobychui/zoraxy/pull/1239)
- 狀態：merged

修正 host-only SSO URL 造成 nested redirect loop，處理 URL normalization、query preservation、same-host guard 與 loop unwrap。Maintainer 要求修正 `X-Forwarded-Proto` 大小寫／multi-hop 與 hard-coded HTTPS 後才 approved。

它的價值在於 review 真正改變設計。可再往 proxy trust boundary、open redirect、multi-proxy E2E 與 URL fuzzing 加深。

### 7. Microsoft SkillOpt #165：ML evaluation leakage

- PR：[#165](https://github.com/microsoft/SkillOpt/pull/165)
- 狀態：merged

修正 all-test batch 經 fallback 混入 train／validation 的 held-out leakage，讓 test tasks 維持隔離並補 split regression tests。Patch 不大，但和量化、ML evaluation 的關聯很高。

下一步可以補 split invariant／property tests、完整 overnight pipeline、dataset provenance，以及 leakage 對 benchmark KPI 的實際影響。

## 「原來還有 BBGO」：兩筆做了什麼

BBGO 有兩筆 merged PR，適合合成一個 trading systems bridge，不宜拆成兩個大型 headline。

### #2566：未設定 notifier 時不應初始化 OTP／persistence

- PR：[#2566](https://github.com/c9s/bbgo/pull/2566)
- 深度：中

沒有 Telegram／Slack 時，啟動流程仍會初始化 interaction／OTP 並碰 persistence，壞掉時甚至會在 strategy／webserver 尚未啟動前 fatal。修正讓 interaction setup 只在有相應 notifier 時進入，並補 no-notifier 與正常 OTP persistence regression tests。

可以加深成完整的 notifier matrix、persistence-unavailable integration test、Docker repro，以及「啟動時不得產生無關副作用」的 contract test。

### #2567：Binance futures market-constraint regression

- PR：[#2567](https://github.com/c9s/bbgo/pull/2567)
- 深度：中偏低

真正 live parsing 問題先前已由 dependency upgrade 修好；這筆貢獻是以 futures `MIN_NOTIONAL` fixture 鎖住 conversion，測 missing-filter path，並為 zero `MinNotional`／`MinQuantity` 增加 diagnostics。

可以加深為完整 raw `exchangeInfo` decode fixture、decimal／schema variants、warning assertions，以及一路連到 order sizing／validation，決定零值時應 fail closed 還是只警告。

## 三個已形成的 contribution clusters

### taiwan-md（15 merged）

已形成 Windows＋繁中內容 pipeline 的連續工作線：cp950 console、UTF-8 file I/O、CJK filenames、Git `core.quotepath`、pre-commit gates、translation status 與 language-scope SSOT。

下一步不應再逐支 script 補同一個 `encoding="utf-8"`，而應：

- 抽共用 console／path／encoding helper。
- 把剩餘七個各自維護語言範圍的 guard 收斂。
- 加 Windows／Linux CI matrix 與 full-corpus parity。
- 用 architecture PR 取代更多同類微修。

### claude-mem（14 merged）

主題集中在 Windows worker／supervisor reliability：process spawn、path quoting、BOM、env preservation、worktree key、concurrency slots。

下一步應選定 supervisor lifecycle 為 subsystem，補真 process、restart、cancellation、fairness、queue pressure 與 metrics，讓多筆修補收斂成一套可維護的狀態模型。

### OpenWhispr（8 merged）

主題集中在 Windows desktop/audio runtime：process window、binary lookup、非 ASCII path、WASAPI helper capability、繁中 STT script。

下一步應建立共用 Windows process-runner／cache-path abstraction，並加入真實 non-ASCII Windows profile、installed app、recording／AEC E2E，而不只是 unit-level 模擬。

## 接下來最值得加深的順序

1. **先把 Flyte #7787 推過 merge**：技術深度最高，也已經有完整實作；優先修 retry-result 語意、paired benchmark、connection failover 與 outage metrics。
2. **在 ToolHive 同一條線再做一筆**：[#6104](https://github.com/stacklok/toolhive/issues/6104) 延伸 SSE transport；[#6230](https://github.com/stacklok/toolhive/issues/6230) 延伸 least-privilege RBAC；[#6332](https://github.com/stacklok/toolhive/issues/6332) 是最深的 authz／streaming security 題。先留言 claim、等待 assignment。
3. **把 BBGO 做成一小叢**：不要再挑隨機交易 bug；固定在 startup lifecycle 或 exchange contract／order validation，累積 3–5 筆相鄰修復。
4. **把現有大叢集升級成 architecture work**：taiwan-md 做跨平台 helper＋CI、claude-mem 做 supervisor state machine、OpenWhispr 做 Windows runtime abstraction。
5. **Jupyter 回補 live product path**：真實 kernel 跑 3–10 分鐘、實際 MCP client、client cancellation／disconnect、server shutdown 與同 kernel 並行 execution。

## 怎麼判斷一筆貢獻真的「變深」

不是看 diff 大小，而是看它有沒有逐步增加以下證據：

- 從 symptom 追到跨層 root cause。
- Patch boundary 涵蓋所有同類 path，而不只 issue 點名的一行。
- 有 pre-fix red → post-fix green，最好再有真實 product path／E2E。
- Review 不只是 LGTM，而是真的改變 threat model、invariant 或設計。
- 主動寫出沒測到的東西與 residual risk。
- 下一筆貢獻能減少前幾筆重複修補，例如抽 abstraction、補 CI 或 telemetry。

## 一個不該忘記的 closed 案例

ToolHive [#5955](https://github.com/stacklok/toolhive/pull/5955) 技術方向合理，也補了 real middleware path tests，但因沒有先 claim／等待 assignment、DCO 缺失，而且 scope 漏掉 webhook 同類路徑而被關閉。後來由 maintainer 的 #6055 完整修掉。

它不算 merged contribution，卻很適合留下作為流程教訓：深度不只有 code；先確認 ownership、涵蓋完整 scope，以及遵守 upstream contribution process，同樣是工程工作的一部分。

## AI assistance 與 ownership

部分 PR 公開揭露使用 Cursor、Claude Code 或 Codex 協助。Case study 應把責任拆清楚：

- AI 協助探索、草稿或測試生成了什麼。
- 本人如何重現、選擇 patch boundary、設計測試與回應 review。
- 哪個 AI／本人初始假設後來被測試或 reviewer 推翻。
- 最終哪些限制仍未解決。

這比隱藏 AI assistance 更能證明 ownership。

