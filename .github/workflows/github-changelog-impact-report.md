---
description: GitHub Changelog impact-oriented report (Japanese)
on:
  workflow_dispatch:
    inputs:
      days_back:
        description: "何日前まで遡るか（未指定時は7）"
        required: false
        default: "7"

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
    - "github.blog"

tools:
  bash:
  web-fetch:

timeout-minutes: 15
---

{{#runtime-import agentics/github-changelog-impact-report.md}}
