> 本檔屬於 **oss-contrib-starter**。請搭配 [SOP-macos-zh-TW.md](SOP-macos-zh-TW.md) 與 [SELECT-TICKETS.md](SELECT-TICKETS.md)。環境預設為 **macOS + 繁體中文**（特化請自建，勿抄別人的 Windows 票）。

# Codex for Open Source — 給朋友的 AI Agent Playbook

把**整份文件**貼給朋友的 AI agent。目標：協助使用者評估並申請 OpenAI **Codex for Open Source**（通過後通常是 **6 個月 ChatGPT Pro，含 Codex**）。  
本文件**不要求**複製任何人的 repo 或票種；只要求：選對專案、有真實貢獻、誠實填表。

**官方入口（以當下頁面為準）：**  
https://developers.openai.com/community/codex-for-oss  
申請表常見路徑：`https://openai.com/form/codex-for-oss/`（或地區語系變體）

---

## Agent 任務（請照做）

1. 用使用者的 GitHub，**串行**查證其公開貢獻（勿平行狂打 `gh search`）。  
2. 對照下方「門檻與充足程度」，判斷：已可申請／先補貢獻再申請／不建議硬送。  
3. 若可申請：幫選 **1 個主打 public repo URL**，起草英文表單欄位（角色、why qualify、API credits 用途、Other info）。  
4. 誠實放大貢獻，**禁止**教使用者謊稱 owner／write access／PMC。  
5. 產出：短評分 + 建議主 repo + 可貼上的英文草稿 + 若未達標的 2–4 週行動清單。

---

## 計畫在幹嘛（給 agent 的背景）

- OpenAI 文案偏「**主要／核心維護者**」：PR review、issue 分流、發版、品質。  
- 實務上是 **discretionary（人工裁量）**：表單常只有 Primary / Core maintainer 二選一，但公開案例顯示 **非 owner、純貢獻者也能過**。  
- 官方也說：不完全符合仍可申請並說明生態系重要性（apply anyway 精神）。  
- 審核常要數週；有人約 1 個月，也有更快。**無保證、無公開星數硬門檻。**  
- 福利：6 個月 ChatGPT Pro（含 Codex）；可能另有 API credits／Codex Security（以錄取信為準）。

---

## Criteria：要做到什麼程度才「夠格一試」？

沒有官方「必須 N 個 PR」。用**可核對的充足程度**分三檔：

### A. 明顯夠格（優先申請）

滿足任一即可當主敘事：

| 路徑 | 充足程度（實務） |
|---|---|
| **維護者** | 你是該 public repo 的 owner／core team，或有 write；專案有明顯使用量（stars／下載／依賴／誰在用） |
| **高訊號專案上的真實貢獻者** | 主打一個**別人一看就懂為什麼重要**的 public repo + 上面有**一小叢已進主線**的貢獻（公開案例有人約 **~7** 支進主線就過；有人更多）。貢獻最好是**同一 subsystem**，附測試／證據，不是 typo 農場 |
| **生態例外** | 專案體量一般，但你能說清「誰依賴它、缺了會怎樣」，且你有持續維護／貢獻證據 |

### B. 灰色（可送，預期可能沒回音）

- 主打 repo 很紅，但你只有 1–2 個淺 PR，或幾乎無關的 docs。  
- 自己的小專案、star／下載都薄，又講不出生態依賴。  
- 貢獻很散、沒有「我在維護某一面」的故事。

### C. 先別送（先做事）

- 主打 URL 上**沒有**可核對的 merges／landed commits。  
- 打算填熱門 repo 但自己零貢獻。  
- 想開小號／多 ChatGPT 帳號刷第二次福利。

**經驗法則（給 agent 轉述使用者）：**  
寧可 **1 個高訊號 repo × 5–10 個紮實、相關、已合併的 PR**，也不要 **20 個 repo × 各 1 個薄 PR**。專案重要性權重往往高於「PR 顆數神話」。

---

## 不要做什麼

- 不要叫使用者去刷同一個我們用過的 repo（例如特定熱門 AI 工具）擠同一條線。  
- 不要建議謊稱 Primary maintainer／有 admin。  
- 不要建議空殼 repo、買星、或偽造下載。  
- 不要建議「已經拿到 6 個月 Pro 的人立刻再申請第二份」或開第二帳號加倍。  
- 不要把 GitHub contribution 綠格子當資格（計畫看的是 **merged／維護證據／生態重要性**）。

