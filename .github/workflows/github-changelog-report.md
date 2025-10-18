---
on:
  workflow_dispatch:  

# Permissions - what can this workflow access?
permissions:
  contents: read
  issues: write
  pull-requests: write

# Outputs - what APIs and tools can the AI use?
safe-outputs:
  create-discussion:
    title-prefix: "${{ github.workflow }}"
    category: "アジェンダ"

mcp-servers:
  tavily:
    command: npx
    args: ["-y", "@tavily/mcp-server"]
    env:
      TAVILY_API_KEY: "${{ secrets.TAVILY_API_KEY }}"
    allowed: ["search", "search_news"]

timeout_minutes: 15

---

# github-changelog-report

このワークフローは、GitHub Changelogの最新情報を取得し、カテゴリごとに分類したレポートを作成します。

## Instructions

1. Tavily MCPを使用して https://github.blog/changelog/feed/ の内容を取得する
2. RSSフィードから `<pubDate>` を確認し、実行日の3日前までの情報のみをフィルタリングする
3. 取得した情報を `<category domain="changelog-label">` ごとに分類する
4. https://github.com/github-dockyard-community/radio/discussions/14 の形式に則ってレポートを作成する
5. 作成したレポートをDiscussionとして投稿する（内容は日本語で記述する）

