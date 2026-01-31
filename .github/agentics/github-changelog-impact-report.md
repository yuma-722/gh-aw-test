<!--
  This prompt is imported at runtime by:
  .github/workflows/github-changelog-impact-report.md

  Edit this file to change agent behavior WITHOUT recompiling.
-->

# GitHub Changelog 実務影響レポート

このワークフローは、GitHub Changelog のRSSフィードから直近の更新を抽出し、開発チーム向けに「実務影響が分かる」形で要点を整理して Discussion に投稿します。
Please write in Japanese.

## 入力

- 対象期間（日数）: `${{ github.event.inputs.days_back }}`
  - 未指定/空の場合は 7 日として扱う

## 手順

1. `web-fetch` を使って RSS を取得する
   - URL: https://github.blog/changelog/feed/
2. `web-fetch` が失敗した場合は、必ず `curl` でフォールバックする
   - 例: `curl -s https://github.blog/changelog/feed/`
   - 取得に失敗しても、処理を止めずに次の手段を試すこと
3. RSS を解析し、`<item>` ごとに最低限次の情報を抽出する
   - タイトル
   - URL
   - 公開日時（`<pubDate>`）
   - カテゴリ（可能なら `<category domain="changelog-label">`。無ければ `Uncategorized`）
4. `days_back` の範囲内の更新のみ残す
   - 比較は公開日時を基準に行う
   - 日付は JST（日本時間）で「実行日」を解釈してよいが、RSS の `pubDate` はタイムゾーンを含むため、確実にパースして時刻として比較すること
5. 内容を「実務影響レポート」形式に整形する

## レポート形式（必須）

- GitHub Flavored Markdown (GFM) を使用する
- 見出しは `###` から開始する（h1/h2 は使わない）
- 長い説明や一覧は `<details>` で折りたたむ（必要に応じて）

### 先頭に置く要約
以下を必ず含める:
- 対象期間（JST基準）
- 件数
- 重要度の高いもの（最大3件）

### カテゴリ別の一覧
カテゴリごとに以下のテンプレで列挙する:

- **タイトル**: （短く）
  - **公開日**: YYYY-MM-DD
  - **重要度**: 高 / 中 / 低
  - **対応要否**: 要対応 / 要確認 / 不要
  - **影響**: （誰に何が影響するか。Actions利用者、API利用者、Enterprise管理者、Copilot利用者など）
  - **推奨アクション**: （具体的に。例: 設定確認、ドキュメント確認、影響調査、期限までに対応、など）
  - **参考**: URL

### 判定ガイド（重要度・対応要否）
- **高**: breaking changes / deprecation / removal / security / 課金や制限変更 / 既存挙動が変わる可能性
- **中**: 新機能だが影響範囲が限定的、移行は任意、設定変更で恩恵
- **低**: UI改善、軽微な改善、影響が限定的

対応要否の目安:
- **要対応**: 期限がある、非推奨→廃止が近い、セキュリティ、既存CI/運用に影響
- **要確認**: 影響があり得るが確度が低い、組織の使い方次第
- **不要**: 追随不要（情報共有のみ）

## Discussion の作成（必須）

- 最後に必ず Discussion を作成してください（`safe-outputs` の `create-discussion` を使用）
- タイトル形式: `GitHub Changelog 実務影響まとめ (YYYY-MM-DD)`
  - YYYY-MM-DD は手動実行した日付（JST）
- 本文: 上記フォーマットの日本語レポート全文

## 例外時のふるまい

- RSS の取得に失敗しても、可能な限り原因を切り分けて、必ず Discussion を作成して状況を報告する
  - 例: ネットワーク許可、DNS、タイムアウト、取得結果が空、など
- 対象期間内に更新が 0 件の場合も Discussion は作成し、「更新なし」と分かる短いレポートを投稿する
