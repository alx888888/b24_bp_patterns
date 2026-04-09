# Отправить карточку заказа в личный чат через im.message.add

- Тип: Кейс
- Документация: https://apidocs.bitrix24.com/api-reference/chats/messages/im-message-add.html
- Дополнительно: https://apidocs.bitrix24.com/api-reference/chats/messages/attachments.html

Этот кейс показывает отправку карточки заказа в личный чат сотрудника через `REST Активити Б24` и метод `im.message.add`.

Для личного чата в `DIALOG_ID` передавайте ID сотрудника, например `20`.

Для табличного вывода используйте `ATTACH` и блок `GRID`. BBCode `[table]` чат выводит обычным текстом, поэтому для карточек он не подходит.

## Готовый демо-запрос

```json
{
  "DIALOG_ID": "20",
  "MESSAGE": "[SIZE=20][B]▶️ 1314-1 НАЗНАЧЕН: 09.04.2026, с 08 до 11 ч.[/B][/SIZE]",
  "SYSTEM": "N",
  "URL_PREVIEW": "N",
  "ATTACH": {
    "ID": 1,
    "COLOR_TOKEN": "primary",
    "BLOCKS": [
      {
        "GRID": [
          {
            "NAME": "Источник",
            "VALUE": "УК партнёр",
            "DISPLAY": "ROW",
            "WIDTH": 220
          },
          {
            "NAME": "Адрес",
            "VALUE": "Краснодарский край, Сочи, микрорайон Донская, Пасечная улица, 30, кв. 33",
            "DISPLAY": "ROW",
            "WIDTH": 220
          },
          {
            "NAME": "Домофон / Код",
            "VALUE": "ав",
            "DISPLAY": "ROW",
            "WIDTH": 220
          },
          {
            "NAME": "Подъезд и этаж",
            "VALUE": "ав",
            "DISPLAY": "ROW",
            "WIDTH": 220
          },
          {
            "NAME": "Клиент",
            "VALUE": "Саша тест, 79771532075",
            "DISPLAY": "ROW",
            "WIDTH": 220
          },
          {
            "NAME": "Комментарий",
            "VALUE": "авыавыа",
            "DISPLAY": "ROW",
            "WIDTH": 220
          }
        ]
      },
      {
        "DELIMITER": {
          "SIZE": 1000,
          "COLOR": "#0B8FE8"
        }
      },
      {
        "MESSAGE": "[B]Работы:[/B][BR]1) [URL=https://ospcrm.bitrix24.ru/company/personal/bizproc/123/]Водосчетчик / Монтаж / 15 мм / Внутри квартиры / 7878783333 руб.[/URL][BR]2) [URL=https://ospcrm.bitrix24.ru/company/personal/bizproc/124/]Теплосчетчик / Поверка / 15 мм / Внутри квартиры / 4444 руб.[/URL][BR]3) [URL=https://ospcrm.bitrix24.ru/company/personal/bizproc/125/]Водосчетчик / Замена / 15 мм / Общий доступ / 3333 руб.[/URL][BR]4) [URL=https://ospcrm.bitrix24.ru/company/personal/bizproc/126/]Водосчетчик / Замена / 15 мм / Внутри квартиры / 4466 руб.[/URL]"
      }
    ]
  },
  "KEYBOARD": {
    "BUTTONS": [
      {
        "TEXT": "ЗАВЕРШИТЬ",
        "LINK": "https://ospcrm.bitrix24.ru/company/personal/bizproc/123/",
        "DISPLAY": "LINE",
        "BG_COLOR_TOKEN": "primary",
        "WIDTH": 300
      },
      {
        "TEXT": "ОТМЕНИТЬ",
        "LINK": "https://ospcrm.bitrix24.ru/company/personal/bizproc/124/",
        "DISPLAY": "LINE",
        "BG_COLOR_TOKEN": "base",
        "WIDTH": 300
      }
    ]
  }
}
```

## Что вернет метод

Успешный вызов вернет `result` — ID отправленного сообщения.

Пример:

```json
{
  "result": 34239
}
```

Если нужен `chat_id`, после отправки выполните отдельный вызов `im.dialog.get` с тем же `DIALOG_ID`.

## Параметры метода im.message.add

