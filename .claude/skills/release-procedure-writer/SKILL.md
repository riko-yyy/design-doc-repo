---
name: release-procedure-writer
description: 設計ドキュメントからリリース手順書を生成する。デプロイ順序・DBマイグレーション・ロールバック手順を含むMarkdownを出力する。
---

設計ドキュメントを読み、`docs/release-procedures/<設計docと同名>.md` として
リリース手順書を作成してください。

- デプロイ対象・手順
- マイグレーションが必要か、必要ならその手順
- ロールバック手順
- 動作確認チェックリスト
