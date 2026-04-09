# REST Активити: SnApi

- Раздел: Возможности приложения
- URL: https://it-solution.kdb24.ru/public/restactivity/a415632/
- Последнее изменение: 21.10.2025 15:58:28

SnAPI - это сервис для вебхуков, разработанный IT-Solution.

# С чего начать

|  |  |  |  |
| --- | --- | --- | --- |
| Поле | Скриншот | Пояснение | Пример |
| Метод snapi.it-solution.ru |  | Метод REST который вы используете | test.helloworld |
| Параметры REST-запроса (Параметры для выбранного метода) |  | Параметры запроса. Правильный формат запросов под каждый метод можно найти в документации ниже. (Возможные форматы запросов json/yaml/xml/toml) | title: "Привет, Мир!" |
| JSONPath |  | JSONPath — это язык запросов, предназначенный для выбора и извлечения нужных данных из документа в формате JSON.   Справка по синтаксису: <https://goessner.net/articles/JsonPath/index.html> | $.NAME |
| Пользователь для отладки: |  | Данный пользователь будет получать отладочные уведомления | Иван Иванов [1] |

---

# Методы snapi.it-solution.ru

## ⇒ test.helloworld

Создает пост в живую ленту. Принимает форматирование Froala.

**Шаблон хука**

https://snapi.it-solution.ru/api/test.helloworld/?auth=String&bitrix_domain=String&title=String

**Параметры**

|  |  |  |  |  |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| Параметр | Тип | | Обязательный | null? | Значение по умолчанию | Описание |
| auth | String | Строка | Да | Нет |  | Авторизационный токен для запроса к REST-API Битрикс24 для вебхука.  Пример формата если id пользователя 1:  ``` 1/4EStwwVYilbktoyg ``` |
| bitrix_domain | String | Строка | Да | Нет |  | Домен портала, например portal.bitrix24.ru |
| title | String | Строка | Нет | Нет | "Hellow, World!" |  |

↪ Исходный код

```
def hello_world(bitrix_user_token, params, hook, its_params, *args, **kwargs):
    response = bitrix_user_token.call_api_method(
        api_method='log.blogpost.add',
        params=dict(
            POST_TITLE=params.get('title', 'Hello, world!'),
            POST_MESSAGE=lorem_ipsum.paragraph(),
        ),
    )

    return dict(
        text='Написан пост в живую ленту: https://{}/stream/'
             .format(bitrix_user_token.domain),
        response=response,
    )
```

## ⇒ return_in_base64

Преобразует файл в формат base64.

**Шаблон хука**

https://snapi.it-solution.ru/api/return_in_base64/?auth=String&bitrix_domain=String&url=String&with_file_name=Boolean

**Параметры**

|  |  |  |  |  |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| Параметр | Тип | | Обязательный | null? | Значение по умолчанию | Описание |
| auth | String | Строка | Да | Нет |  | Авторизационный токен для запроса к REST-API Битрикс24 для вебхука.  Пример формата если id пользователя 1:  ``` 1/4EStwwVYilbktoyg ``` |
| bitrix_domain | String | Строка | Да | Нет |  | Домен портала, например portal.bitrix24.ru |
| url | String | Строка | Да | Нет |  | Ссылка на файл |
| with_file_name | Boolean | Булев | Нет | Нет | false | Определять название файла.  Если истина, то помимо содержимого передает еще оригинальное название файла. Если истина то формат результата всей функции будет такой массив:  [{file_name}, {content}]  - где "{file_name}" - строка имя файла, а "{content}" - строка содержимое файла в base64  Если ложь, то формат результата всей функции будет просто строка-содержимое файла в base64. |

**Пример использования**

Python

```
bitrix_user_token.call_api_method_v2(
    api_method='user.update',
    params={
        'ID': 1,
        'PERSONAL_PHOTO': ['mark_face.jpg', response]  # response - файл в base64, полученный от этого хука
    }
)
```

**В бизнес-процессе**

