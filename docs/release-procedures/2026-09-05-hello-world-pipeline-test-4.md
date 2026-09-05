# リリース手順書: パイプライン動作確認用ダミー機能(4回目)

## デプロイ対象・手順
本ドキュメントはスモークテスト用のダミー設計であり、実際のアプリケーションデプロイは不要。
確認対象は `design-doc-pipeline` ワークフロー(`.github/workflows/design-doc-pipeline.yml`)の
`gh pr create` 実行部分のみ。

1. 本設計ドキュメントを含むPRを `main` にマージする。
2. `design-doc-pipeline` ワークフローが起動し、`docs/test-specs/` および
   `docs/release-procedures/` にファイルが生成されることを確認する。
3. 生成結果を含む新規ブランチが作成され、PRが自動作成されることを確認する。

## マイグレーション
不要。DBスキーマ・データの変更なし。

## ロールバック手順
1. 自動生成されたPRをマージ済みの場合は `git revert` で当該マージコミットを取り消す。
2. ワークフロー自体に問題がある場合は `.github/workflows/design-doc-pipeline.yml` を
   直前のコミットに戻す(`git revert` または該当ファイルのみ復元)。
3. 本番影響はないため、緊急ロールバックは不要。

## 動作確認チェックリスト
- [ ] PRマージ後に `design-doc-pipeline` ワークフローが起動した
- [ ] `docs/test-specs/2026-09-05-hello-world-pipeline-test-4.md` が生成された
- [ ] `docs/release-procedures/2026-09-05-hello-world-pipeline-test-4.md` が生成された
- [ ] 生成物を含む新規ブランチが作成された
- [ ] `gh pr create` によりPRが自動作成された(権限エラーが発生していない)