- `DIALOG_ID` — ID сотрудника для личного чата, `chatXXX` для общего чата, `sgXXX` для группового или проектного чата.
- `MESSAGE` — текст сообщения. Поддерживает BBCode форматирования.
- `ATTACH` — объект или JSON-строка со структурированной карточкой.
- `KEYBOARD` — объект, JSON-строка или сокращенный массив кнопок под сообщением.
- `MENU` — объект, JSON-строка или сокращенный массив пунктов контекстного меню.
- `SYSTEM` — `Y` или `N`. Системное сообщение или обычное сообщение.
- `URL_PREVIEW` — `Y` или `N`. Включение или отключение rich-preview для ссылок.
- `REPLY_ID` — ID сообщения в этом же чате, на которое уходит ответ.

Нюанс по `MENU`:

- официальная документация параметр перечисляет;
- отдельный флаг включения отображения документация не описывает;
- в текущем тесте на портале контекстные пункты не появились, поэтому рабочий кейс идет без `MENU`.

## Корневые параметры ATTACH

- `ID` — произвольный числовой ID вложения.
- `COLOR_TOKEN` — цветовая тема карточки: `primary`, `secondary`, `alert`, `base`.
- `BLOCKS` — массив блоков внутри карточки.

`ATTACH` принимает два формата:

- полный объект с ключом `BLOCKS`;
- сокращенный массив блоков без обертки.

## Доступные блоки ATTACH

- `MESSAGE` — текстовый блок.
- `DELIMITER` — горизонтальный разделитель.
- `GRID` — блок строк или табличных пар `NAME | VALUE`.
- `LINK` — ссылка с заголовком и описанием.
- `IMAGE` — одна картинка или массив картинок.
- `FILE` — один файл или массив файлов.
- `USER` — карточка пользователя, чата или бота.

## Параметры блока MESSAGE

- `MESSAGE` — текст блока. Поддержка BBCode по официальной документации: `USER`, `CHAT`, `SEND`, `PUT`, `CALL`, `BR`, `B`, `U`, `I`, `S`, `URL`.

Примечание:

- `[SIZE]` официальный список не перечисляет, но в верхнем `MESSAGE` текущий клиент портала его отрисовал.

## Параметры блока DELIMITER

- `SIZE` — длина линии в пикселях.
- `COLOR` — цвет линии в формате `#RGB` или `#RRGGBB`.

Практика:

- чем больше `SIZE`, тем длиннее линия;
- проценты и режим `stretch` документация не перечисляет.

## Параметры элемента GRID

- `DISPLAY` — формат вывода: `BLOCK`, `LINE`, `ROW`, `TABLE`.
- `NAME` — подпись поля.
- `VALUE` — значение поля.
- `WIDTH` — ширина блока или колонки в пикселях.
- `HEIGHT` — высота блока в пикселях.
- `COLOR_TOKEN` — цвет значения: `primary`, `secondary`, `alert`, `base`.
- `COLOR` — HEX-цвет значения.
- `LINK` — внешняя ссылка у значения.
- `USER_ID` — внутренняя ссылка на пользователя.
- `CHAT_ID` — внутренняя ссылка на чат.

Подсказки по `DISPLAY`:

- `BLOCK` — поля идут вертикальным списком.
- `LINE` — карточки в одну строку с переносом при нехватке ширины.
- `ROW` — двухколоночный формат `NAME | VALUE`.
- `TABLE` — более плотный табличный режим. В части клиентов рендер повторяет `ROW`.

BBCode для `VALUE`:

- `USER`, `CHAT`, `SEND`, `PUT`, `CALL`, `BR`, `B`, `U`, `I`, `S`, `URL`.

## Параметры блока LINK

- `LINK` — абсолютный URL `http/https` или относительный путь от корня Bitrix24.
- `NAME` — текст ссылки.
- `DESC` — описание под заголовком.
- `HTML` — HTML-описание вместо `DESC`.
- `PREVIEW` — картинка превью.
- `WIDTH` — ширина превью в пикселях.
- `HEIGHT` — высота превью в пикселях.
- `USER_ID` — внутренняя ссылка на пользователя.
- `CHAT_ID` — внутренняя ссылка на чат.
- `NETWORK_ID` — ссылка на пользователя Bitrix24 Network.

## Параметры блока IMAGE

- `LINK` — URL исходной картинки.
- `NAME` — название картинки.
- `PREVIEW` — URL превью. Если параметр пустой, клиент возьмет `LINK`.
- `WIDTH` — ширина картинки в пикселях.
- `HEIGHT` — высота картинки в пикселях.

## Параметры блока FILE

- `LINK` — URL файла.
- `NAME` — отображаемое имя файла.
- `SIZE` — размер файла в байтах.

## Параметры блока USER

