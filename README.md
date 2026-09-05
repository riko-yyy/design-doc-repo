# design-doc-repo

設計ドキュメント駆動の実装自動化パイプライン。

設計ドキュメントのPRがこのリポジトリの `main` にマージされたことを起点に、GitHub Actions上でClaude Codeを起動し、テスト仕様書とリリース手順書のドラフトを自動生成してPRを作成する。将来的にはDevinへの実装依頼(別リポジトリ)も同じフックから起動する想定。

## 現状の方針

- まず個人アカウント・個人検証で試している段階
- Devinは未契約 → Devin部分はスタブ(ドライラン)。契約後にワークフロー内のcurlコメントを外すだけで有効化できる
- 対象は以下の2つ
  1. 実装フック(Devin・別リポジトリ) — 未契約のためスタブのみ
  2. テスト仕様書・リリース手順書のドラフト生成(Claude Code・同一リポジトリ)
- 実装完了後にテスト仕様書・リリース手順書を実コードに合わせて更新する2段構成は今回は見送り、ドラフト生成のみ

## リポジトリ構成

```
design-doc-repo/
├── .github/workflows/design-doc-pipeline.yml   # PRマージ起点のワークフロー
├── .claude/skills/
│   ├── test-spec-writer/SKILL.md               # テスト仕様書生成skill
│   └── release-procedure-writer/SKILL.md       # リリース手順書生成skill
└── docs/
    ├── designs/               # 設計ドキュメント(ここに置いたPRがトリガー)
    ├── test-specs/            # 自動生成されたテスト仕様書
    └── release-procedures/    # 自動生成されたリリース手順書
```

## 設計ドキュメントの書き方

`docs/designs/` にMarkdownを追加し、frontmatterに以下を含める。

```markdown
---
target_repo: your-account/example-app
implementation_prompt: |
  ユーザー一覧にページネーションを追加してください。
  詳細は本ドキュメント参照。
---

# 機能名
...
```

- `target_repo`: Devinへの実装依頼先リポジトリ(現状スタブでは未使用)
- `implementation_prompt`: Devinセッション作成APIに渡すプロンプト本文(現状スタブでは未使用)

このPRを `main` にマージすると、`docs/test-specs/` と `docs/release-procedures/` に対応するドラフトを生成するPRが自動作成される。

## セットアップ

1. **GitHub Appのインストール**: このリポジトリのクローンで `claude` を起動し、`/install-github-app` を実行
2. **認証方法をどちらか選ぶ**
   - Claude Pro/Maxサブスクリプションを使う場合: ローカルで `claude setup-token` を実行し、発行されたトークンをリポジトリの **Settings → Secrets and variables → Actions** に `CLAUDE_CODE_OAUTH_TOKEN` として登録
   - Anthropic ConsoleのAPIキーを使う場合: `ANTHROPIC_API_KEY` を同じくActions Secretsに登録し、ワークフローの `claude_code_oauth_token` を `anthropic_api_key` に読み替える
3. **リポジトリ設定を有効化**: **Settings → Actions → General → Workflow permissions** で「Allow GitHub Actions to create and approve pull requests」を有効化(これがないと `docgen` ジョブは生成物のコミット・pushまでは成功するが、PR作成だけ失敗する)
4. (Devin契約後) `DEVIN_API_KEY` / `DEVIN_ORG_ID` をSecretsに登録し、`devin-stub` ジョブ内のcurlコメントを解除

## 動作確認でハマったポイント(トラブルシューティング)

- **`API Error: 404 model: claude-sonnet-4-20250514`**: `anthropics/claude-code-base-action@beta` のデフォルトモデルが廃止されていた。`with.model` に現行モデル(例: `claude-sonnet-5`)を明示指定する
- **`claude_args` を指定しても効かない**: `@beta` タグのこのアクションは `claude_args` を受け付けない(warningで無視されるだけでエラーにならないので気づきにくい)。`model` / `allowed_tools` など、アクションが実際に定義している入力名を使うこと
- **`gh pr create` が承認待ちのまま止まる**: `allowed_tools` に `Bash(gh pr create:*)` を含めないと、非対話のCI上では誰も承認できずジョブがそのまま(exit 0で)終了してしまう
- **`gh pr create` が権限エラーで失敗する**: ステップに `GH_TOKEN` を渡していない、または上記「セットアップ」手順3のリポジトリ設定が無効なままになっている

## 未着手・今後の検討事項

- frontmatterから `target_repo` / `implementation_prompt` を抽出するロジックの実装
- 複数リポジトリ対応(`target_repos` 配列化・matrix実行・実装順序の制御)
- 実装完了後にテスト仕様書・リリース手順書を実コードに合わせて更新する2段構成
- 実装PRと設計ドキュメントの紐付け(PR本文への `design-doc: <パス>` 埋め込みなど)
- CLAUDE.md / AGENTS.mdによる実装対象リポジトリへの規約共有
- レビューゲートの役割分担(実装PR / テスト仕様書PR / リリース手順書PRをそれぞれ誰がレビューするか)
- コスト管理(DevinのACU上限、Claude Code Actionの同時実行数制限)
- Enterprise移行時の認証方式の見直し(個人のOAuthトークンから、組織のAPIキーまたはWorkload Identity Federation + Service Accountへ)

## 背景として押さえておきたい原則

- Plan-first(計画→実装→検証)を崩さない
- タスクは小さく切る
- 検証ゲート(lint・型チェック・既存テスト)を人間レビューより前に置く
- 「人間レビュー」は誰が・どの時点で・何を基準に見るかを明文化する
- AI生成PRは一見きれいでも技術的負債を持ち込みやすいので通常より注意深く見る
- リポジトリ構成・組織文化によって最適解が変わる部分(設計docの置き場所、並列/直列、複数リポジトリ連携)は、他社事例をそのまま輸出せず小さく試して直す前提で進める
