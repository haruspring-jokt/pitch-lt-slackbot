# Slack Botを作ってみました

---

## 目次

1. Slack Botの種類
2. 今回導入したBotの仕様
3. 今後の開発方針

---

## Slack Botの種類

![Slack Botの種類と大まかな作り方 - Qiita](https://i.imgur.com/Dr98jHg.jpg)

*https://qiita.com/namutaka/items/233a83100c94af033575*

---

### Real Time Messaging API

Botユーザーと会話する形式

![](https://i.imgur.com/QGk1YYC.jpg)

---

メンション（`@bot_name`）やチャンネル内の特定の投稿に応じて処理が開始される

![](https://i.imgur.com/d2tFzSz.jpg)

---

Pythonでは[Slackbot](https://github.com/lins05/slackbot)というライブラリがあるので発火条件を簡単に書ける

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

### 個人用に作ったRTM Slack Botの構成

![](https://i.imgur.com/Mt9fjRs.jpg)

---

この構成だと割と自由に作ることができるが、Herokuの管理が面倒、もっとサクッとつくりたい

---