![](https://ts.it-solution.ru/media/screenshots/125/2020/03/16/2020-03-16_182042.png)

↪ Исходный код

```
def return_in_base64(bitrix_user_token, params, hook, its_params, *args, **kwargs):
    # == ПРИМЕР ИСПОЛЬЗОВАНИЯ ==
    # bitrix_user_token.call_api_method(
    #     api_method='user.update',
    #     params={
    #         'ID': 1,
    #         'PERSONAL_PHOTO': ['mark_face.jpg', response]  # response - файл в base64, полученный от этого хука
    #     }
    # )

    url = params.get('url')
    response = requests.get(url)
    content = base64.b64encode(response.content).decode('utf-8')

    with_file_name = params.get('with_file_name', False)
    if with_file_name:
        file_name = ''
        if 'content-disposition' in response.headers:
            disposition_string = response.headers['content-disposition']
            file_name_start = disposition_string.find('filename="') + 10
            file_name_end = disposition_string.find('"', file_name_start)
            if file_name_start and file_name_end:
                file_name = disposition_string[file_name_start: file_name_end]
        if not file_name:
            file_name = url[url.rfind('/') + 1:]

        file_name = unquote(file_name)
        return {'file_name': file_name, 'content': content}

    return content
```

## ⇒ refresh_email

Обновляет почтовый ящик на портале для автоматической генерации элементов CRM из почтового ящика даже когда пользователь не активен.

**Шаблон хука**

https://snapi.it-solution.ru/api/refresh_email/?auth=String&bitrix_domain=String&login=String&mailbox_id=Integer&password=String&dir_optional=String

**Параметры**

|  |  |  |  |  |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| Параметр | Тип | | Обязательный | null? | Значение по умолчанию | Описание |
| auth | String | Строка | Да | Нет |  | Авторизационный токен для запроса к REST-API Битрикс24 для вебхука.  Пример формата если id пользователя 1:  ``` 1/4EStwwVYilbktoyg ``` |
| bitrix_domain | String | Строка | Да | Нет |  | Домен портала, например portal.bitrix24.ru |
| login | String | Строка | Да | Нет |  | Логин пользователя Битрикс24 для авторизации на портале |
| mailbox_id | Integer | Число | Да | Нет |  | Идентификатор почтового ящика |
| password | String | Строка | Да | Нет |  | Пароль пользователя Битрикс24 для авторизации на портале |
| dir_optional | String | Строка | Нет | Нет |  | Директорий |

↪ Исходный код

```
def refresh_email(bitrix_user_token, params, hook, its_params, *args, **kwargs):
    login = params.get('login')
    password = params.get('password')
    mailbox_id = params.get('mailbox_id')

    dir_optional = params.get('dir_optional', '')

    session = requests.Session()

    url = 'https://' + bitrix_user_token.domain + '/configs/'
    a = session.get(url, auth=(login, password))

    session_id = re.search(r'name="sessid" id="sessid" value="(\S+)"', a.text).group(1)
    if dir_optional:
        data = {"id": mailbox_id, "dir": dir_optional, "onlySyncCurrent": False, "sessid": session_id}
    else:
        data = {"id": mailbox_id, "dir": "INBOX", "onlySyncCurrent": False, "SITE_ID": "s1", "sessid": session_id}

    a = session.post(
        'https://' + bitrix_user_token.domain + '/bitrix/services/main/ajax.php?mode=ajax&c=bitrix%3Amail.client&action=syncMailbox',
        auth=(login, password),
        data=data
    )

    return a.json()
```

## ⇒ push_bp_files_to_github

**[BP]Контроль версий для бизнес процессов**  
Записывает изменения произошедшие с бизнес процессами. Для успешного выполнения требуется создать новый репозиторий на github и добавить в Collaborators пользователя bizproc.

**Шаблон хука**

https://snapi.it-solution.ru/api/push_bp_files_to_github/auth=String&bitrix_domain=String&git_link=String&bp_login=String&bp_password=String&max_count=Integer

**Параметры**

|  |  |  |  |  |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| Параметр | Тип | | Обязательный | null? | Значение по умолчанию | Описание |
| auth | String | Строка | Да | Нет |  | Авторизационный токен для запроса к REST-API Битрикс24 для вебхука.  Пример формата если id пользователя 1:  ``` 1/4EStwwVYilbktoyg ``` |
| bitrix_domain | String | Строка | Да | Нет |  | Домен портала, например portal.bitrix24.ru |
| git_link | String | Строка | Да | Нет |  | HTTPS ссылка на репозиторий в гите, берется у системного пользователя при приглашении |
| bp_login | String | Строка | Нет | Нет |  | Логин пользователя Битрикс24 для авторизации на портале |
| bp_password | String | Строка | Нет | Нет |  | Пароль пользователя Битрикс24 для авторизации на портале |
| max_count | Integer | Число | Нет | Нет |  | Максимальный счетчик id бизнес процессов. При экспорте поиск осуществляется перебором |

↪ Исходный код

```
def push_bp_files_to_github(bitrix_user_token, params, hook, its_params, *args, **kwargs):
    domain = bitrix_user_token.domain
    git_link = params.get('git_link', '')
    git_link = urlparse(git_link)
    git_link = git_link._replace(netloc='{}@github.com'.format(GIT_TOKEN))
    git_link = urlunparse(git_link)

    login = params.get('bp_login')
    password = params.get('bp_password')
    max_count = params.get('max_count', 100)

    if login and password:
        bp_list = export_bp(login, password, portal=domain, max_count=max_count)
    else:
        bx_response = bitrix_user_token.call_api_method(
            api_method='bizproc.workflow.template.list',
            params={
                'select': [
                    'ID',
                    'MODULE_ID',
                    'ENTITY',
                    'DOCUMENT_TYPE',
                    'AUTO_EXECUTE',
                    'NAME',
                    'TEMPLATE',
                    'PARAMETERS',
                    'VARIABLES',
                    'CONSTANTS',
                    'MODIFIED',
                    'IS_MODIFIED',
                    'USER_ID',
                    'SYSTEM_CODE',
                ],
            }
        )
        bp_list = bx_response.get('result')

    # создаем временную папку
    with tempfile.TemporaryDirectory(suffix=None, prefix=None, dir=None) as version_control_folder:

        return_code, output = sys_call('{common_command} && '
                                       'git clone {git_link} {file_folder}'
                                       .format(common_command=COMMON_COMMAND_TEMPLATE.format(version_control_folder),
                                               git_link=git_link,
                                               file_folder=domain))
        if return_code != 0:
            its_logger.warn('push_bp_files_to_github', 'git clone: {}'.format(output))

        git_repository_folder = os.path.join(version_control_folder, domain)

        folders = os.listdir(git_repository_folder)

        # удаляются все файлы в папке репозитория, чтобы зафиксировать удаление бизнес-процесса
        for folder in folders:
            if folder != '.git':
                shutil.rmtree(os.path.join(git_repository_folder, folder))

        for bp in bp_list:
            if 'DOCUMENT_TYPE' in bp:
                first_level_catalog = _create_level(git_repository_folder, bp['DOCUMENT_TYPE'][0])
            else:
                first_level_catalog = _create_level(git_repository_folder, 'export')
            if 'DOCUMENT_TYPE' in bp:
                second_level_catalog = _create_level(first_level_catalog, bp['DOCUMENT_TYPE'][2])
            else:
                second_level_catalog = _create_level(first_level_catalog, 'block')
            bp_catalog = os.path.join(second_level_catalog, bp['ID'])
            os.makedirs(bp_catalog)

            name = _create_file_name(bp['NAME'], bp['ID'], prefix='BP-')
            its_logger.debug('push_bp_files_to_github', 'name: {}'.format(name))
            with open(os.path.join(bp_catalog, name), 'w', encoding='utf-8'):
                pass

            with open(os.path.join(bp_catalog, 'TEMPLATE'), 'w', encoding='utf-8') as bp_file:
                json.dump(bp['TEMPLATE'], bp_file, indent=4, ensure_ascii=False, sort_keys=True)

            with open(os.path.join(bp_catalog, 'VARIABLES'), 'w', encoding='utf-8') as bp_file:
                json.dump(bp['VARIABLES'], bp_file, indent=4, ensure_ascii=False, sort_keys=True)

            with open(os.path.join(bp_catalog, 'PARAMETERS'), 'w', encoding='utf-8') as bp_file:
                json.dump(bp['PARAMETERS'], bp_file, indent=4, ensure_ascii=False, sort_keys=True)

            with open(os.path.join(bp_catalog, 'CONSTANTS'), 'w', encoding='utf-8') as bp_file:
                json.dump(bp['CONSTANTS'], bp_file, indent=4, ensure_ascii=False, sort_keys=True)

            with open(os.path.join(bp_catalog, 'DESCRIPTION'), 'w', encoding='utf-8') as bp_file:
                bp_description = {
                    'ID': bp['ID'],
                    'MODULE_ID': bp['MODULE_ID'] if 'MODULE_ID' in bp else '',
                    'ENTITY': bp['ENTITY'] if 'ENTITY' in bp else '',
                    'DOCUMENT_TYPE': bp['DOCUMENT_TYPE'] if 'DOCUMENT_TYPE' in bp else '',
                    'AUTO_EXECUTE': bp['AUTO_EXECUTE'] if 'AUTO_EXECUTE' in bp else '',
                    'NAME': bp['NAME'],
                    'MODIFIED': bp['MODIFIED'] if 'MODIFIED' in bp else '',
                    'IS_MODIFIED': bp['IS_MODIFIED'] if 'IS_MODIFIED' in bp else '',
                    'USER_ID': bp['USER_ID'] if 'USER_ID' in bp else '',
                    'SYSTEM_CODE': bp['SYSTEM_CODE'] if 'SYSTEM_CODE' in bp else '',
                }
                json.dump(bp_description, bp_file, indent=4, ensure_ascii=False, sort_keys=True)

            if 'DOCUMENT_FIELD' in bp:
                with open(os.path.join(bp_catalog, 'DOCUMENT_FIELD'), 'w', encoding='utf-8') as bp_file:
                    json.dump(bp['DOCUMENT_FIELD'], bp_file, indent=4, ensure_ascii=False, sort_keys=True)

        return_code, output = sys_call('{common_command} && '
                                       'git add --all'
                                       .format(common_command=COMMON_COMMAND_TEMPLATE.format(git_repository_folder)))
        if return_code != 0:
            its_logger.warn('push_bp_files_to_github', 'git add: {}'.format(output))

        return_code, output = sys_call('{common_command} && '
                                       'git config user.name "{name}" && '
                                       'git config user.email "{email}" &&'
                                       'git commit -m "changes {time}"'
                                       .format(common_command=COMMON_COMMAND_TEMPLATE.format(git_repository_folder),
                                               name=GIT_NAME,
                                               email=GIT_EMAIL,
                                               time=timezone.now()))
        if return_code != 0:
            if "working tree clean" not in output.decode('utf-8'):
                its_logger.warn('push_bp_files_to_github', 'git commit: {}'.format(output))

        return_code, output = sys_call('{common_command} && '
                                       'git push origin master'
                                       .format(common_command=COMMON_COMMAND_TEMPLATE.format(git_repository_folder)))
        if return_code != 0:
            its_logger.warn('push_bp_files_to_github', 'git push: {}'.format(output))

        return dict(
            return_code=return_code,
            output=output.decode('utf-8'),
            response=bp_list,
        )
```

## ⇒ ping_pong_json_string

**Вернуть строку обратно**

Метод предназначен для парсинга и обработки JSON-строк. Он принимает на вход JSON-данные, извлекает из них значения по заданным ключам и возвращает структурированную информацию для дальнейшего использования в бизнес-процессах.

**Основная функция:** преобразование структурированных JSON-данных в отдельные переменные или удобный формат для текстовой обработки.

**Шаблон хука**

https://snapi.it-solution.ru/api/ping_pong_json_string/?auth=String&bitrix_domain=String&json_string=Raw

**Параметры**

|  |  |  |  |  |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| Параметр | Тип | | Обязательный | null? | Значение по умолчанию | Описание |
| auth | String | Строка | Да | Нет |  | Авторизационный токен для запроса к REST-API Битрикс24 для вебхука.  Пример формата если id пользователя 1:  ``` 1/4EStwwVYilbktoyg ``` |
| bitrix_domain | String | Строка | Да | Нет |  | Домен портала, например portal.bitrix24.ru |
| json_string | Raw | Json строка или объект | Да | Нет |  | Json строка или объект |

**Пример использования**

↪ Исходный код

```
def ping_pong_json_string(bitrix_user_token, params, hook, its_params, *args, **kwargs):

    if type(params.get('json_string', '')) == str:
        response = json.loads(params.get('json_string', ''))
    else:
        response = params.get('json_string', '')

    return response
```

## ⇒ crm.fix_phone_number

**[CRM] Исправить телефон**

Шаблон хука

https://snapi.it-solution.ru/api/crm.fix_phone_number/?auth=String&bitrix_domain=String&activity_id=Integer&check_mobile=Integer&company_id=Integer&contact_id=Integer&convert_rules=String&lead_id=Integer&notify=String

**Параметры**

|  |  |  |  |  |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| Параметр | Тип | | Обязательный | null? | Значение по умолчанию | Описание |
| auth | String | Строка | Да | Нет |  | Авторизационный токен для запроса к REST-API Битрикс24 для вебхука.  Пример формата если id пользователя 1:  ``` 1/4EStwwVYilbktoyg ``` |
| bitrix_domain | String | Строка | Да | Нет |  | Домен портала, например portal.bitrix24.ru |
| activity_id | Integer | Число | Нет | Нет |  | Идентификатор дела |
| check_mobile | Integer | Число | Нет | Нет |  | Если равно "1", проверяем телефон и при необходимости меняем тип на "MOBILE" |
| company_id | Integer | Число | Нет | Нет |  | Идентификатор компании |
| contact_id | Integer | Число | Нет | Нет |  | Идентификатор контакта |
| convert_rules | String | Строка | Нет | Нет |  | По умолчанию: 10->10->+7, 11->10->+7  (Если столько цифр в телефоне->обрезаем до->ставим префикс) |
| lead_id | Integer | Число | Нет | Нет |  | Идентификатор лида |
| notify | String | Строка | Нет | Нет |  | Идентификатор пользователя, или "responsible". Если не передан параметр, то никого не будет о смене уведомлять |

↪ Исходный код

```
def fix_phone_number(bitrix_user_token, params, hook, its_params, log, *args, **kwargs):
    """
    https://ts.it-solution.ru/#/ticket/59519/
    """

    bx_type, bx_id, bx_owner_type_id = get_crm_target_from_params(params, its_params)

    check_mobile = params.get('check_mobile', 0)

    try:
        bx_object = bitrix_user_token.call_api_method('crm.{}.get'.format(bx_type),
                                                         {'id': bx_id},
                                                         timeout=3)['result']
    except requests.Timeout as e:
        return []
    except BitrixApiError as e:
        if e.status_code == 400 or e.status_code == 401:
            return []
        else:
            raise e
    except ConnectionToBitrixError as e:
        its_logger.warn('fix_phone_number ConnectionToBitrixError', '{} {}'.format(str(bitrix_user_token), str(e)))
        return []

    if params.get('notify') == 'responsible':
        notify = bx_object.get('ASSIGNED_BY_ID', None)
    else:
        notify = params.get('notify', None)

    fixed_phones = []
    initial_phones = bx_object.get('PHONE')
    if initial_phones:
        for phone in initial_phones:
            initial_num = phone['VALUE']
            fixed_num = get_international_number(initial_num, params.get('convert_rules'))
            phone_updated = False
            if initial_num != fixed_num:
                phone['VALUE'] = fixed_num
                phone_updated = True

                log.append('{} -> {}'.format(initial_num, fixed_num))
            if check_mobile == 1 and fixed_num[2] == '9' and phone['VALUE_TYPE'] != "MOBILE":
                log.append('{}: {} -> MOBILE'.format(fixed_num, phone['VALUE_TYPE']))

                phone['VALUE_TYPE'] = "MOBILE"
                phone_updated = True

            if phone_updated:
                fixed_phones.append(phone)

    if fixed_phones:
        bitrix_user_token.call_api_method('crm.{}.update'.format(bx_type), {
            'id': bx_id,
            'fields': {
                'PHONE': fixed_phones
            }
        })

        if notify:
            bitrix_user_token.call_api_method('im.notify', {
                'to': notify,
                'type': 'SYSTEM',
                'message': 'Исправлен номер телефона {type} [URL={url}]{title}[/URL]\n{log}'.format(
                    url=get_bx_object_url(domain=bitrix_user_token.domain, bx_type=bx_type, bx_id=bx_id),
                    title=get_bx_object_title(bx_object),
                    type='компании' if bx_type == 'company' else 'контакта' if bx_type == 'contact' else 'лида',
                    log='\n'.join(log)
                )
            })

    return fixed_phones
```

## ⇒ crm.filter_and_start_bp

**[CRM] Запустить БП по выборке**

Шаблон хука

https://snapi.it-solution.ru/api/crm.filter_and_start_bp/?auth=String&bitrix_domain=String&bp_id=Integer&method=String&bp_document_id=Dict&limit=Integer&params=Dict&timeout=Integer

**Параметры**

|  |  |  |  |  |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| Параметр | Тип | | Обязательный | null? | Значение по умолчанию | Описание |
| auth | String | Строка | Да | Нет |  | Авторизационный токен для запроса к REST-API Битрикс24 для вебхука.  Пример формата если id пользователя 1:  ``` 1/4EStwwVYilbktoyg ``` |
| bitrix_domain | String | Строка | Да | Нет |  | Домен портала, например portal.bitrix24.ru |
| bp_id | Integer | Число | Да | Нет |  | ID Бизнес Процесса для запуска |
| method | String | Строка | Да | Нет |  | REST-метод, например crm.lead.list |
| bp_document_id | Dict | Словарь | Нет | Да |  | Первые два параметра для массива "DOCUMENT_ID" в методе bizproc.workflow.start.  Пример: ["crm", "CCrmDocumentLead"] или ?bp_document_id[0]=crm&bp_document_id[1]=CCrmDocumentLead.  ID сущностей (третий параметр массива) будут подставлены из выборки. |
| limit | Integer | Число | Нет | Нет | 0 | Лимит,0 - без ограничения, т.к. записи получаются от Битрикс24 по 50 штук, то значение может быть округлено вверх до 50. Например, если вы запрашиваете 80 записей, а на портале их более 100, то вы получите 100 записей |
| params | Dict | Словарь | Нет | Да |  | Параметры запроса, либо как JSON либо в виде ?params[FILTER][ID]=5 |
| timeout | Integer | Число | Нет | Нет | 60 | Таймаут в секундах |

↪ Исходный код

```
def crm_filter_and_start_bp(

        bitrix_user_token: BitrixUserToken,

        params: CallListParams,

        *args, **kwargs

):
    """Выполняет запрос к REST-методу на вашем портале,
    может получать более 50 записей за 1 запрос.

    Через POST:
    fetch('https://snapi.it-solution.ru/api/rest.call_list/', {
        body: JSON.stringify({
            bitrix_domain: 'bytryks.bitrix24.by',
            auth: '1/xxxxyyyyzzzzqqqq',
            method: 'crm.deal.list',
            params: {
                FILTER: { ID: 4 },
            },
        }),
        method: 'POST',
    });

    Аналогичный запрос через GET:
    https://snapi.it-solution.ru/api/rest.call_list/?bitrix_domain=bytryks.bitrix24.by&amp;amp;auth=1/xxxxyyyyzzzzqqqq&method=crm.deal.list&params[FILTER][ID]=4
    """
    try:
        res = bitrix_user_token.call_list_method_v2(
            method=params.method,
            fields=params.params,
            force_total=params.limit,
            timeout=params.timeout
        )
    except BitrixApiError as e:
        return dict(response=e.dict(), success=False)
    bp_id = params.bp_id
    bp_document_id = params.bp_document_id
    methods = []
    for entity in res:
        methods.append(
            (
                entity['ID'],
                'bizproc.workflow.start',
                {
                    'TEMPLATE_ID': bp_id,
                    'DOCUMENT_ID': bp_document_id + [entity['ID']]
                }
            )
        )
    try:
        res = bitrix_user_token.batch_api_call_v3(methods, timeout=params.timeout)
    except BitrixApiError as e:
        return dict(response=e.dict(), success=False)
    return dict(response=res, success=True)
```

### 

## ⇒ crm.change_status_and_revert

**[CRM] Перевести CRM сущность на стадию и обратно**  

**ВНИМАНИЕ!**

Для параметра 'auth' этому вебхуку потребуется "подставной" пользователь.

На портале Битрикс24 создайте или найдите подходящего пользователя, который никогда не будет работать с CRM (он "по жизни" не должен никогда редактировать сущности CRM). Создайте от него входящий вебхук и укажите в параметре 'auth' данные для вебхука этого пользователя. Этот пользователь должен иметь доступ ко всем сущностям CRM.

Шаблон хука

https://snapi.it-solution.ru/api/crm.change_status_and_revert/?auth=String&bitrix_domain=String&status_id=String

**Параметры**

|  |  |  |  |  |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| Параметр | Тип | | Обязательный | null? | Значение по умолчанию | Описание |
| auth | String | Строка | Да | Нет |  | Авторизационный токен для запроса к REST-API Битрикс24 для вебхука.  Пример формата если id пользователя 1:  ``` 1/4EStwwVYilbktoyg ``` |
| bitrix_domain | String | Строка | Да | Нет |  | Домен портала, например portal.bitrix24.ru |
| status_id | String | Строка | Да | Нет |  | ID временной стадии/статуса |

↪ Исходный код

```
def change_status_and_revert(bitrix_user_token, params, hook, its_params, *args, **kwargs):
    entity_type, entity_id, entity_owner_type_id = get_crm_target_from_params(params, its_params)

    temporary_status = params.get('status_id')

    if entity_type == 'lead' or entity_type == 'deal':
        response = bitrix_user_token.call_api_method(
            'profile',
            {}
        )
        receiving_webhook_user_id = response['result']['ID']

        response = bitrix_user_token.call_api_method(
            'crm.' + entity_type + '.get',
            {
                'id': entity_id
            }
        )
        modify_by_id = response['result']['MODIFY_BY_ID']

        if modify_by_id != receiving_webhook_user_id:
            status_field_name = ''
            if entity_type == 'lead':
                status_field_name = 'STATUS_ID'
            if entity_type == 'deal':
                status_field_name = 'STAGE_ID'

            current_status = response['result'][status_field_name]

            bitrix_user_token.call_api_method(
                'crm.' + entity_type + '.update',
                {
                    'id': entity_id,
                    'fields': {
                        status_field_name: temporary_status
                    }
                }
            )
            bitrix_user_token.call_api_method(
                'crm.' + entity_type + '.update',
                {
                    'id': entity_id,
                    'fields': {
                        status_field_name: current_status
                    }
                }
            )

        return {
            'success': True
        }
    else:
        return {
            'error': 'Работает только для лидов и сделок'
        }
```

## ⇒ buisness_process.start_bp_on_user_add

**[start_bp_on_user_add] Запустить бизнес-процесс по добавлению пользователя**

**Шаблон хука**

https://snapi.it-solution.ru/api/crm.change_status_and_revert/?auth=String&bitrix_domain=String&status_id=String

**Параметры**

|  |  |  |  |  |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| Параметр | Тип | | Обязательный | null? | Значение по умолчанию | Описание |
| auth | String | Строка | Да | Нет |  | Авторизационный токен для запроса к REST-API Битрикс24 для вебхука.  Пример формата если id пользователя 1:  ``` 1/4EStwwVYilbktoyg ``` |
| bitrix_domain | String | Строка | Да | Нет |  | Домен портала, например portal.bitrix24.ru |
| bp_template_id | Integer | Число | Да | Нет |  | ID шаблона бизнес-процесса, который надо запустить (например, 740 если portal.ru/bizproc/processes/390/bp_edit/740/) |
| iblock_id | Integer | Число | Да | Нет |  | ID инфоблока (например, 390, если portal.ru/bizproc/processes/390/bp_edit/740/) |
| bp_parameters | Dict | Словарь | Нет | Да | [] | Параметры для бизнес-процесса |
| only_intranet | Boolean | Булев | Нет | Нет | false | НЕ АКТУАЛЬНО - Функционал Экстранет пользователей устарел в Битрикс24  Запускать ли бизнес-процессы по экстранетчикам. True или False. По умолчанию - False |

#### 

↪ Исходный код

```
def start_bp_on_user_add(bitrix_user_token, params, hook, its_params, log, *args, **kwargs):

    bp_template_id = params['bp_template_id']
    iblock_id = params['iblock_id']

    only_intranet = its_params.get('only_intranet', False)
    user_id = its_params.get('data[ID]', '')

    if only_intranet and 'data[UF_DEPARTMENT][0]' not in its_params:
        return 'Business-process was not started because it is extranet user and parameter "only_intranet" is True'

    if not user_id:
        return 'User id was not found'

    try:
        bp_params = {
            'new_user': 'user_{}'.format(user_id), # на стороне бп принимать в качестве строки
            'new_user_id': user_id,
        }
        for item in its_params:
            if 'bp_parameters' not in item:
                continue
            r = re.findall(r"[\w']+", item)

            if not r or len(r) != 2:
                continue
            key = 'bp_parameters[{}]'.format(r[1])
            bp_params.update({r[1]: its_params[key]})

        # получаем по ID шаблона ENTITY и MODULE_ID
        result_template = bitrix_user_token.call_api_method(
            api_method='bizproc.workflow.template.list',
            params={
                'SELECT': ['MODULE_ID', 'ENTITY'],
                'FILTER': {
                    'ID': bp_template_id
                }
            }
        )

        if not result_template['total']:
            return 'Workflow template by bp_template_id not found'

        module = result_template['result'][0]['MODULE_ID']
        entity = result_template['result'][0]['ENTITY']

        # получаем по ID инфоблока IBLOCK_TYPE
        result_iblock_type = bitrix_user_token.call_api_method(
            api_method='lists.get.iblock.type.id',
            params={
                'IBLOCK_ID': iblock_id
            }
        )

        if not result_iblock_type['result']:
            return 'Iblock_type by iblock_id not found'

        iblock_type = result_iblock_type['result']

        # создаем элемент, по которому запустим бп
        result_add_element = bitrix_user_token.call_api_method(
            api_method='lists.element.add',
            params={
                'IBLOCK_ID': iblock_id,
                'IBLOCK_TYPE_ID': iblock_type,
                'ELEMENT_CODE':  uuid.uuid4().hex,
                'FIELDS': {
                    'NAME': 'Пользователь {}'.format(user_id),

                }
            }
        )

        if not result_add_element['result']:
            return 'New list\'s element was not add'

        bitrix_user_token.call_api_method(
            api_method='bizproc.workflow.start',
            params={
                'TEMPLATE_ID': bp_template_id,
                'DOCUMENT_ID': [module, entity, result_add_element['result']],
                'PARAMETERS': bp_params
            }
        )

    except Exception as e:
        return e.__str__
```