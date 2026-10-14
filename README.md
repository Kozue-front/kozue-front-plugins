# kozue-front-plugins

Kozue-front が公開している、Claude 用の skill・プラグインのお店(マーケットプレイス)です。お店を1回追加すれば、ここに並んでいるものをどれでも入れられます。あとから増えたものも、自動で候補に出てきます。

## 並んでいるもの

| 名前 | できること | 呼び出し方 |
|---|---|---|
| [slack-filter](plugins/slack-filter/README.md) | Slack から自分に関係のある発言だけを拾ってまとめる。特定のアカウントの発言を結果から外せる | `Slackフィルタ` / `Slackフィルタ設定` |
| [ai-updates-digest](https://github.com/Kozue-front/ai-updates-digest) | Claude のアップデートを集めて裏を取る。今の設定をベストプラクティスで診断する | `Claudeアプデ` / `Claude診断` |

## お店の追加のしかた

### Claude Code

```
claude plugin marketplace add Kozue-front/kozue-front-plugins
```

追加したら、`claude plugin install <名前>@kozue-front-plugins` で入れます。

### Claude(デスクトップアプリ・Web)

1. **カスタマイズ > プラグイン** を開く
2. **「+ 追加」の ▼ →「マーケットプレイスを追加」** に `Kozue-front/kozue-front-plugins` を入れ、**「自動的に同期」**をオンのまま同期する
3. **「探索」** タブに出るものから、使いたいものの **「+」** を押す

## ライセンス

MIT(それぞれのプラグインのフォルダにも記載があれば、そちらに従います)
