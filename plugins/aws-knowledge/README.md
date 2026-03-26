# aws-knowledge

AWS関連の質問・調査時にAWS Knowledge MCP Serverを自動活用するプラグイン。

## 概要

AWS サービスに関する質問を受けると、`aws-expert` エージェントが AWS Knowledge MCP Server から公式ドキュメントを取得し、最新情報に基づいて回答します。

## コンポーネント

| 種類 | 名前 | 説明 |
|------|------|------|
| Agent | `aws-expert` | AWS公式ドキュメントに基づいて回答するエージェント |

## 前提条件

### AWS Knowledge MCP Server の登録（必須）

このプラグインを使用するには、AWS Knowledge MCP Server をグローバル MCP に事前登録する必要があります。

**セットアップ:**

```bash
claude mcp add --transport http aws-knowledge --scope user https://knowledge-mcp.global.api.aws
```

登録後、`/mcp` で接続状態を確認してください。

### 制約事項

- プラグインの `.mcp.json` ではリモート HTTP MCP サーバーがサポートされていないため、`claude mcp add` によるグローバル MCP への登録が必要です
- AWS Knowledge MCP Server はインターネット接続が必要です
- 認証は不要ですが、レート制限があります

## MCP 設定の削除

プラグインをアンインストールする際は、グローバル MCP 設定も削除してください:

```bash
claude mcp remove aws-knowledge --scope user
```
