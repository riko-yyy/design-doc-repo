---
target_repo: riko-yyy/example-app
source_design: docs/designs/2026-09-05-hello-world-pipeline-test-3.md
---

# パイプライン動作確認用ダミー機能(3回目) リリース手順書

## デプロイ対象・手順
本ドキュメントはスモークテスト用のダミー設計であり、実装コードの変更は伴わない。
そのため対象アプリケーション(`riko-yyy/example-app`)へのデプロイ作業は不要。
確認対象は design-doc-pipeline ワークフロー自体の正常動作のみ。

1. 本設計ドキュメントが `docs/designs/` にマージされたことを確認する。
2. design-doc-pipeline ワークフローが起動し、正常終了することを確認する。

## マイグレーション
不要。データベーススキーマ・データの変更はない。

## ロールバック手順
アプリケーションへの変更がないため、ロールバックは不要。
仮にパイプライン生成物(`docs/test-specs/` や `docs/release-procedures/` 配下のファイル)に
誤りがある場合は、該当コミットを `git revert` する。

## 動作確認チェックリスト
- [ ] design-doc-pipeline ワークフローがエラーなく完了している
- [ ] `docs/test-specs/2026-09-05-hello-world-pipeline-test-3.md` が生成されている
- [ ] `docs/release-procedures/2026-09-05-hello-world-pipeline-test-3.md` が生成されている
- [ ] 生成されたドラフトの内容が設計ドキュメントの意図と矛盾していない
