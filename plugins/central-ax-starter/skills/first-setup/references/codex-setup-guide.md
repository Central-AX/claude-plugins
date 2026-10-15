# first-setup Codex版ガイド

Codex（OpenAIのCodex CLI・ChatGPTデスクトップアプリ）で first-setup を実行するときの読み替えと設定テンプレート。質問の中身・職種別分岐表・CLAUDE.mdの構成テンプレート・セキュリティルール文面は `setup-guide.md` をそのまま使い、ファイル名・設定の書き方・質問の出し方だけをここで置き換える。

Codex固有の設定値は2026-10時点の公式ドキュメント（末尾の出典）で確認したもの。Codexの設定は変更が多く、権限プロファイル（permissions）はベータ、rulesは実験的機能なので、挙動が説明と違うときは出典を確認する。

## 読み替え表

| Claude Code | Codex |
|------|------|
| `CLAUDE.md`（作業フォルダ直下） | `AGENTS.md`（作業フォルダ直下） |
| `~/.claude/CLAUDE.md`（個人共通） | `~/.codex/AGENTS.md` |
| `.claude/settings.json`（JSON） | `.codex/config.toml`（TOML。作業フォルダ直下） |
| `~/.claude/settings.json`（個人共通） | `~/.codex/config.toml` |
| `permissions.deny` の `Read(...)` / `Edit(...)` | 権限プロファイル `[permissions.<名前>]` の filesystem で `"deny"` |
| 都度確認（デフォルト） | `approval_policy = "on-request"` |
| `Bash(rm -rf:*)` 等のdeny | rulesファイルの `prefix_rule(..., decision = "forbidden")` |
| `disableBypassPermissionsMode` | 個人の設定では封印できない（組織なら `requirements.toml`。後述） |
| AskUserQuestion（選択式の質問） | 番号付き選択肢をテキストで出す（次節） |

## 質問の出し方（AskUserQuestionの代わり）

Codexで選択式の質問ツールが使えない場合は、番号付きの選択肢をテキストで出し、番号で答えてもらう。タイプ量を最小にするというfirst-setupの方針は変えない。

- 1回のメッセージに出す質問は最大4問。各質問に `Q1` `Q2` …、選択肢に `1` `2` … を振る
- 推奨の選択肢は「（推奨）」を付けて先頭に置く
- 複数選択できる質問は見出しに「（複数可）」と書く
- 各質問の最後に「その他（自由に書いてください）」を置く（AskUserQuestionのOtherの代わり）
- 末尾に回答例を1行添える

```text
Q1 あなたのお仕事は？（複数可）
  1. 経営者・役員
  2. 経理・総務・事務
  3. 営業・マーケティング
  4. エンジニア・開発
  5. その他（自由に書いてください）

Q2 今日設定する範囲は？
  1. 全部おまかせ（推奨）
  2. AGENTS.mdだけ
  3. 機密まわりだけ
  4. その他（自由に書いてください）

回答例: 「Q1: 2,3 / Q2: 1」
```

回答が番号だけで曖昧なとき（どの質問への答えか分からない等）は推測せず、その質問だけ聞き直す。

## ステップ0: 現状診断

確認するファイルを読み替える: `AGENTS.md` / `.codex/config.toml` / `~/.codex/config.toml` / `secrets/` / `.env` / `.gitignore`。作業フォルダに `CLAUDE.md` や `.claude/settings.json` があれば「Claude Codeと併用している」と判断し、ステップ1・2の併用時の扱いに従う。

## ステップ1: AGENTS.md

- `setup-guide.md` の「CLAUDE.md の構成テンプレート」をそのまま使い、ファイル名を `AGENTS.md` にする
- Codexは起動時にプロジェクトのルートから作業フォルダまでの `AGENTS.md` を読み、合計32KiB（`project_doc_max_bytes` の既定値）で読み込みを打ち切る。Claude Code版と同じく短く保つ
- `AGENTS.md` の変更は次に起動したセッションから効く
- **Claude Codeと併用している場合**（既に `CLAUDE.md` がある）: 同じルールを2か所に書くとズレるので、次の選択肢を出す
  1. `AGENTS.md` を正本にし、`CLAUDE.md` は `@AGENTS.md` の1行で取り込む（推奨。Claude Codeは `@パス` で別ファイルを読み込める）
  2. 両方に同じ内容を書く
  3. `AGENTS.md` だけ作り、`CLAUDE.md` は触らない
  - どれを選んでも既存の `CLAUDE.md` は上書きせず、差分を見せてから書き換える

## ステップ2: 機密情報の隔離

置き場の方式（secrets/ フォルダ or .env）の質問、直書き機密の移動フロー、`.gitignore` への追加は `setup-guide.md` と同じ。読み書きのブロックだけ、Codexの権限プロファイルで設定する。

### 権限プロファイルのテンプレート（`.codex/config.toml`）

