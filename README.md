# OSS Contrib Starter（小白友善）

給想認真做開源貢獻、之後也可能申請 **OpenAI Codex for Open Source**／類似計畫的朋友用的**精簡範本**。

- 預設環境：**macOS + 繁體中文使用者**
- 語言：文件以**繁體中文**為主；PR／申請表草稿常用**英文**（國際專案慣例）
- 這不是某人的私人戰場存檔；請 fork／clone 後改成**你自己的** GitHub 帳號與 LEDGER

## 你會得到什麼

| 路徑 | 內容 |
|---|---|
| [docs/SOP-macos-zh-TW.md](docs/SOP-macos-zh-TW.md) | 從零到第一支 PR 的逐步 SOP（macOS） |
| [docs/SELECT-TICKETS.md](docs/SELECT-TICKETS.md) | 怎麼選題、moat、要避開什麼 |
| [docs/CONTRIBUTION-MAP.md](docs/CONTRIBUTION-MAP.md) | 真實案例的工程方向、貢獻深度與後續加深路線 |
| [docs/COMMENT-AND-PR.md](docs/COMMENT-AND-PR.md) | 留言與 PR 怎麼寫 |
| [docs/CODEX-OSS-APPLY.md](docs/CODEX-OSS-APPLY.md) | 申請 Codex for OSS（可整份貼給 AI agent） |
| [templates/](templates/) | PR body、認領留言、簡短 ack 範本 |
| [LEDGER.md](LEDGER.md) | 最小進度表（只記 merged／dropped 等） |
| [claims/](claims/) | 可選：避免同時做同一題 |

## 核心原則（先背這五句）

1. **只認 merged（或維護者明確收進主線）**，不認「我開了很多 PR」。  
2. **先加深、再散開**：同一個親和 repo 做好 2–3 支高品質，再換下一間。  
3. **先能重現再寫碼**：在你的 Mac 上確認 bug 還在。  
4. **有證據**：指令輸出、測過什麼、沒測什麼要誠實寫。  
5. **節流**：同一 upstream 不要同時開一堆 PR；有人留言 48 小時內回。

## 建議你怎麼開始（30 分鐘）

1. 安裝 [GitHub CLI](https://cli.github.com/)：`brew install gh` → `gh auth login`  
2. Fork 或 clone **本 repo**，改 `LEDGER.md` 開頭的名字／GitHub  
3. 讀完 `docs/SOP-macos-zh-TW.md` 與 `docs/SELECT-TICKETS.md`  
4. 跟你的 AI 說：「請依本 repo 的 SOP 幫我找 2–3 個可做的題，環境是 macOS + 繁中」  
5. 有一小叢真實 merged 後，再打開 `docs/CODEX-OSS-APPLY.md` 評估申請

## 刻意沒有放進本 repo 的東西

- 別人的 ban list、私人節流例外、多 agent 工廠腳本  
- Windows 專用工具鏈（你是 Mac；niche 請用本 repo 的 macOS + 繁中版）  
- 「保證過審」或「複製某熱門 repo 就能領 Pro」

## License

文件以 CC0 / MIT 皆可自行選；你 fork 後怎麼授權自己決定。貢獻上游時以**該專案**的 LICENSE／CONTRIBUTING 為準。

