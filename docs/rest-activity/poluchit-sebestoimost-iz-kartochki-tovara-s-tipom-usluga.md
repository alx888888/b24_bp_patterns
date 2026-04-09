# Получить себестоимость из карточки товара с типом «Услуга»

- Раздел: Примеры использования
- URL: https://it-solution.kdb24.ru/public/restactivity/a292175/
- Последнее изменение: 17.01.2025 11:39:15

Допустим, Вам нужно получить значение из поле «Себестоимость» для бизнес-процесса. Это можно сделать с помощью использования приложения REST Активити.

**Что понадобится**

Коробка/Облако. Тариф, который поддерживает работу бизнес-процессов. Приложение REST Активити.

Подробнее о тарифах можно узнать по [ссылке](https://www.bitrix24.ru/prices/compare_cloud_plans.php).

**Ссылки на полезные ресурсы**

<https://www.bitrix24.ru/apps/?app=itsolutionru.restactivity>

[Основные положения (1c-bitrix.ru)](https://dev.1c-bitrix.ru/rest_help/index.php)

**Решение**

В конструкторе бизнес-процессов используйте действие приложения «Получить параметр через REST» и выполните следующие шаги:

Шаг 1.

Создайте переменную, в которой в дальнейшем будет храниться себестоимость товаров

![](https://it-solution.kdb24.ru/api/v1/froala/file_proxy/412940/3165/847624/?sig=0d4cb4debe3e3052888a6001831d0cad)

Шаг 2. Добавьте в бизнес-процесс действие приложения «Получить параметр через REST»

![](https://it-solution.kdb24.ru/api/v1/froala/file_proxy/413502/3165/848921/?sig=aba18cac19bad2900b4733b46e4d94e7)

Шаг 3. Воспользуйтесь методом «crm.product.list» чтобы получить информацию о товаре.

Здесь обязательно нужно указать в параметрах filter и select значения полей id и iblockID, также в select непосредственно укажем себестоимость – purchasingPrice. В JSONPath укажем $..purchasingPrice

![](https://it-solution.kdb24.ru/api/v1/froala/file_proxy/413495/3165/848917/?sig=bd7cfe1dbcf09934b70df630418e2db3)

Откроем товар и посмотрим его ID в адресной строке.  

Товар имеет ID 11, при этом iblockID указан как 15, но не спешите его вбивать в REST.

![](https://it-solution.kdb24.ru/api/v1/froala/file_proxy/435303/3165/900705/?sig=4e41ab1264b1063a6d225ab250ace53d)

Чтобы узнать истинный iblockID делаем REST запрос методом get.

```
```

Как видим, iblockID здесь 17, а не 15, как было в адресной строке.

![](https://it-solution.kdb24.ru/api/v1/froala/file_proxy/435266/3165/900633/?sig=76d76faa4eb921a01b0b84a14e7674a4)

Теперь делаем запрос методом list и получаем закупочную цену товара.

```
```

![](https://it-solution.kdb24.ru/api/v1/froala/file_proxy/435269/3165/900635/?sig=4f0b1663dd8b275d0674c9ca67657564)


Также обратите внимание на следующую особенность: <https://dev.1c-bitrix.ru/rest_help/catalog/product/catalog_product_list.php>   
![](https://it-solution.kdb24.ru/api/v1/froala/file_proxy/435273/3165/900642/?sig=5a0e30cea8161216c481018af3990a60)

Шаг 4. Запишите результат в переменную:

![](https://it-solution.kdb24.ru/api/v1/froala/file_proxy/413494/3165/848915/?sig=bf477c5b2ee3274f227debf3f0f6613b)