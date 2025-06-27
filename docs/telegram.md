# Отправка уведомлений в телеграм

## Часть 1. Создать бота

1. Открыть чат с [@BotFather](https://t.me/BotFather)
2. Ввести команду `/newbot`
3. Введите имя бота. Пример: `OTUS QAJS-2023-12`
4. Введите логин бота. Его имя должно заканчиваться на `_bot`. Пример: `otus_qajs_2023_12_bot`
5. Скопируйте токен, он после пригодится, чтобы отправить сообщение.

## Часть 2. Добавление бота в чат

1. Создайте чат или добавьте бота (по его логину, пример `otus_qajs_2023_12_bot`) в существующий чат.
2. Сделайте бота админом. Дайте ему права на отправку сообщений в этот чат.
3. Тегните вашего бота в чате.
4. Откройте в браузере https://api.telegram.org/bot${TOKEN}/getUpdates где ${TOKEN} замените, на ранее полученный токен для бота
5. Из ответа телегерамма, скопируйте chatId вашей группы. Ниже пример: ID группы = `-1001626407590`

```json
{
  "ok": true,
  "result": [
    {
      "update_id": 235771293,
      "channel_post": {
        "message_id": 15,
        "sender_chat": {
          "id": -1001626407590,
          "title": "OtusTest",
          "type": "channel"
        },
        "chat": {
          "id": -1001626407590,
          "title": "OtusTest",
          "type": "channel"
        },
        "date": 1708429332,
        "text": "@otus_qajs_2023_12_bot",
        "entities": [
          {
            "offset": 0,
            "length": 22,
            "type": "mention"
          }
        ]
      }
    }
  ]
}
```

## Часть 3.

Отправим сообщение в чат:

```bash
curl --request POST \
  --url https://api.telegram.org/bot${TOKEN}/sendMessage \
  --header 'Content-Type: application/json' \
  --data '{
	"chat_id": "${CHAT_ID}",
	"text": "Hello, Telegram!"
}'
```

${TOKEN} замените, на ранее полученный токен для бота
${CHAT_ID} замените на ID группы (чата) в телеграм

При необходимости, мы можем написать в чат и из тестов:

```js
fetch(`https://api.telegram.org/bot${TOKEN}/sendMessage`, {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  data: JSON.stringify({
    chat_id: '...',
    text: 'Hello, Telegram!',
  }),
})
```