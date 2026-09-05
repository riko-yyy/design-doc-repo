# テスト仕様書: パイプライン動作確認用ダミー機能(4回目)

## 概要
`gh pr create` の許可と `GH_TOKEN` 追加後、design-doc-pipeline がPRマージをトリガーに
テスト仕様書・リリース手順書を自動生成し、PR作成まで完走することを確認するスモークテスト。

## テストケース一覧

| ID | 前提条件 | 操作 | 期待結果 |
|----|---------|------|---------|
| TC-01(正常系) | `docs/designs/**.md` を変更するPRが作成されている | PRを `main` にマージする | `design-doc-pipeline` ワークフローが起動し、`docs/test-specs/` と `docs/release-procedures/` にファイルが生成され、新規ブランチ経由でPRが作成される |
| TC-02(異常系) | `allowed_tools` に `Bash(gh pr create:*)` が含まれていない、または `GH_TOKEN` が未設定 | 同上の手順でPRをマージする | `gh pr create` が権限エラーで失敗し、ワークフローがエラー終了する(生成物のコミットまでは成功する場合がある) |
| TC-03(境界値) | 対象PRに `docs/designs/**.md` の変更が含まれない(他ファイルのみ変更) | PRをマージする | `paths` フィルタにより `design-doc-pipeline` ワークフローが起動しない |
