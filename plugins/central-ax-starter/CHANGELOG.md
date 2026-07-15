# Changelog

## 0.5.0 (2026-07-15)

- メルカリの社内数百人へのClaude Code配布事例（2026-07公開のSpeaker Deck）を反映
  - ステップ2のdenyテンプレに `Read(./.env.*)` / `Edit(./.env.*)` を追加、SSH鍵を扱うPCはユーザー設定に `Read(**/.ssh/**)` を足す旨を注記
  - ステップ3の安全重視セットに `permissions.disableBypassPermissionsMode: "disable"`（確認スキップモードの封印）を追加
  - ステップ3に「非エンジニア向け強化オプション」（`Bash(curl:*)` / `Bash(wget:*)` のdeny）を追加し、職種別に出し分け。引数条件付き許可の脆弱性とWebFetchへの一本化を注記
  - referencesに新セクション「組織展開ガイド（複数人に配るとき）」を追加（managed-settings.jsonのOS別配置パス、エンジニア/非エンジニアの安全レベル出し分け、サンドボックス設定例とWindows非対応の注記、出典）

## 0.4.0 (2026-06-12)

- first-setupを2シナリオ×2周のempiricalテストで検証・改善（最終: 両シナリオ精度100%）
  - 最初の質問を「あなたのお仕事は？」（複数選択可・経営者/経理事務/営業マーケ/エンジニア）に変更し、職種別分岐表で以降の選択肢とCLAUDE.md重点を分岐
  - セキュリティ強化: permissionsに「安全重視セット」（破壊的コマンドdeny）、CLAUDE.md追記文面にプロンプトインジェクション対策・機密の外部送信禁止を追加
  - 既存ファイルに直書きされた機密の検出→移動フローを追加
  - denyの限界（Read/Editツールのみ有効、シェル経由は素通り）の注記を追加
  - 動作確認手順のエッジ（同一セッションではdeny未反映・ステップ2スキップ時）を明文化

## 0.3.0 (2026-06-12)

- Anthropic公式ベストプラクティスに準拠
  - SKILL.md descriptionを「三人称＋具体的トリガー句＋proactive条件」形式に統一
  - SKILL.md本文をコアフローに絞り、質問セット・テンプレート・チェックリスト全文を `references/` に分割（progressive disclosure）
  - plugin.jsonに `displayName` を追加、非標準の `license: UNLICENSED` を削除
  - CHANGELOG.mdを追加
- `claude plugin validate --strict` 通過

## 0.2.0 (2026-06-12)

- first-setupの全質問をAskUserQuestion（選択式）化。タイプ不要で完走できる

## 0.1.0 (2026-06-12)

- 初版。first-setup / email-check / email-reviewer
