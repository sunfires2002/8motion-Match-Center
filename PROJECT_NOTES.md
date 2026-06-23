# SUNFIRES 公式サイト / 管理画面メモ

## 現在確認したファイル

- 公開用HTML: `/Users/makotoyokoe/Documents/index.html`
- メモ保存先: `/Users/makotoyokoe/Documents/PROJECT_NOTES.md/PROJECT_NOTES.md`

## 現状の確認結果

現在の `index.html` は、SUNFIRES フットサルチーム公式サイトの公開用HTMLとして構成されている。
管理画面、入力フォーム、`localStorage` 保存処理、公開HTML生成処理の JavaScript は、このファイル内には確認できない。

そのため、現時点で確認できるのは「生成後の公開ページ」の構造であり、管理画面側の実装は別ファイル、または過去の未保存コードに存在していた可能性がある。

## 公開サイトの機能一覧

### デザイン / 共通

- オレンジ x 黒系のスポーツサイトデザイン。
- Google Fonts の `Bebas Neue` と `Noto Sans JP` を使用。
- 固定ナビゲーション:
  - 順位表
  - 試合結果
  - 試合動画
  - 個人成績
- ヒーローエリア:
  - SUNFIRES ロゴ風タイトル
  - コートライン背景
  - `./emblem.jpg` を中央円内に表示する想定
  - 試合数、勝利数、総得点、現在順位のサマリー表示

### 順位表

- `#`, チーム, 試, 勝, 分, 負, 得, 失, 差, 全10試合, 勝点を表示。
- SUNFIRES 行は `rank-highlight` で強調。
- 直近/全10試合の結果は `form-badge` で表示。
  - `form-W`: 勝ち
  - `form-L`: 負け
  - `form-D`: 引き分け
  - `form-N`: 未消化

### 試合結果

- 試合カード形式で表示。
- 表示項目:
  - 日付
  - 節
  - ホームチーム
  - スコア
  - 相手チーム
- 注意:
  - 要件では「試合結果欄には動画リンクを表示しない」ことになっている。
  - しかし現在の `index.html` では、試合結果カード内に `前半動画` / `後半動画` ボタンが残っている。
  - 次回修正時は、試合結果欄から `.match-actions` の動画ボタンを削除し、動画は試合動画セクションのみに集約する必要がある。

### 試合動画

- `#highlights` セクションに、試合ごとの動画カードを表示。
- 1試合につき前半・後半を別々の YouTube iframe として表示。
- 現在の例:
  - 第2節 vs ナイスガイ
  - 前半: `https://www.youtube.com/embed/kdOg6lZjKcA`
  - 後半: `https://www.youtube.com/embed/dSIO3AdVRIs`

### 個人成績

- 得点ランキングとアシストランキングを2カラムで表示。
- 得点ランキング:
  - 順位
  - 選手名
  - 得点数
- アシストランキング:
  - 順位
  - 選手名
  - アシスト数
- 現在のアシストランキングは空。

### 過去データ PDF

- ユーザー要件では「過去データをPDFリンクとして掲載可能」とされている。
- 現在の `index.html` 内には、PDFリンク表示セクションやPDFリンク用HTMLは確認できない。
- 次回修正時は、既存デザインに合わせて過去データセクションを追加または生成対象に含める必要がある。

## 現在の公開HTMLに含まれるデータ

### ヒーローサマリー

```js
{
  matches: 2,
  wins: 2,
  totalGoals: 38,
  currentRank: "3位"
}
```

注意:
順位表上では SUNFIRES が1位として表示されている一方、ヒーローでは現在順位が3位になっている。
次回修正時は、管理データから自動反映するか、表示値の整合性を確認する。

### 順位表データ

```js
[
  {
    rank: 1,
    team: "SUNFIRES",
    played: 2,
    wins: 2,
    draws: 0,
    losses: 0,
    goalsFor: 17,
    goalsAgainst: 9,
    goalDiff: "+8",
    form: ["W", "W", "N", "N", "N", "N", "N", "N", "N", "N"],
    points: 3,
    highlight: true
  },
  {
    rank: 2,
    team: "ジョカトールKOBE",
    played: 2,
    wins: 1,
    draws: 0,
    losses: 0,
    goalsFor: 8,
    goalsAgainst: 5,
    goalDiff: "+3",
    form: ["W", "N", "N", "N", "N", "N", "N", "N", "N", "N"],
    points: 3,
    highlight: false
  }
]
```

