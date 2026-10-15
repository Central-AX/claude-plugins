# Changelog

## 0.1.0 (2026-10-15)

- 初版
  - 安全管理: input-check（入力前の情報区分とマスキング）/ office-ai-policy（事務所AI利用規程・ツール管理台帳・職員教育記録・顧問先への説明文）/ tax-output-check + tax-reviewer（税務成果物の独立検査、税理士確認欄付き）
  - 業務: client-reply（顧問先への回答文案）/ journal-draft（領収書・請求書から仕訳候補CSV）/ meeting-note（面談記録）
  - 業務skillはすべて冒頭で input-check の原則（区分確認とマスキング）を確認する
- 見送り（次版の候補）: マイナンバーらしき12桁を入力時に検出して警告する hook。Windows・Macの両方で追加ランタイムなしに動く形式が決まっていないため
