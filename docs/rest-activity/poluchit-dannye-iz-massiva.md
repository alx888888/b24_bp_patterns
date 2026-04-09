# Получить данные из массива

- Раздел: Примеры использования
- URL: https://it-solution.kdb24.ru/public/restactivity/a318489/
- Последнее изменение: 28.12.2024 17:44:36

## Запрос

Если есть json с массивом внутри, то для получения всех данных массива с разбитием их на значения нужно добавить ".*" в конце запроса. В этом случае массив вида [15653, 15655] будет разбит на элементы массива:

![](https://it-solution.kdb24.ru/api/v1/froala/file_proxy/464093/3165/958592/?sig=56eb50807c940619b5ba6f42d1a7e81b)

## Пример

Пример БП будет выглядеть так:

![](https://it-solution.kdb24.ru/api/v1/froala/file_proxy/464323/3165/959112/?sig=14f43bdbe8447323b25eddc50393c321)

По блокам:

1. Получаем данные о файлах в задаче:

![](https://it-solution.kdb24.ru/api/v1/froala/file_proxy/464325/3165/959115/?sig=26da3e0014f9e3024c9e7425413120ef)

2. Итерируемся по результатам 1 jsonpath:

![](https://it-solution.kdb24.ru/api/v1/froala/file_proxy/464327/3165/959120/?sig=eff9137956fe5a4e294e52895195050c)

3. Выводим в отчет:

![](https://it-solution.kdb24.ru/api/v1/froala/file_proxy/464328/3165/959127/?sig=bf9164dc0564bb076ed7a5e88b783933)

Вот так будет выглядеть лог:

![](https://it-solution.kdb24.ru/api/v1/froala/file_proxy/464331/3165/959136/?sig=6453a0f0c677086a9dc44c5b73123597)