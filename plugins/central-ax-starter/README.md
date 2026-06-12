# central-ax-starter

Central AX推奨のClaude Code初期セットアップを1コマンドで配布するプラグイン。AI研修受講者・AX支援先の「最初に何を設定すればいいか分からない」を解消する。

## 同梱内容

| 種類 | 名前 | 役割 |
|------|------|------|
| skill | `first-setup` | CLAUDE.md作成・機密情報の隔離・permissions・外部skill安全確認を対話形式でセットアップ |
| skill | `email-check` | 営業・顧客向けメールの送信前QA（C/W/S重大度制チェックリスト） |
| agent | `email-reviewer` | email-checkから委譲される独立レビュアー。書き手に忖度しない検査役 |

## 使い方

インストール後、Claude Codeで:

- 「初期設定して」 → first-setupが起動
- 「このメール送信前にチェックして」 → email-checkが起動

## バージョン

[CHANGELOG.md](./CHANGELOG.md) を参照。
