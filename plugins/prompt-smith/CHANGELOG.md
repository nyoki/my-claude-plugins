# Changelog

## [0.2.1] - 2026-03-26
- 公式ドキュメント仕様を反映（docs.anthropic.com → code.claude.com 移行対応）
- パターン定義を更新 (claude-code@a542f1b, claude-plugins-official@b10b583)
- [SPEC] Skill `name` の最大文字数を64に修正（旧: 30）、必須→推奨に変更
- [SPEC] Skill `description` を必須→推奨に変更
- [SPEC] SKILL.md の推奨行数を500行以内に更新
- [SPEC] Plugin に LSP サーバー（`.lsp.json`）、`settings.json`、`outputStyles` を追加
- [SPEC] フックタイプ `agent`（エージェント型バリファイア）の説明を追加
- [PATTERN] Agent モデル分布を更新（inherit 57%, sonnet 37%, opus 7%）
- [PATTERN] Skill フィールド頻度を更新（allowed-tools 25%, user-invocable 17%, model 11%）
- [PATTERN] 新フィールド追加: `disable-model-invocation`, `effort`, `context`, `agent`, `hooks`（Skill）
- [PATTERN] Agent フィールド追加: `disallowedTools`, `permissionMode`, `maxTurns`, `skills`, `mcpServers`, `memory`, `background`, `effort`, `isolation`
- [PATTERN] Command フィールド頻度を更新（allowed-tools 69%, argument-hint 40%）
- [CONFLICT] Agent `color`: 公式仕様フロントマッターテーブルに記載なし、リポジトリでは100%使用 → 推奨として記載

## [0.2.0] - 2026-03-11
- 初回リリース
