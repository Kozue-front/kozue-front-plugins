# slack-filter

Slack から、**自分宛て・自分がいる会話・自分の担当する話題**の発言だけを拾ってまとめる Claude 用の skill です。

大きなチャンネルに全員が集まっていると、自分に関係のない発言まで読むことになります。この skill は、関係のあるものだけを1つのまとめにします。特定のアカウントの発言を結果から外す設定もできます(自動投稿や、自分の担当と関係のない発言が多い相手など)。

> English summary: A Claude skill (Japanese UI) that reads your Slack through the Slack connector and summarizes only what concerns you — mentions, threads you are in, and topics you choose. You can also mute specific accounts so their messages, and replies to or quotes of them, stay out of the summary. Settings are kept on your own machine or in your own Claude memory; nothing is written to Slack.

## 使い方

| 打つ文 | 動き |
|---|---|
| `Slackフィルタ` | 直近24時間を、設定に従ってまとめる |
| `Slackフィルタ 3日` / `Slackフィルタ 10/1〜10/7` | 指定した期間をまとめる |
| `Slackフィルタ設定` | 設定画面を開く |

はじめて使うときは、まず設定画面が開きます。

## 設定画面でできること

1. **除外する人を決める**:人ごとに、2つのモードから選べます
   - **ブロック**:その人の発言、その人への返信、その人の発言の引用を、すべて結果から外します
   - **場面ごとに決める**:「自分宛て」「自分がいるスレッドの中」「関係のない場所」の3つの場面ごとに、「消す/確認する/残す」を選べます。「確認する」は本文を出さず、場所と日時のリンクだけを出します
2. **拾う発言を選ぶ**:自分宛てのメンション/自分がいるスレッド/自分に関係のある話題。話題は、直近1週間の自分の発言から Claude が候補を出すので、選んで、足りないものだけを書き足します
3. **読むチャンネルを決める**(空欄なら、参加しているチャンネルすべて)
4. **結果の形を選ぶ**(要約/発言の一覧)
5. **次にこの画面を開くタイミングを選ぶ**(毎回/3日後/1週間後/月初/自分で言う)

## 入れ方

このプラグインは、お店(マーケットプレイス)[`Kozue-front/kozue-front-plugins`](../../README.md) から入れます。

### Claude Code

```
claude plugin marketplace add Kozue-front/kozue-front-plugins
claude plugin install slack-filter@kozue-front-plugins
```

### Claude(デスクトップアプリ・Web)

1. **カスタマイズ > プラグイン** を開く
2. **「+ 追加」の ▼ →「マーケットプレイスを追加」** に `Kozue-front/kozue-front-plugins` を入れて同期する
3. **slack-filter** の **「+」** を押してインストールする

## 必要なもの

- **Slack のコネクタ**:Claude から自分の Slack を読めるようにしておいてください
- **設定画面**は、会話の中に画面(ウィジェット)を出せる環境で表示されます。出せない環境では、同じ項目を質問形式で聞きます
- 設定画面の見出しの文字は Google Fonts から読み込みます。読み込めない環境では、普通の等幅の文字で表示されます
- 日本語専用です

## 設定と情報の扱い

- 設定は、Claude Code では自分のPCの `~/.claude/slack-filter/settings.json` に保存します。claude.ai では自分のメモリか指示欄に置きます
- Slack には何も書き込みません。除外した相手に通知が行くこともありません
- 除外したアカウントの発言は、どれを外すか判定するために Claude が一度読みます。そのうえで、結果にも、まとめの判断にも使いません

## 既知の限界

- 発言者のIDが取れない発言は、表示名で照合します。同じ表示名の別の人の発言が外れることがあります
- 検索は1つの対象につき新しいものから100件までです。超えたときは、結果の冒頭にそう書きます

## 動作確認した環境

- Claude Code デスクトップアプリ(Windows)+ Slack コネクタ
- claude.ai:プラグインの画面からのインストールまで確認済み。設定画面と、設定の保存(メモリ・指示欄)は未確認

## ライセンス

MIT
