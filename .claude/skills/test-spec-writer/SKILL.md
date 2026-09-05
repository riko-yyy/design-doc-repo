---
name: test-spec-writer
description: 設計ドキュメントからテスト仕様書を生成する。docs/designs/配下のMarkdownファイルを入力として、正常系・異常系・境界値のテストケースをMarkdown表形式で出力する。
---

設計ドキュメント(frontmatterを含むMarkdown)を読み、以下を含むテスト仕様書を
`docs/test-specs/<設計docと同名>.md` として作成してください。

- 対象機能の概要(1〜2行)
- テストケース一覧(表形式: ID / 前提条件 / 操作 / 期待結果)
- 正常系・異常系・境界値を最低1件ずつ含める
