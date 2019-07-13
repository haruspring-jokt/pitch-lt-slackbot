# Slack Botを作ってみました

---

## 目次

1. Slack Botの種類
2. 今回導入したBotの仕様
3. 今後の開発方針

---

## Slack Botの種類

---

![Slack Botの種類と大まかな作り方 - Qiita](https://i.imgur.com/i0C9Gcx.jpg)

*[Slack Botの種類と大まかな作り方 - Qiita](https://qiita.com/namutaka/items/233a83100c94af033575)*

---

### Real Time Messaging API

Botユーザーと会話する形式

![](https://i.imgur.com/QGk1YYC.jpg)

---

チャンネル内の特定の投稿に応じて処理が開始される

![](https://i.imgur.com/d2tFzSz.jpg)

---

Pythonでは[Slackbot](https://github.com/lins05/slackbot)というライブラリで発火条件を簡単に書ける

```python
@listen_to(r'^htb$')
@listen_to(r'^hatebu$')
@listen_to(r'^はて(ぶ|ブ|な|なブックマーク)$')
def post_htb_hotentry(message, *args):

    print(f'[info] listen to message. text=[{message.body["text"]}]')
    channel = message.body['channel']
    slack_messenger.post(
        "はてブのホッテントリを取得します",
        channel,
        as_user=False,
    )

    htb_service.send_htb_hotentry(channel)
```

---

#### 個人用に作ったRTM Slack Botの構成

![](https://i.imgur.com/Mt9fjRs.jpg)

---

この構成だと割と自由に作ることができるが、Herokuの管理が面倒、もっとサクッとつくりたい

---

## 今回導入したBotの仕様

---

### Event API

*[必要な Slack API はどれ？ - Slack アプリの作成のためのヒント](https://api.slack.com/lang/ja-jp/which-api)*

---

> Events API は、イベント情報のサブスクリプションを使用して JSON ペイロードをサーバーに送信します。サーバーではペイロードの受信を行い、どのように応答するか (独自の API、Slack の API、その他の API のどれを呼び出すか) を決めます。Slack アプリの設定ページから、チームとボットユーザーのイベントをサブスクライブできます。

---

![](https://i.imgur.com/ZBYelfr.jpg)

Lambdaと組み合わせるのでサーバーの設定は不要、実装に集中できる

---

#### 実装方法

詳しくは[ブログ参照](https://m4usta13ng.hatenablog.com/entry/2019/07/05/204258)、以下ざっくり説明

---

検知したいワークスペース内のイベントを選択する

![](https://i.imgur.com/UX6H1bW.jpg)

---

検知したイベントの送信先（API Gateway）を指定する

![](https://i.imgur.com/hzYg3NE.jpg)

---

Lambda内で実装する

![](https://i.imgur.com/HnHDCOn.jpg)

---

イベントに応じたJSONが来るので、それに応じて処理を実行

```python
    if is_channel_created_event(slack_event):
        post_channel_created(slack_event)
```

---

```python
    # Slackにメッセージを投稿する
    post_message_to_slack_channel(
        f"新しいチャンネルが作成されました: #{channel_name}",
        os.environ["DIST_CHANNEL_CH_CREATED"])
```

---

![](https://i.imgur.com/mMIcRu3.jpg)

![](https://i.imgur.com/odPlyoj.jpg)

---

#### 他に何ができる？

読めるイベントの一覧

*[Event Types for the Events API | Slack](https://api.slack.com/events/api)*

---

- channel_left
- menber_joined_channel |
- pin_added |
- subteam_created |
- and more! |

---

## 今後の開発方針

---

要望や不便なことがあればBotで解決できるか検討します

---

### example

- チャンネルに加入した場合に固定メッセージを表示する
- ファイルの場所などを検索する |
- 他ツールと連携し通知を投稿する |

---

ガンガン自動化していきましょう
