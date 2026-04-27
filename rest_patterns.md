# REST и вебхуки

## Входящие

```php
$title = urlencode('Новая сделка');
$comment = urlencode('Комментарий к новой сделке');
$url = "https://yourdomain.bitrix24.ru/rest/1/your_webhook_token/crm.deal.add?fields[TITLE]={$title}&fields[COMMENTS]={$comment}";
```

#### Завершить задачу:
https://bta.bitrix24.ru/rest/212/wvlg431n3gpwqyfo/tasks.task.complete?taskId={=Document:UF_CRM_1645912491}

#### Завершить дело:
https://rus-line.bitrix24.ru/rest/3178/65s7isk1fo68a6hb/crm.activity.update?id={{Дело ID}}&fields[COMPLETED]=Y

#### Обновить название чата:
{{Константы глобальные: Вебхук для БП}}im.chat.updateTitle?CHAT_ID={{ID чата}}&TITLE={{=urlencode({=Variable:temp})}}

#### Отправка системного сообщения в диалог:
https://compass.bitrix24.ru/rest/41059/64wii9ojy8qp3vai/im.message.add?DIALOG_ID=chat52399&MESSAGE=ВАШ_ТЕКСТ_СООБЩЕНИЯ&SYSTEM=Y

#### Остановить два бизнес-процесса и запустить новый через batch:
https://mobileocean.bitrix24.ru/rest/186/izghgiqpd090a9ht/batch.json?halt=1&cmd[start_new]=bizproc.workflow.start%3FTEMPLATE_ID%3D248%26DOCUMENT_ID%5B0%5D%3Dcrm%26DOCUMENT_ID%5B1%5D%3DBitrix%5CCrm%5CIntegration%5CBizProc%5CDocument%5CDynamic%26DOCUMENT_ID%5B2%5D%3DDYNAMIC_1036_24&cmd[stop_control]=bizproc.workflow.terminate%3FID%3D69e60ab37fabd8.44693628%26STATUS%3DStopped%20from%20BP&cmd[stop_self]=bizproc.workflow.terminate%3FID%3D69e60aa3a0aac1.81996900%26STATUS%3DStopped%20from%20BP

## REST Активити Б24

#### Получить список себестоимостей товаров:

Метод:
`catalog.product.list`

Запрос:
```json
{
  "select": ["id", "iblockId", "name", "purchasingPrice"],
  "filter": {
    "iblockId": 14,
    "id": [238, 248, 240]
  },
  "order": {
    "id": "asc"
  }
}
```

JSONPath:
`$.products.*.purchasingPrice`

#### Получить связанные элементы смарт-процесса с сортировкой по полю номера:

Метод:
`crm.item.list`

Запрос:
```json
{
  "entityTypeId": 1044,
  "useOriginalUfNames": "Y",
  "select": [
    "id",
    "title",
    "stageId",
    "categoryId",
    "parentId1038",
    "UF_CRM_10_1775151696181",
    "UF_CRM_10_1775699211906"
  ],
  "filter": {
    "parentId1038": 40,
    "categoryId": 20
  },
  "order": {
    "UF_CRM_10_1775151696181": "ASC"
  }
}
```

JSONPath:
`$.items[*].UF_CRM_10_1775699211906`

#### Отправить карточку выезда в личный чат через im.message.add:

Метод:
`im.message.add`

Запрос:
```json
{
  "DIALOG_ID": "{{=substr({{Константы глобальные: Бригадир}}, 5)}}",
  "MESSAGE": "[SIZE=20]▶️ [B]{{Сделка}}-{{_Номер выезда}} НАЗНАЧЕН:[/B] {{Плановая дата выезда}}, {{Диапазон времени (текст)}}[/SIZE]",
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
            "VALUE": "{=A43494_16201_78654_83104:SOURCE_ID > printable}",
            "DISPLAY": "ROW",
            "WIDTH": 200
          },
          {
            "NAME": "Адрес",
            "VALUE": "{{Адрес выезда}}",
            "DISPLAY": "ROW",
            "WIDTH": 200
          },
          {
            "NAME": "Домофон / Код",
            "VALUE": "{{Домофон / Код}}",
            "DISPLAY": "ROW",
            "WIDTH": 200
          },
          {
            "NAME": "Подъезд и этаж",
            "VALUE": "{{Подъезд и этаж}}",
            "DISPLAY": "ROW",
            "WIDTH": 200
          },
          {
            "NAME": "Клиент",
            "VALUE": "{{Контакты клиента}}",
            "DISPLAY": "ROW",
            "WIDTH": 200
          },
          {
            "NAME": "Комментарий",
            "VALUE": "{{Комментарий для мастера}}",
            "DISPLAY": "ROW",
            "WIDTH": 200
          }
        ]
      },
      {
        "DELIMITER": {
          "SIZE": 600,
          "COLOR": "#0B8FE8"
        }
      },
      {
        "MESSAGE": "[B]Работы:[/B][BR]{=A84142_96083_1306_13335:rest_result_1}{=A84142_96083_1306_13335:rest_result_2}{=A84142_96083_1306_13335:rest_result_3}{=A84142_96083_1306_13335:rest_result_4}"
      }
    ]
  },
  "KEYBOARD": {
    "BUTTONS": [
      {
        "TEXT": "✓ ЗАВЕРШИТЬ ВЫЕЗД",
        "LINK": "https://ospcrm.bitrix24.ru/company/personal/bizproc/{{_ID задания по выезду}}/",
        "DISPLAY": "LINE",
        "BG_COLOR_TOKEN": "primary",
        "WIDTH": 200
      },
      {
        "TEXT": "→ ПОЗВОНИТЬ КЛИЕНТУ",
        "ACTION": "CALL",
        "ACTION_VALUE": "{=A43494_16201_78654_83104:UF_CRM_1775155723330}",
        "DISPLAY": "LINE",
        "BG_COLOR_TOKEN": "primary",
        "WIDTH": 200
      },
      {
        "TEXT": "✗ ОТМЕНИТЬ / ПЕРЕНОС",
        "LINK": "https://ospcrm.bitrix24.ru/company/personal/bizproc/124/",
        "DISPLAY": "LINE",
        "BG_COLOR_TOKEN": "alert",
        "WIDTH": 200
      }
    ]
  }
}
```

#### Перенести товарные строки из счёта в сделку через batch:

Метод:
`batch`

Запрос:
```json
{
  "halt": 1,
  "cmd": {
    "get_rows": "crm.item.productrow.list?filter%5B%3DownerType%5D=SI&filter%5B%3DownerId%5D={{ID}}&order%5Bsort%5D=asc",
    "set_rows": "crm.item.productrow.set?ownerType=D&ownerId={{Сделка}}&productRows=$result[get_rows][productRows]"
  }
}
```

Примечание:
`{{ID}}` — ID счёта, `{{Сделка}}` — ID сделки. URL-кодирование `filter%5B%3DownerType%5D` и `filter%5B%3DownerId%5D` нужно для batch, иначе Битрикс24 возвращает ошибку `REQUIRED_ARG_MISSING` по аргументу `=ownerType`.