- `NAME` — имя в карточке.
- `AVATAR` — URL аватара.
- `LINK` — внешняя ссылка по клику.
- `USER_ID` — внутренняя ссылка на пользователя.
- `CHAT_ID` — внутренняя ссылка на чат.
- `BOT_ID` — внутренняя ссылка на бота.
- `NETWORK_ID` — ссылка на пользователя Bitrix24 Network.
- `AVATAR_TYPE` — тип аватара: `USER`, `CHAT`, `BOT`.

## Параметры KEYBOARD

`KEYBOARD` принимает три формата:

- JSON-строка;
- объект с корнем `BUTTONS`;
- сокращенный массив кнопок без обертки.

Параметры кнопки:

- `TEXT` — текст кнопки.
- `TYPE` — `NEWLINE`. Перенос следующей кнопки на новую строку.
- `LINK` — ссылка кнопки.
- `APP_ID` — ID приложения для сценариев Open Channels.
- `APP_PARAMS` — параметры запуска приложения вместе с `APP_ID`.
- `ACTION` — действие: `PUT`, `SEND`, `COPY`, `CALL`, `DIALOG`.
- `ACTION_VALUE` — значение для `ACTION`.
- `COMMAND` — команда бота.
- `COMMAND_PARAMS` — параметры команды.
- `BLOCK` — блокировка после нажатия: `Y` или `N`.
- `DISABLED` — активность кнопки: `Y` или `N`.
- `CONTEXT` — контекст показа: `MOBILE`, `DESKTOP`, `ALL`.
- `DISPLAY` — способ вывода: `LINE` или `BLOCK`.
- `WIDTH` — ширина кнопки в пикселях.
- `BG_COLOR` — HEX-цвет фона.
- `BG_COLOR_TOKEN` — цветовая тема: `primary`, `secondary`, `alert`, `base`.
- `TEXT_COLOR` — HEX-цвет текста.
- `OFF_BG_COLOR` — цвет фона неактивной кнопки.
- `OFF_TEXT_COLOR` — цвет текста неактивной кнопки.

Практика:

- точную ширину `50% / 50%` документация не описывает;
- `WIDTH` принимает только число в пикселях;
- для двух кнопок в одну строку используйте `DISPLAY: "LINE"` у обеих кнопок;
- для второстепенной кнопки подходят `secondary` или `base`.

## Параметры MENU

`MENU` принимает три формата:

- JSON-строка;
- объект с корнем `ITEMS`;
- сокращенный массив пунктов без обертки.

Параметры пункта меню:

- `TEXT` — текст пункта.
- `LINK` — ссылка.
- `COMMAND` — команда бота.
- `COMMAND_PARAMS` — параметры команды.
- `APP_ID` — ID приложения для сценариев Open Channels.
- `APP_PARAMS` — параметры запуска приложения.
- `ACTION` — действие: `PUT`, `SEND`, `COPY`, `CALL`, `DIALOG`.
- `ACTION_VALUE` — значение для `ACTION`.
- `CONTEXT` — контекст показа: `MOBILE`, `DESKTOP`, `ALL`.
- `DISABLED` — активность пункта: `Y` или `N`.

Практика:

- официальный REST параметр перечисляет;
- в текущем кейсе портал пункты не показал;
- в рабочий пример выше `MENU` не входит.

## Практические заметки по кейсу

- Для личного чата достаточно передать в `DIALOG_ID` ID сотрудника.
- Табличная подача в мессенджере идет через `ATTACH -> GRID`, а не через BBCode `[table]`.
- Для длинной горизонтальной линии поднимайте `DELIMITER.SIZE`, например до `1000`.
- Цвет `DELIMITER` удобно синхронизировать с левой вертикальной полосой через одинаковый HEX.
- `ROW` в `GRID` дает самый стабильный результат в клиентах Bitrix24.
- Если после отправки нужен `chat_id`, дополнительно вызывайте `im.dialog.get`.

## Официальные ссылки

- [im.message.add](https://apidocs.bitrix24.com/api-reference/chats/messages/im-message-add.html)
- [Attachments](https://apidocs.bitrix24.com/api-reference/chats/messages/attachments.html)
- [Working with Keyboards](https://apidocs.bitrix24.com/api-reference/chats/messages/keyboards.html)
- [Working with Context Menu](https://apidocs.bitrix24.com/api-reference/chats/messages/menu.html)
- [Message Formatting](https://apidocs.bitrix24.com/api-reference/chats/messages/formatting.html)
