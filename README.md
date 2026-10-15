# Central AX Claude Code / Codex Plugins

Central AXが提供するプラグインのマーケットプレイス（Claude Code / Codex 両対応）。AI研修受講者・AX支援先に、推奨初期設定と業務skillを2コマンドで配布する。

## インストール（受講者向け）

### Claude Codeで使う場合

Claude Code内で以下を実行:

```
/plugin marketplace add Central-AX/claude-plugins
/plugin install central-ax-starter@central-ax
```

※ ローカル検証時はリポジトリのフォルダパスを指定する: `/plugin marketplace add "C:\path\to\central-ax-plugins"`

### Codexで使う場合

ターミナル（PowerShell等）で以下を実行:

```
codex plugin marketplace add Central-AX/claude-plugins
codex plugin add central-ax-starter@central-ax
```

2行目の代わりに、Codex CLIの中で `/plugins` と入力してプラグインブラウザを開き、`central-ax` タブから `central-ax-starter` をインストールしてもよい（1行目のマーケットプレイス追加は先に必要）。インストール後は新しいセッションを始めてから使う。

- Codexには `email-reviewer` エージェントは入らない（Codexはプラグイン内のサブエージェント定義を読み込まないため）。代わりに email-check が検査のたびに同じ指示でサブエージェントを起動する
- first-setup はCodexで実行すると、CLAUDE.md の代わりに AGENTS.md、`.claude/settings.json` の代わりに `.codex/config.toml` を設定する
- ローカル検証時はフォルダパスを指定する: `codex plugin marketplace add ./central-ax-plugins`

## プラグイン一覧

| プラグイン | 説明 |
|-----------|------|
| `central-ax-starter` | 推奨初期設定ガイド（first-setup）＋メール送信前QA（email-check + email-reviewer） |

## 構成

```
central-ax-plugins/
├── .claude-plugin/marketplace.json   # マーケットプレイス定義
└── plugins/
    └── central-ax-starter/
        ├── .claude-plugin/plugin.json
        ├── CHANGELOG.md
        ├── skills/
        │   ├── first-setup/
        │   │   ├── SKILL.md
        │   │   └── references/
        │   │       ├── setup-guide.md         # 質問セット・テンプレート集
        │   │       └── codex-setup-guide.md   # Codexで実行したときの読み替え・設定テンプレート
        │   └── email-check/
        │       ├── SKILL.md
        │       └── references/checklist.md     # C/W/Sチェックリスト完全版
        └── agents/
            └── email-reviewer.md   # Claude Code用。Codexではemail-checkがこの本文を指示にしてサブエージェントを起動
```

## 今後の追加候補

- brand-guidelinesテンプレート（自社版ブランド規範skillの雛形）
- competitor-watch雛形（競合ウォッチの汎用版）
- 通知hookのサンプル集
