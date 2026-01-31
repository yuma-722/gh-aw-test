---
on:
  workflow_dispatch:

# Permissions - mainジョブでは書き込み権限を持たせず、safe-outputsで書き込みを行います
permissions:
  contents: read
  actions: read
  issues: read
  pull-requests: read

# Outputs - Discussion作成はsafe-outputs経由で行います
safe-outputs:
  create-discussion:
    title-prefix: "${{ github.workflow }}"
    category: "アジェンダ"

network:
  allowed:
    - defaults
    - github
    - node
    - "github.blog"
    - "*.github.blog"

mcp-servers:
  tavily:
    command: "npx"
    args: ["-y", "@tavily/mcp-server"]
    env:
      TAVILY_API_KEY: "${{ secrets.TAVILY_API_KEY }}"
    allowed: ["search", "search_news"]

timeout-minutes: 15

---

# github-changelog-report

このワークフローは、GitHub Changelogの最新情報を取得し、カテゴリごとに分類したレポートを作成します。
Please write in Japanese.

## Instructions

1. Tavily MCPを使用して https://github.blog/changelog/feed/ の内容を取得する
2. RSSフィードから `<pubDate>` を確認し、実行日の3日前までの情報のみをフィルタリングする
3. 取得した情報を `<category domain="changelog-label">` ごとに分類する
4. https://github.com/github-dockyard-community/radio/discussions/14 の形式に則ってレポートを作成する
5. 作成したレポートをDiscussionとして投稿する（内容は日本語で記述する）

## Discussionの作成

- 最後に必ずDiscussionを作成してください（safe-outputsツールを使用）
- タイトル形式: `GitHub Changelog アップデートまとめ (YYYY-MM-DD)` ※手動実行した日付（JST）を含める
- 本文: 上記手順で作成した日本語レポート全文をMarkdown形式で記載

## 注意点
- Tavily MCP が使えない場合（MCP クライアントの起動に失敗したり、エラーが返る場合）は、必ず `curl` コマンドを使って RSS フィードを取得してください。
  - 例: `curl -s https://github.blog/changelog/feed/`
  - Tavily での取得に失敗しても、そこで処理を止めず、必ず `curl` での取得を試みてください。
- Tavily も `curl` もどちらも使えない場合のみ、その旨と原因・解消方法を日本語で明示したレポートを safe-outputs の Discussion として出力してください。