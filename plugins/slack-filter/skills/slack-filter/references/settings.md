# 設定の置き場所と形

## 置き場所

環境によって置き場所が違う。上から順に試し、最初に見つかったものを使う。

| 環境 | 読む場所 | 保存する場所 |
|---|---|---|
| ファイルを読み書きできる環境(Claude Code など) | `~/.claude/slack-filter/settings.json` | 同じファイル |
| ファイルが無い環境(claude.ai のチャットなど) | 指示欄・メモリ・会話の中にある「【Slackフィルタ設定】」で始まる文 | メモリに保存できればメモリへ。できなければ、下の「貼り付け用の文」を出して、使う人に指示欄へ貼ってもらう |

保存先はPCの中か、使う人の指示欄・メモリに限る。共有されるもの(Artifact の共有データ、リポジトリ、Slack への投稿など)には置かない。外したアカウントの名前が入っているため。Slack には何も書き込まない。

## settings.json の形

```json
{
  "version": 1,
  "me": { "slack_user_id": "U0123ABCD" },
  "exclude": [
    {
      "name": "表示名(画面に出すためと、IDが取れない発言の照合に使う)",
      "slack_user_id": "U0456EFGH",
      "mode": "block",
      "scene": { "mention": "ask", "my_thread": "drop", "elsewhere": "drop" }
    }
  ],
  "pick": {
    "mentions": true,
    "my_threads": true,
    "bots": false,
    "topics": [
      { "name": "Webサイトの改修", "keywords": ["サイト", "LP", "改修"] }
    ]
  },
  "channels": [ { "name": "#marketing", "id": "C0789IJKL" } ],
  "output": "summary",
  "reopen": { "rule": "3d", "next_at": "2026-10-17" },
  "updated_at": "2026-10-14T09:50:00+09:00"
}
```

- `mode`:`block`(ブロック)/ `scene`(場面ごとに決める)。`scene` は `mode` が `scene` のときだけ使う
- `scene` の値:`drop`(消す)/ `ask`(確認する)/ `keep`(残す)。`mention` は①自分宛て、`my_thread` は②自分がいるスレッド、`elsewhere` は③関係のない場所
- `pick.topics` を使わないときは空の配列
- `pick.bots`:ボットや自動通知(CI・勤怠・監視ツールなどの自動投稿)を結果に入れるか。`false` でも件数だけは結果の最後に1行で出す。この項目が無い古い設定は `false` とみなす
- `channels` が空なら、自分が参加しているチャンネルすべて
- `output`:`summary`(要約)/ `list`(発言の一覧)
- `reopen.rule`:`every`(毎回)/ `3d` / `1w` / `month`(月が変わったあと最初)/ `manual`(自分で言う)。`next_at` は `every` と `manual` では `null`

## 画面から届く文と、保存する値の対応

画面の「設定を記録する」を押すと、次のような文が届く(全角の「：」「（）」で届く)。これを上の形に直して保存する。

```
【Slackフィルタ設定】
除外する人：
- 山田：ブロック
- 佐藤：場面ごと（①メンション=確認する ②自分のスレッド=消す ③関係ない場所=消す）
拾うもの：自分宛てのメンション、自分がいるスレッド、話題（Webサイトの改修、メルマガの配信）
ボットや自動通知：拾わない（件数だけ出す）
読むチャンネル：参加しているチャンネルすべて
結果の形：要約
次に設定画面を開く：3日後
```

外す人がいないときは「除外する人：なし」の1行で届く。

| 画面の言葉 | 保存する値 |
|---|---|
| ブロック / 場面ごと | `mode`: `block` / `scene` |
| 消す / 確認する / 残す | `drop` / `ask` / `keep` |
| 拾う / 拾わない(件数だけ出す) | `pick.bots`: `true` / `false` |
| 要約 / 発言の一覧 | `output`: `summary` / `list` |
| 毎回 / 3日後 / 1週間後 / 月初 / 自分で言う | `reopen.rule`: `every` / `3d` / `1w` / `month` / `manual` |

## 貼り付け用の文(ファイルが無い環境)

メモリにも保存できないときは、届いた「【Slackフィルタ設定】」の文に、IDを書き足したものを出す。

```
【Slackフィルタ設定】（この文を指示欄に貼ってください）
自分：U0123ABCD
除外する人：
- 山田（U0456EFGH）：ブロック
...
次に設定画面を開く：2026-10-17 以降
```

## 画面に渡す初期値

`assets/settings_screen.html` の `__INITIAL_STATE__` を、次の形のJSONに置き換える。前回の設定があればその値、無ければ下の既定値を入れる。埋め込む前に `<` を `\u003c`(バックスラッシュ・u・0・0・3・c の6文字)に置き換える(理由は SKILL.md の手順A-4)。

```json
{
  "people": [
    { "name": "山田", "mode": "ブロック", "m": "確認する", "t": "消す", "o": "消す" }
  ],
  "mention": true,
  "thread": true,
  "topic": true,
  "bots": false,
  "topics": [
    { "n": "Webサイトの改修", "c": 14, "sel": true }
  ],
  "channels": "#marketing, #web-renewal",
  "shape": "要約",
  "next": "3日後"
}
```

- 画面は**画面の言葉そのもの**で値を判定する。`settings.json` の値(`block` など)を入れると、どの選択肢も選ばれていない表示になる
  - `people[].mode`:`"ブロック"` か `"場面ごとに決める"`
  - `people[].m` `t` `o`(①②③):`"消す"` `"確認する"` `"残す"` のどれか
  - `shape`:`"要約"` か `"発言の一覧"`
  - `next`:`"毎回"` `"3日後"` `"1週間後"` `"月初"` `"自分で言う"` のどれか
- `topics[].c` は候補づくりで数えた件数。前回選ばれていて今回の発言に無かった話題は `c: 0`(画面には「0件」と出る)
- 既定値:`people` は空、`mention` `thread` `topic` は `true`、`bots` は `false`、`channels` は空文字、`shape` は「要約」、`next` は「3日後」(使い始めは調整が多いため)
