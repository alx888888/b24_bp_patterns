# Как заполнить поле "Данные", если выбран формат отправки данных FormData?

- Раздел: Примеры использования
- URL: https://it-solution.kdb24.ru/public/restactivity/a263173/
- Последнее изменение: 24.04.2024 14:26:47

FormData - это Content-Type "multipart/form-data".   
То есть передача содержимого по частям.   

**❗️****Если используется формат отправки данных FormData, то рекомендуем выбрать HTTP-метод POST**

**Пример**

Выберем HTTP-метод POST, а формат отправки данных - FormData.  
Поле данные заполним таким образом, как указано на скрине ниже.

![](https://it-solution.kdb24.ru/api/v1/froala/file_proxy/385098/3165/785608/?sig=83fef89242bcb722c0506e49649164b6)

Сервер получит данные в таком формате, как указано на скрине ниже.

![](https://it-solution.kdb24.ru/api/v1/froala/file_proxy/385097/3165/785607/?sig=ea29250c7eedca76162b90a1308b4c29)

Если HTTP-метод сменим на PUT, а формат отправки данных и данные оставим такими же,   
то данные передадутся в body таким образом, как указано на скрине ниже.   

![](https://it-solution.kdb24.ru/api/v1/froala/file_proxy/385100/3165/785609/?sig=97a270b54a1b42a888deecf945f8b752)

Больше о FormData можно узнать [по ссылке](https://datatracker.ietf.org/doc/html/rfc7578)