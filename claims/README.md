# claims（可選）

一人單打可以不用。若你與朋友／多個 AI session 可能撞題，用檔名佔坑：

```text
claims/<owner>__<repo>__<issue>.md
```

範例：`claims/exampleorg__cooltool__123.md`

```markdown
tool: cursor
date: 2026-08-11
branch: fix/123-slug
session: my-session-id
```

做完（merged／dropped）就刪檔，並更新根目錄 `LEDGER.md`。

**規則：** 看到已有 claim → 換題，不要搶。
