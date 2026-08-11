# SOP：macOS + 繁體中文（從零到 PR）

適用：第一次認真向上游開 PR 的人。預設 shell 為 **zsh**（macOS 預設）。

---

## 0. 心態

- 目標是 **merged**，不是綠格子、不是 PR 數量。  
- 你的優勢應是：**你能重現、別人懶得重現**的問題（見 [SELECT-TICKETS.md](SELECT-TICKETS.md)）。  
- AI 可以幫寫碼與起草，但**重現、測試、對齊 upstream 規範**必須你（或 agent 在你機器上）真實跑過。

---

## 1. 環境安裝（一次做完）

```bash
# Homebrew（若尚未安裝）：https://brew.sh
brew install git gh node   # node 視目標專案而定；Go/Python 專案另裝

gh auth login
# 選 GitHub.com → HTTPS → 瀏覽器登入

git config --global user.name "你的名字"
git config --global user.email "你的GitHub信箱"
```

建議再設：

```bash
git config --global init.defaultBranch main
git config --global pull.rebase false
```

檢查：

```bash
gh auth status
git --version
```

---

## 2. 本 starter 的用法

```bash
cd ~/Projects   # 或你喜歡的目錄
git clone <本-repo-的-url> oss-contrib-starter
cd oss-contrib-starter
```

編輯 `LEDGER.md`：填你的 GitHub 與開始日期。  
之後每做一題就加一列。

**可選認領：** 若你與朋友／多個 agent 並行，用 `claims/`（見該目錄 README）。一人單打可先跳過。

---

## 3. 選一個 upstream（第一季只養 1–2 間）

好的第一間通常具備：

- 你**每天或每週會用到**  
- 接受外部 PR（看最近 10 筆外部 merged 有沒有在動）  
- CONTRIBUTING／AI 政策允許你用的工具（有的專案禁止 LLM，就換一間）  
- 你在 Mac 上能跑它的測試或最小 repro

不要第一週就同時開五間。

---

## 4. 選題（開碼前必做）

完整清單見 [SELECT-TICKETS.md](SELECT-TICKETS.md)。最短版：

1. Issue 是否還在？在 **目前預設分支 HEAD** 重現。  
2. 有沒有人已經開了涵蓋同一問題的 PR？（`gh pr list` + 搜關鍵字，**一次一個搜尋**）  
3. 你能否解釋：症狀的每一點，是否都被你的假設覆蓋？  
4. 這題是「邀請你做的小修」還是「你自己去擠 good-first-issue 紅海」？後者慎選。

通過才進入下一步。

---

## 5. 工作目錄建議（worktree）

不要在 upstream 的 `main` 上直接改。範例：

```bash
# 假設你已 clone upstream 為唯讀 origin
cd ~/src/some-upstream
git fetch origin
git worktree add ../some-upstream-wt/fix-123-slug -b fix/123-slug origin/main
cd ../some-upstream-wt/fix-123-slug
```

Fork 後：

```bash
gh repo fork <owner>/<repo> --clone=false
git remote add fork https://github.com/<你的帳號>/<repo>.git
```

---

## 6. 開發迴圈

1. **最小失敗重現**（指令或步驟寫下來）  
2. 若有測試框架：先寫／改一個會紅的測試，再修到綠  
3. 跑專案文件要求的 lint／test（不要自稱跑過卻沒跑）  
4. `git add` **明示路徑**（不要習慣性 `git add -A` 把垃圾帶進去）  
5. Commit 訊息跟該 repo 近期風格（conventional commits 很常見）

macOS 注意：

- 檔名大小寫：預設磁碟常是 case-insensitive，Linux CI 可能 case-sensitive。  
- 不要依賴只有你機器上的絕對路徑。  
- 密鑰、`.env`、個資不要進 commit。

---

## 7. 開 PR 前檢查

- [ ] 仍無覆蓋中的別人 PR  
- [ ] 讀過 CONTRIBUTING 與 PR template  
- [ ] 若專案要求 AI 揭露／Evidence，已照做  
- [ ] PR 標題清楚；body 用 [templates/PR-body.md](../templates/PR-body.md) 改寫  
- [ ] 「Allow edits from maintainers」通常保持開啟  

```bash
git push -u fork HEAD
gh pr create --repo <owner>/<repo> --fill
# 或貼上準備好的 title/body
```

---

## 8. PR 開了之後

- CI 紅了：先分清是你的 diff 還是 fork 權限／flaky。  
- Maintainer／審查者留言：**同語系回覆**（對方英文你回英文；中文則繁中），48 小時內盡量回。  
- 需要改就推同一分支，不要再開第二支重複 PR。  
- Merged → 更新 `LEDGER.md`。被拒／放棄 → 寫原因（dropped），這也是有效結果。

---

## 9. 節流（保護你自己的名聲）

| 規則 | 建議 |
|---|---|
| 同一 repo 同時 open | 最多 2–3 支 |
| 同一天新開 PR | 少而精；間隔數小時以上較安全 |
| 有未回的人類 review | 先回完再開新題 |
| 被要求不要再丟 | 立刻停，換專案 |

---

## 10. 跟 AI agent 怎麼協作

把本 repo 當「專案說明」打開，然後說：

> 請嚴格遵守 docs/SOP-macos-zh-TW.md 與 docs/SELECT-TICKETS.md。  
> 我的環境是 macOS + 繁體中文。先幫我選題，不要直接開 PR。  
> 所有 gh 搜尋請串行。

申請 Codex 時，改貼整份 [CODEX-OSS-APPLY.md](CODEX-OSS-APPLY.md)。

---

## 11. 常見失敗

| 失敗 | 解法 |
|---|---|
| 修了已經被修掉的 issue | 開碼前在 HEAD 重現 |
| PR 太薄，被人超集後關掉 | 同一狀態機的相鄰失敗一起覆蓋 |
| 一天開超多 PR | 節流；品質 > 數量 |
| 只改文件卻宣稱大功 | 誠實標 S；福利計畫通常要實作貢獻 |
| Mac 綠、CI 紅 | 看大小寫、路徑、未追蹤的本地設定 |