---

## 怎麼做（可遷移步驟，與具體 repo 無關）

### Step 1 — 盤點使用者資產

用 `gh`（串行）列出：

- 近 12 個月 `author:<user> type:pr is:merged`  
- 依 **非自有** repo 分組計數  
- 每個候選 repo：stars、大致下載／依賴（npm／PyPI／crates 若有）、使用者在該 repo 的 PR 標題主題是否成「一條線」

### Step 2 — 選「表單上那一個」GitHub 程式碼庫 URL

評分優先序：

1. **生態訊號**（stars／下載／下游依賴／是否在熱門工作流裡）  
2. **使用者在該 repo 的貢獻深度與連貫性**  
3. **故事是否好講**（「我維護 X 的 Y 面」一句話）

輸出 1 個主打 + 1 個備選。Other info 可提第二個 repo 當佐證，但 URL 只填一個主打。

### Step 3 — 決定角色勾選

- 若使用者是 owner／正式 core：可勾對應維護者角色。  
- 若只是外部貢獻者：勾 **Core maintainer** 時，**必須在自由欄寫明非 owner**，並把範圍鎖在「某 subsystem 的持續維護貢獻」。不誠實就勾 Primary。  
- 若表單有更細選項，選最不誇大的那項。

### Step 4 — 起草英文欄位（建議結構）

**Why this repository qualifies（生態）：**  
stars／下載／誰用、解決什麼缺口、為何對生態重要。

**How you will use API credits / Pro（用途）：**  
對準該專案的真實維護：重現 bug、補測、修 CI、分流、發版自動化——不要空話。

**Other info（為什麼選這個人）：**  
獨特能力／niche／貢獻模式（加深同一 class，而非散彈）。若有難 repro 的環境優勢（例如特定 OS／語系），寫清楚。

### Step 5 — 送出與等待

- 用與 ChatGPT 帳戶綁定的 email。  
- GitHub profile 設公開。  
- 送出後等待郵件；不催。  
- 已獲批准：分享誠實經驗可以；不要教人鑽漏洞再領一次。

---

## 可複製的「充足貢獻」配方（讓使用者自己選戰場）

請 agent 依使用者日常技術棧挑**一種**，不要照抄別人的專案：

| 配方 | 做什麼 | 何時夠一試 |
|---|---|---|
| **熱門專案加深**（預設） | 在使用者已會用的 1 個高星／高下載 upstream，修同一類 bug 5–10 次並合併 | 主線有一小叢 merged + 能講清 subsystem |
| **自有套件維護** | 把真正有人用的 library 養到可出示下載／依賴 | 接近官方維護者敘事（下載／dependents） |
| **利基環境 moat** | 專修多數人無法重現的環境（特定 OS、語系、編碼、CI） | 有 live repro + 多個同 class merged |

**不要**把「先去當 Apache PMC／CNCF committer」當路徑——那對一般想申請的朋友不實際，也不是本 playbook 的目標。有正式 write／core team 只是加分，不是前提。

---

## 與 Anthropic Claude for Open Source 的差別（避免搞混）

| | OpenAI Codex for OSS | Anthropic Claude for OSS |
|---|---|---|
| 福利 | 常為 6 個月 ChatGPT Pro + Codex | 常為 6 個月 Claude Max 20x |
| 門檻風格 | 偏維護者文案 + **裁量**；無公開「100 PR」硬數 | 有較清楚的多 track（含 **12 個月 100+ 非自有 merged** 等） |
| 策略 | 高訊號 repo + 真實貢獻叢集 往往較關鍵 | 衝非自有 merged 數字或套件下載／基金會身分較對口 |

兩者可平行；**不要**用同一套「假裝 PMC」話術兩邊騙。

---

## Agent 回覆使用者時的固定格式

```text
## 評估
- 建議：申請 / 先補再申請 / 不建議
- 主打 repo：<url> （理由一句）
- 充足程度：A / B / C（對照本文件）
- 風險：<一句誠實風險>

## 表單草稿（英文）
- Role: ...
- Why qualify: ...
- Credits / usage: ...
- Other info: ...

## 若尚未達標：2–4 週行動
1. ...
2. ...
3. ...
```

---

## 免責

錄取由 OpenAI 全權決定。本 playbook 來自社群公開案例與貢獻者經驗整理，**不是**官方保證、也不是教你規避條款。使用者須自行閱讀並同意當下的 Program Terms。
