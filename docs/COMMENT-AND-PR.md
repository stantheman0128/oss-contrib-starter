# 留言與 PR 寫作

原則：**對齊對方語系、短、有證據、不裝維護者。**

---

## 1. 語系

| 對方怎麼寫 | 你怎麼回 |
|---|---|
| English | English |
| 繁中／簡中 | **繁體中文**（若專案是台灣／繁中社群；簡中專案可視情況用簡中） |

被 @ 到一定要回（ack + 你的立場：同意／補充／退讓）。

---

## 2. PR body 必備塊（無 template 時）

用 [templates/PR-body.md](../templates/PR-body.md)。最少包含：

1. **問題**（使用者看到什麼）  
2. **原因**（一句人話 + 關鍵程式位置）  
3. **改動**（做了什麼、刻意沒做什麼）  
4. **驗證**（真實跑過的指令與結果；沒測的誠實寫）  
5. **關聯** `Fixes #123`（若真的修該 issue）  
6. 若 CONTRIBUTING 要求：**AI 協助揭露**

避免：超長散文、無人能跑的「理論上應該好了」、隱藏 AI 卻被規範要求揭露。

---

## 3. Evidence 怎麼寫才像人

- 先寫**產品路徑／重現步驟**，再寫單元測試。  
- 貼精簡的真實輸出（可打碼 token）。  
- 「What was not tested」寫清楚，但若沒測的就是使用者會撞的路徑 → **先補測再開 PR**。

---

## 4. 常見留言情境

### 認領 issue（若專案習慣先說一聲）

見 [templates/issue-claim-comment.md](../templates/issue-claim-comment.md)。

### Maintainer 要你改

```text
Thanks — I'll update for <具體一點> and push to this branch.
```

或繁中：

```text
收到，我會依你說的 <具體一點> 改，並推到同一分支。
```

### 別人的 PR 已經超集你的

```text
Your PR covers this more completely (including <x>). Happy to close mine in favor of yours once it lands — thank you.
```

### 簡短 ack（被 @）

見 [templates/review-ack.md](../templates/review-ack.md)。

---

## 5. 風格小約束（可選但實用）

- 少用空洞形容詞（「革命性」「無縫」）。  
- 少用長破折號堆氣氛；講清楚即可。  
- 不要在別人的 repo 裡塞與本題無關的大重構。

---

## 6. AI 協助時

- 遵守該 repo 的 AI／LLM 政策。  
- 需要揭露就揭露；不要在禁止 AI 的專案硬上。  
- Agent 產出的 PR 你要能解釋每一個 diff 區塊。