```toml
approval_policy = "on-request"
default_permissions = "central-ax-safe"

[permissions.central-ax-safe]
description = "作業フォルダは編集可。機密の置き場は読み書き不可"
extends = ":workspace"

[permissions.central-ax-safe.filesystem.":workspace_roots"]
"secrets" = "deny"
".env" = "deny"
".env.*" = "deny"
```

- `extends = ":workspace"` は組み込みの「作業フォルダ内は編集可」の設定を引き継ぐ。ネットワークはこの設定のままだとオフ（コマンドから外部に通信するには承認が要る）
- 選ばれた方式に合わせて行を調整する（secrets/ のみなら `.env` の2行は不要）
- 既存の `.codex/config.toml` があれば上書きせず、足りない行だけ追記する差分を提案する

ユーザーに伝える注意点（Claude Code版の「denyの限界」に相当）:

- **`sandbox_mode` と併用できない。** 権限プロファイルはベータ機能で、個人設定・プロジェクト設定のどこかに `sandbox_mode` や `[sandbox_workspace_write]` が書かれている、または起動時に `--sandbox` を付けると、このプロファイルは使われず機密が読める状態に戻る。診断で既存の `sandbox_mode` 等を見つけたら、消してよいかを選択式で確認する
- **プロジェクトを「信頼」しないと読み込まれない。** `.codex/config.toml` は、Codexがそのフォルダを信頼済み（trusted）として扱うときだけ読み込まれる。初回起動時の確認で信頼する
- **サンドボックス外での実行を承認すると効かない。** このブロックはCodexのサンドボックス内で動くコマンドに効く。Codexから「サンドボックスの外で実行してよいか」と承認を求められて許可したコマンドには効かないので、機密ファイルに触れる承認依頼は拒否する
- `.codex/` はCodexのサンドボックスで書き込みが保護される場所なので、作成・編集のときに承認を求められることがある。内容を見せてから許可してもらう
- Windowsでは、サンドボックスの方式によっては細かい読み書きの切り分けを強制できず、その場合Codexはコマンドの実行自体を断る。うまく動かないときはWSL2での利用を案内する

SSH鍵を扱うPCでは、個人設定 `~/.codex/config.toml` に次を追加する（作業フォルダ外のパスなので、プロジェクト単位ではなく個人設定に書く。同じプロファイル名に書くと、プロジェクト側の設定と合わさって両方効く）:

```toml
default_permissions = "central-ax-safe"

[permissions.central-ax-safe]
extends = ":workspace"

[permissions.central-ax-safe.filesystem]
"~/.ssh" = "deny"
```

### 確実に読ませたくない機密は作業フォルダの外へ

上のブロックはベータ機能で、承認ひとつで外れる。顧客の個人情報・口座情報など「絶対に読ませたくない」ものは、Codexの作業フォルダ（とその配下）に置かず、別の場所に保管する方式も選択肢として示す。作業に必要になったときだけ、人間が必要な部分を渡す。

## ステップ3: permissions

許可の方針の質問をCodex向けに読み替える:

- **許可の方針**
  - 安全重視セット（推奨）＝都度確認＋機密のブロック＋破壊的コマンドの禁止 / デフォルトのまま＝Codexの標準設定 / 読み取り専用＝ファイルの編集もさせない

Codexの標準（Gitで管理されたフォルダの場合）は「作業フォルダ内の読み取り・編集・コマンド実行は承認なし、作業フォルダ外の編集とネットワーク利用は承認が要る」設定。Gitで管理されていないフォルダや、まだ信頼していないフォルダでは読み取り専用で始まることがある（`/permissions` で切り替えられる）。

- **安全重視セット**: ステップ2のテンプレート（`approval_policy = "on-request"` を含む）に加えて、次のrulesファイルを置く
- **デフォルトのまま**: `.codex/config.toml` に手を入れない（既存の設定は温存）。ステップ2で機密のブロックを入れた場合はそれだけ残る
- **読み取り専用**: ステップ2のテンプレートの `extends = ":workspace"` を `extends = ":read-only"` に変える（機密のdenyは残す）

`approval_policy` に `"untrusted"` は使わない（Codexでは廃止され、書くと起動できなくなることがある）。`"never"`（承認を一切求めない）も業務用の初期設定では選ばない。

### 破壊的コマンドの禁止（rulesファイル）

`.codex/rules/central-ax-safety.rules` に置く（プロジェクトを信頼したときだけ読み込まれる。全フォルダに効かせるなら `~/.codex/rules/central-ax-safety.rules`）。書いたらCodexを再起動する。

