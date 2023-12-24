---
title: Googleカレンダーの予定をSlackに連携する with GAS
tags:
  - GoogleCalendar
  - Slack
  - slackbot
  - slack-api
  - GAS
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

## やりたいこと

毎日特定の時間になったら、Googleカレンダーに登録されている当日の予定を取得し、指定のSlackチャネルに通知する。
予定が複数あった場合も、すべて表示する。

### 完成イメージ

![スクリーンショット 2023-12-25 063305.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/d9bd45d4-f631-87b0-1d0c-a87d592e0ed5.png)

## 悩んだところ

### GASのSlackAppライブラリの問題

「GAS Slack 連携」で検索して出てくる多くの記事で`SlackApp`(M3W5Ut3Q39AaIwLquryEPMwV62A3znfOO)というライブラリを使用する方法が紹介されています。
しかし2023/11/8時点では、既に使用できなくなっていました。(Slackに通知が飛ばない)

代替手段として、SlackのWebhookを使用する方法とSlack APIを使用する方法を検討した結果、以下の理由で最終的にSlack APIを使用しました。


### Webhookの問題点

Webhookの方がSlack APIより簡易的に連携で来たのですが、Webhookの場合に以下のことが出来ませんでした。

**複数行の引用ブロックができない**

通常Slackでは、引用ブロックを複数行にしたい時に`Shift + Enter`で実現出来ます。
しかしWebhookの場合は、これの実現手段がありません。
Slack APIでは`>>>`とすることにより、引用ブロック内での改行が実現出来ました。

**URLをハイパーリンク化できない**

WebhookではURLをそのまま送信しても、ただの文字列として扱われてしまいます。
Slack APIではURLを`< >`で囲むことにより、ハイパーリンクとして認識させることが出来ます。
例) `<https://example.com>`

## 実際のコード

```javascript
function execute() {

  // Slackボットがメッセージを投稿するチャンネルを定義する
  const channelId = "XXXXXXXXXX"; // 通知したいSlackのチャンネルID

  // SlackAPIで登録したボットのトークンを設定する
  const token = 'xoxb-XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX';
  const url = "https://slack.com/api/chat.postMessage";

  // 実行ユーザーのデフォルトであるGoogleカレンダーを取得する
  const myCalendar = CalendarApp.getDefaultCalendar();

  // Googleカレンダーの予定取得する日を設定する
  const calDate = new Date();

  // 今日1日のGoogleカレンダーのイベントを取得する
  const myEvent = myCalendar.getEventsForDay(calDate);

  // イベントがなかったら終了
  if (myEvent.length === 0) {
    console.info('イベント無し');
    return;
  }

  // Slackに通知するGoogleカレンダーの予定メッセージを作成
  const blocks = [];
  blocks.push(createMessage(`本日の予定です`));

  for(let i = 0; i < myEvent.length; i++){
    let startH = myEvent[i].getStartTime().getHours();
    let startM = myEvent[i].getStartTime().getMinutes();
    if(startM < 10) startM = '0' + startM;
    let endH = myEvent[i].getEndTime().getHours();
    let endM = myEvent[i].getEndTime().getMinutes();
    if(endM < 10) endM = '0' + endM;

    const timeString = `${myEvent[i].getStartTime().getMonth() + 1}/${myEvent[i].getStartTime().getDate()}　${startH}:${startM} ～ ${endH}:${endM}`;
    const locationString = myEvent[i].getLocation() ? `<https://www.google.com/maps/search/?api=1&query=${myEvent[i].getLocation()}|${myEvent[i].getLocation()}>` : '';

    blocks.push(createMessage(`
>>> *${myEvent[i].getTitle()}*
⏰ ${timeString}
📍 ${locationString}
📝 ${myEvent[i].getDescription()}
`
    ));
  }

  const headers = {
    "Content-Type": "application/json; charset=utf-8",
    "Authorization": "Bearer " + token
  };
  const payload = {
    "channel": channelId,
    "blocks": blocks
  };
  const params = {
    "method": "POST",
    "headers": headers,
    "payload": JSON.stringify(payload)
  };

  UrlFetchApp.fetch(url, params);

  console.info('Success');
}

/* メッセージを作成 */
function createMessage(message) {
  return {
      "type": "section",
      "text": {
        "type": "mrkdwn",
        "text": message
      }
    };
}
```

### チャンネルIDとAPIトークンの取得方法

それぞれ以下の記事を参考にして、取得してください。

- チャンネルID

https://zenn.dev/dashi296/articles/4324507780a3cf

- APIトークン

https://qiita.com/ykhirao/items/3b19ee6a1458cfb4ba21