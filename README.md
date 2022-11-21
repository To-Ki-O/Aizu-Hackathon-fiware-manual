# 会津ハッカソンFIWARE利用説明

## Orionの利用方法

### データの追加

```curl https://orion.c-3lab.org/v2/entities -H "Content-Type: application/json" -H "Authorization: ${APIキー}" -H "Fiware-Service: ${チームごとのテナント名}" -X POST -d @${追加データ}```

追加データはEOFで記述することも可能です。

### データの取得

```curl https://orion.c-3lab.org/v2/entities -H "Authorization: ${APIキー}" -H "Fiware-Service: ${チームごとのテナント名}"```

entities以下のパスを指定することでアトリビュートの値などを取得することが出来ます。
詳細は以下の学習コンテンツPart2をご参照ください。
* https://github.com/c-3lab/fiware-learning-handson

## データ全体の更新

```curl https://orion.c-3lab.org/v2/entities/${変更したいエンティティ名}/attrs -H "Content-Type: application/json" -H "Authorization: ${APIキー}" -H "Fiware-Service: ${チームごとのテナント名}" -X PUTCH -d @${更新データ}```

更新データはEOFで記述することも可能です。

## データ一部の更新

```curl https://orion.c-3lab.org/v2/entities/${変更したいエンティティ名}/attrs/${更新したいアトリビュート名}/value -H "Content-Type: text/plain" -H "Authorization: ${APIキー}" -H "Fiware-Service: ${チームごとのテナント名}" -X PUT -d ${更新後の値}```

# 時系列データの利用方法

## 時系列データの登録

時系列データを追加していくためにはOrionにサブスクリプションを設定する必要があります。
以下にサブスクリプションを設定するための例を提示します。

```
curl -i https://orion.c-3lab.org/v2/subscriptions -H "Content-Type: application/json" -H "Authorization: ${APIキー}" -H "Fiware-Service: ${チームごとのテナント名}" -H "fiware-servicepath: /" -d @- <<EOF
{
  "description": "${サブスクリプションの説明}",
  "subject": {
      "entities": [
      {
          "idPattern": "${変更を検知するエンティティ名}",
          "type": "${変更を検知するエンティティのタイプ}"
      }
      ],
      "condition": {
          "attrs": [
          "${変更を検知するアトリビュート名}"
          ]
      }
  },
  "notification": {
      "http": {
          "url": "http://quantumleap:8668/v2/notify"
      },
      "attrs": [
      "${変更があった際に送信するアトリビュート名}"
      ],
      "metadata": ["dateCreated", "dateModified"]
  },
  "throttling": 1
}
EOF
```

アトリビュートの値に変更があった際にデータ記録先にデータをPOSTする設定です。

## 時系列データの取得

登録されているすべてのデータを取得する例を以下に提示します。

```curl https://quantumleap.c-3lab.org/v2/entities -H "Authorization: ${APIキー}" -H "Fiware-Service: ${チームごとのテナント名}" -H "fiware-servicepath: /"```

entities以下のパスを指定することでアトリビュートやタイプごとのデータ取得が可能です。
以下のURLにパスの指定方法が記載されている公式のドキュメントがありますので、queriesの欄をご参照ください。
	※GET以外のAPIは利用を制限させていただいております。ご了承ください。
* https://app.swaggerhub.com/apis/smartsdk/ngsi-tsdb/0.8.3#/