### 試合結果 / 動画データ

```js
[
  {
    date: "2026.06.07",
    round: "第2節",
    homeTeam: "SUNFIRES",
    awayTeam: "ナイスガイ",
    homeScore: 8,
    awayScore: 5,
    firstHalfVideoUrl: "https://youtu.be/kdOg6lZjKcA",
    secondHalfVideoUrl: "https://youtu.be/dSIO3AdVRIs",
    firstHalfEmbedUrl: "https://www.youtube.com/embed/kdOg6lZjKcA",
    secondHalfEmbedUrl: "https://www.youtube.com/embed/dSIO3AdVRIs"
  }
]
```

### 個人成績データ

```js
{
  goals: [
    { rank: 1, player: "奥隅", goals: 7 },
    { rank: 2, player: "下仮屋", goals: 4 }
  ],
  assists: []
}
```

## 管理画面側で想定されるデータ構造

管理画面が `localStorage` に保存する場合、次のような1つのオブジェクトにまとめると扱いやすい。

```js
{
  summary: {
    matches: 2,
    wins: 2,
    totalGoals: 38,
    currentRank: "3位"
  },
  standings: [],
  matches: [],
  playerStats: {
    goals: [],
    assists: []
  },
  archives: [
    {
      label: "2025年度 成績PDF",
      url: "./archive/2025.pdf"
    }
  ]
}
```

推奨する `localStorage` キー:

```js
"sunfiresAdminData"
```

## 公開HTML生成処理の整理

管理画面から公開用 `index.html` を生成する処理は、概念的には次の流れになる。

1. `localStorage` から管理データを読み込む。
2. 入力フォーム上の最新値を管理データへ反映する。
3. 順位表HTMLを生成する。
4. 試合結果HTMLを生成する。
   - ここには動画リンクを出さない。
   - 日付、節、チーム名、スコアのみ表示する。
5. 試合動画HTMLを生成する。
   - YouTube URL を embed URL に変換する。
   - 前半URLがある場合は前半iframeを表示する。
   - 後半URLがある場合は後半iframeを表示する。
   - PULSE ECHO 前半URL / 後半URLも、同じ試合動画セクション内で扱う。
6. 得点ランキングHTMLを生成する。
7. アシストランキングHTMLを生成する。
8. 過去データPDFリンクHTMLを生成する。
9. 既存のCSS、配色、セクション構造を維持したHTMLテンプレートへ差し込む。
10. Blob などで `index.html` としてダウンロード、またはファイルへ書き出す。

## YouTube URL 変換ルール

管理画面では通常URLを保存し、公開HTML生成時に iframe 用URLへ変換するのがよい。

対応したい入力例:

```text
https://youtu.be/VIDEO_ID
https://www.youtube.com/watch?v=VIDEO_ID
https://www.youtube.com/embed/VIDEO_ID
```

出力:

```text
https://www.youtube.com/embed/VIDEO_ID
```

## 次回修正時の注意点

- 既存のオレンジ x 黒のスポーツサイトデザインを維持する。
- 既存セクションIDを維持する。
  - `standings`
  - `results`
  - `highlights`
  - `stats`
- 試合結果欄には動画リンクを表示しない。
- 試合動画セクションにのみ、前半・後半動画を表示する。
- 前半と後半のURLは別々に管理する。
- PULSE ECHO 前半URL / 後半URLを各試合に持たせる。
- 得点とアシストは別ランキングとして管理する。
- 過去データPDFリンクは公開ページ上で確認できる形にする。
- `localStorage` の既存データがある場合、キー名やデータ形式変更時は移行処理を入れる。
- 公開HTML生成時は、ユーザー入力文字列をHTMLエスケープして表示崩れや意図しないHTML挿入を防ぐ。

## 現在見つかっている不整合 / 要確認

- 現在の公開HTMLでは、試合結果欄に動画ボタンが表示されている。
- ヒーローの現在順位が `3位`、順位表の SUNFIRES は `1位` で不一致。
- 総得点 `38` と順位表上の SUNFIRES 得点 `17` の関係が不明。
- 管理画面本体のHTML / JavaScriptが現在の確認範囲には存在しない。
- PDFリンク表示機能は現在の公開HTMLには含まれていない。