```python
# Central AX 安全重視セット: 取り返しのつかない操作をブロックする
prefix_rule(
    pattern = ["rm", ["-rf", "-fr"]],
    decision = "forbidden",
    justification = "一括削除は禁止。消すファイルを確認して人間が実行する",
    match = ["rm -rf build", "rm -fr build"],
)
prefix_rule(
    pattern = ["git", "push", ["--force", "-f"]],
    decision = "forbidden",
    justification = "強制pushは禁止。必要なら人間が実行する",
    match = ["git push --force origin main", "git push -f"],
)
prefix_rule(
    pattern = ["git", "reset", "--hard"],
    decision = "forbidden",
    justification = "未保存の変更が消えるため禁止。必要なら人間が実行する",
    match = ["git reset --hard HEAD~1"],
)
```

- rulesは実験的機能。コマンドの先頭一致で判定するため、`rm -r -f` のように書き方を変えると一致しない。「事故防止の一層」と説明し、万能ではないと伝える
- エンジニア等でこれらのコマンドが業務で必要なユーザーには、ブロックされる場面と外し方（該当の `prefix_rule(...)` を消す）を一言添える
- 動作は `codex execpolicy check --pretty --rules .codex/rules/central-ax-safety.rules -- rm -rf build` で確認できる（`"decision": "forbidden"` と出れば一致）

### 確認スキップの封印について

Claude Code版の `disableBypassPermissionsMode` に当たる「本人も解除できない封印」は、Codexの個人設定ではできない。起動時に `--yolo`（`--dangerously-bypass-approvals-and-sandbox`）や `--sandbox danger-full-access` を付けると承認とサンドボックスがすべて外れるので、「使わない」とAGENTS.mdのルールに書くかを選んでもらう。組織で強制する方法は次節。

### 非エンジニア向け（ネットワーク）

Codexは標準でコマンドからのネットワーク利用がオフ（使うには承認が要る）なので、Claude Code版の「curl/wgetのdeny」に当たる追加設定は不要。設定ファイルで `network_access = true` や `network.enabled = true` を入れないよう、ルールとして伝える。

## ステップ4: セキュリティルール

`setup-guide.md` の「CLAUDE.md追記文面」をそのまま `AGENTS.md` に追記する。「外部skill・プラグインは導入前に中身を目視確認する」の行は、Codexのプラグインにも同じく当てはまる。

## ステップ5: 動作確認

1. `.codex/config.toml` の書式を確認する（TOMLとして読めるか。Pythonがあれば `python -c "import tomllib; tomllib.load(open('.codex/config.toml','rb'))"`）
2. `sandbox_mode` が個人設定・プロジェクト設定のどこにも残っていないことを確認する（残っているとプロファイルが使われない）
3. 新しいセッションで `/status` を実行し、承認ポリシー（approval policy）と書き込み可能な場所が設定どおりか確認する
4. AGENTS.mdの内容に沿った簡単な確認（例: 「成果物はどこに置くルール？」に正しく答えられるか）
5. 新しいセッションを開いて機密ファイルを読ませ、`Permission denied` で読めないことを確認してください、と手順を案内する（ステップ2をスキップした場合は省く）
6. ズレがあればAGENTS.mdまたは `.codex/config.toml` を修正する

## 組織展開ガイド（Codex）

複数人に配る場合、個人の `config.toml` は本人が書き換えられる。本人が変えられない制限は管理者が `requirements.toml` で配る。

配置パス:
- macOS / Linux: `/etc/codex/requirements.toml`
- Windows: `%ProgramData%\OpenAI\Codex\requirements.toml`

例（承認なしモードとフルアクセスを禁止し、機密ブロック入りのプロファイルを既定にする）:

```toml
allowed_approval_policies = ["on-request"]
default_permissions = "central_ax_safe"

[allowed_permission_profiles]
":read-only" = true
":workspace" = true
central_ax_safe = true
# ":danger-full-access" は書かないので禁止される

[permissions.central_ax_safe]
description = "作業フォルダは編集可。機密の置き場は読み書き不可"
extends = ":workspace"

[permissions.central_ax_safe.filesystem.":workspace_roots"]
"secrets" = "deny"
".env" = "deny"
".env.*" = "deny"
```

- 権限プロファイルの許可リストはCodex 0.138.0以降で有効。古いバージョンが混ざる環境では全員の更新を待ってから配る
- 管理用のプロファイル名は、利用者の個人設定と名前が衝突しないものにする（上の例では個人用テンプレートの `central-ax-safe` と分けている）
- `requirements.toml` の `[rules]` で、rulesの禁止（forbidden）を本人が外せない形で配ることもできる

### 出典
- 公式: https://learn.chatgpt.com/docs/agent-configuration/agents-md
- 公式: https://learn.chatgpt.com/docs/config-file/config-basic
- 公式: https://learn.chatgpt.com/docs/config-file/config-reference
- 公式: https://learn.chatgpt.com/docs/agent-approvals-security
- 公式: https://learn.chatgpt.com/docs/permissions
- 公式: https://learn.chatgpt.com/docs/agent-configuration/rules
- 公式: https://learn.chatgpt.com/docs/enterprise/managed-configuration
