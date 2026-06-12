# Central AX Claude Code Plugins

Central AXが提供するClaude Codeプラグインのマーケットプレイス。AI研修受講者・AX支援先に、推奨初期設定と業務skillを2コマンドで配布する。

## インストール（受講者向け）

Claude Code内で以下を実行:

```
/plugin marketplace add JunP1ayer/claude-plugins
/plugin install central-ax-starter@central-ax
```

※ ローカル検証時はリポジトリのフォルダパスを指定する: `/plugin marketplace add "C:\path\to\central-ax-plugins"`

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
        │   │   └── references/setup-guide.md   # 質問セット・テンプレート集
        │   └── email-check/
        │       ├── SKILL.md
        │       └── references/checklist.md     # C/W/Sチェックリスト完全版
        └── agents/
            └── email-reviewer.md
```

## 今後の追加候補

- brand-guidelinesテンプレート（自社版ブランド規範skillの雛形）
- competitor-watch雛形（競合ウォッチの汎用版）
- 通知hookのサンプル集
