---
target_repo: riko-yyy/example-app
implementation_prompt: |
  これはパイプラインの動作確認用のダミー設計ドキュメントです。
  実際の実装は不要です。
---

# パイプライン動作確認用ダミー機能

## 背景
design-doc-pipeline(テスト仕様書・リリース手順の自動生成)が
PRマージをきっかけに正しく起動するかを確認するためのスモークテスト用ドキュメント。

## 実装方針
特になし(このドキュメントのマージをトリガーに、Claude Codeが
`docs/test-specs/` と `docs/release-procedures/` にドラフトを生成できるかを確認する)。
