# Central AX Claude Code Plugins

Central AXが提供するClaude Codeプラグインのマーケットプレイス。AI研修受講者・AX支援先に、推奨初期設定と業務skillを2コマンドで配布する。

## インストール（受講者向け）

Claude Code内で以下を実行:

```
/plugin marketplace add Central-AX/claude-plugins
/plugin install central-ax-starter@central-ax
```

※ ローカル検証時はリポジトリのフォルダパスを指定する: `/plugin marketplace add "C:\path\to\central-ax-plugins"`

## プラグイン一覧

| プラグイン | 説明 |
|-----------|------|
| `central-ax-starter` | 推奨初期設定ガイド（first-setup）＋メール送信前QA（email-check + email-reviewer） |
| `central-ax-zeirishi` | 税理士事務所パック。安全管理（input-check / office-ai-policy / tax-output-check + tax-reviewer）＋業務skill（client-reply / journal-draft / meeting-note） |

## 構成

```
central-ax-plugins/
├── .claude-plugin/marketplace.json   # マーケットプレイス定義
└── plugins/
    ├── central-ax-starter/
    │   ├── .claude-plugin/plugin.json
    │   ├── CHANGELOG.md
    │   ├── skills/
    │   │   ├── first-setup/
    │   │   │   ├── SKILL.md
    │   │   │   └── references/setup-guide.md   # 質問セット・テンプレート集
    │   │   └── email-check/
    │   │       ├── SKILL.md
    │   │       └── references/checklist.md     # C/W/Sチェックリスト完全版
    │   └── agents/
    │       └── email-reviewer.md
    └── central-ax-zeirishi/
        ├── .claude-plugin/plugin.json
        ├── CHANGELOG.md
        ├── README.md
        ├── skills/
        │   ├── input-check/          # 入力前の情報区分・マスキング
        │   ├── office-ai-policy/     # 規程・ツール管理台帳・教育記録・顧問先への説明文
        │   ├── tax-output-check/     # 税務成果物の検査（tax-reviewerに委譲）
        │   ├── client-reply/         # 顧問先への回答文案
        │   ├── journal-draft/        # 仕訳候補CSV
        │   └── meeting-note/         # 面談記録
        │   （各skillは SKILL.md ＋ references/）
        └── agents/
            └── tax-reviewer.md
```

## 今後の追加候補

- brand-guidelinesテンプレート（自社版ブランド規範skillの雛形）
- competitor-watch雛形（競合ウォッチの汎用版）
- 通知hookのサンプル集
