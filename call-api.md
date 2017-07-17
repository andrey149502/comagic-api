# Call API

## Знакомство

### Введение

Настоящий документ описывает Call API, которое позволяет совершать звонки и управлять ими.

Управлять можно так же звонками, которые поступили на виртуальную АТС. Для этого необходимо получить уникальный идентификатор сессии звонка. Его можно получить с помощью [сервера уведомлений](http://help.comagic.ru/topics/86-nastrojka-uvedomlenij/), [REST API](https://www.comagic.ru/support/article/137/#poluchenie-informacii-o-zvonkah).

API реализован на основе спецификации [JSON-RPC 2.0](http://www.jsonrpc.org/specification) без поддержки групповых операций.

При запросах к API в качестве транспортного протокола используется HTTP/1.1 с защитой соединения с помощью SSL/TLS, при этом соблюдая следующие требования:

*   Заголовок `Content-Type` должен быть `application/json; charset=UTF-8`
*   Заголовок `Content-Length` должен содержать корректную длину сообщения, следуя спецификации HTTP/1.1

### Список методов

| Название | Описание |
| Аутентификация |
| [login.user](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-login-user) | Аутентификация: Вход |
| [logout.user](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-logout-user) | Аутентификация: Выход |
| Группа методов создания звонков |
| [start.employee_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-employee-call) | Вызов на сотрудника. Создает прямой звонок абонента с сотрудником без использования сценария. |
| [start.scenario_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-scenario-call) | Вызов по сценарию. Создает звонок согласно настроенному сценарию. Для использования метода достаточно одного виртуального номера и N сценариев. |
| [](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-vnumber-call) | Вызов на виртуальный номер АТС, динамического или статического коллтрекинга |
| [start.informer_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-informer-call) | Информационный вызов с возможностью проиграть абоненту файл или текстовое сообщение. После окончания проигрывания сообщения вызов завершается автоматически. |
| [start.simple_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-simple-call) | Звонок на любые номера кроме собственных виртуальных |
| Группа методов управления звонками |
| [make.call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-make-call) | Создать звонок для трансфера или консультации |
| [transfer.talk](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-transfer-talk) | Метод позволяет произвести трансфер звонка другому сотруднику или на внешний номер (см. раздел "[Диаграмма состояний звонка](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#state-diagram-call)") |
| [restore.talk](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-restore-talk) | Метод позволяет вернуться в разговор с абонентом после получения консультации от другого сотрудника (см. раздел "[Диаграмма состояний звонка](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#state-diagram-call)") |
| [hold.call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-hold-call) | Постановка вызова на удержание |
| [unhold.call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-unhold-call) | Снятие вызова с удержания |
| [add.coach](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-add-coach) | Подключение тренера к разговору |
| [tag.call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-tag-call) | Простановка тега для активного вызова |
| [record.call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-record-call) | Управление записью разговора
Отключить запись разговора, настроенную через личный кабинет невозможно |
| [disconnect.leg](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-disconnect-leg) | Метод позволяет разъединять отдельных участников разговора. Уникальный идентификатор каждого участника разговора можно получить используя метод "[list.calls](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-list-calls)" или [сервер уведомлений](http://help.comagic.ru/topics/86-nastrojka-uvedomlenij/) |
| [send.dtmf](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-send-dtmf) | Отправка DTMF сотрудником |
| [block.contact](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-block-contact) | Занести абонента в чёрный список во время разговора |
| [list.calls](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-list-calls) | Получить список активных вызовов и их участников |
| [list.talk_options](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-list-talk-options) | Получение списка опций разговора настроенных в Личном кабинете виртуальной АТС |
| [call.talk_option](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-call-talk-option) | Вызов опции разговора, которая настроена в Личном кабинете виртуальной АТС |
| [release.call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-release-call) | Завершить вызов |

### Соглашения

Приняты следующие соглашения:

*   Пустые поля всегда возвращаются в ответе со значением `null`. В случае массива возвращается пустой массив, в случае объекта возвращается пустой объект;
*   Все поля связанные с датой и временем передаются согласно [ISO 8601](http://www.iso.org/iso/ru/home/standards/iso8601.htm) в формате `YYYY-MM-DDT hh:mm:ssZ`;
*   Запросы к API выполняются всегда с помощью метода POST;
*   Все параметры в запросах/ответах, а также в структурах данных в формате JSON и название методов именуются в стиле Snake Case - разделение слов через нижнее подчёркивание;
*   Данные возвращаются только в JSON формате согласно спецификации [RFC 7159](https://tools.ietf.org/html/rfc7159);
*   Кодировка данных UTF-8;

### Формат возвращаемых данных

| Формат | MIME Type |
| JSON | application/json |

По умолчанию используется формат JSON. Заголовок Accept игнорируется

### Базовый URL для доступа к API

Базовый URI для доступа к API:

```
https://callapi.uiscom.ru/<version>
```

или

```
https://callapi.comagic.ru/<version>
```

где

`<version>` - версия API (см. раздел [Версионность](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#versioning))

### Версионность

Call API поддерживает версионность. Версия указывается в базовом URL как vX.Y, где X - номер мажорной версии, Y - номер минорной версии

Версия должна указываться через точку, к примеру 4.0

Если была выпущена новая версия, то старая считается устаревшей и соответственно при обращении к старой версии API в мета-параметрах (см. раздел [Мета-параметры](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#meta-parameters)) будет возвращаться параметр `current_version_depricated` со значением `true`. Это говорит о том, что в ближайшие пару месяцев старая версия может стать недоступной.

Пример:

```
https://callapi.comagic.ru/v4.0 \b https://callapi.comagic.ru/v4.0
```

Максимальное количество поддерживаемых версий - 2
Период поддержки устаревшей версии 2 месяца

### Лимиты и ограничения

Лимиты построены по бальной системе, т.е каждый метод имеет свой вес в баллах.

Баллы списываются только за успешные запросы, т.е в [отчете по запросам](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#) он помечен как успешный. Успешным считается запрос, если в `result` был возвращен статус `success` = `true` или идентификатор сессии звонка

Лимиты привязаны к компоненту `Call API Базовый набор` (см. [Компоненты](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#сomponents)) и учитываются в зависимости от стоимости метода в баллах

Информация о лимитах возвращается во всех ответах в мета-парметрах (см. [Мета-параметры](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#meta-parameters)), кроме случаев когда лимиты не учитываются;

*   Если возвращются ошибки валидации JSON или структуры запроса - мнемоника "parse_error" или "invalid_request";
*   Если возвращается ошибка на вызов метода, который не существует - мнемоника "method_not_found";
*   Если возвращается ошибка связанная с аутентификацией - "access_token_blocked", "access_token_invalid", "access_token_expired", "auth_error";
*   Если возвращается ошибка при запросе с IP адреса, который не в белом списке - мнемоника "ip_not_whitelisted".

Информация о лимитах возвращается в мета-параметрах (см. [Мета-параметры](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#meta-parameters))

#### Расширение лимитов

Лимитами можно управлять в личном кабинете на странице "Аккаунт" -> "Тарифы и опции".

Название лимитов:

*   Баллов Call API в минуту;
*   Баллов Call API в день;
*   Длина TTS сообщения.

Отсутствие лимитов в личном кабинете может быть связанно с тем, что имеются ограничения в тарифном плане. В этом случае необходимо связаться с персональным менеджером или службой технической поддержки.

#### Управление безопасностью звонка

Управлять безопасностю звонков (мобильные, междугородние, международные направления) можно в личном кабинете на странице "Аккаунт" -> "Правила и настройки безопасности" -> "API".

По умолчанию запрещено международное направление, остальные направления разрешены.

Разрешения для направлений звонка разбиты по компонентам Call API (см. [Компоненты](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#сomponents))

### Мета-параметры

Возвращаются в ответ на вызов метода. Присутствуют как в ошибочном, так и в успешном ответе

Параметр `api_version` возвращается только для версий которые `deprecated`.

Описание параметров

| Название параметра | Описание |
| `day_limit` | Текущие лимит баллов в день |
| `day_remaining` | Какое количество баллов осталось до достижения дневного лимита |
| `day_reset` | Время в секундах через которое дневной лимит будет сброшен |
| `minute_limit` | Текущие лимит баллов за минуту |
| `minute_remaining` | Какое количество баллов осталось до достижения минутного лимита |
| `minute_reset` | Время в секундах через которое минутный лимит будет сброшен |
| `current_version_depricated` | Признак, говорящий, что в ближайшие пару месяцев старая версия может стать недоступной. |
| `current_version` | Вызванная версия |
| `latest_version` | Последняя доступная версия |

JSON структура

```json
"metadata": {
  "api_version": {
    "current_version_depricated": "boolean",
    "current_version": "string",
    "latest_version": "string"
  },
  "limits": {
    "day_limit": "number",
    "day_remaining": "number",
    "day_reset": "number",
    "minute_limit": "number",
    "minute_remaining": "number",
    "minute_reset": "number"
  }
}
```

### Компоненты

| Название для клиента | Список методов | Описание |
| Call API Базовый набор | [start.employee_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-employee-call), [start.vnumber_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-vnumber-call), [start.simple_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-simple-call), [login.user](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-login-user), [logout.user](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-logout-user), [release.call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-release-call) | Базовый пакет Call API |
| Call API Управление вызовами | [hold.call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-hold-call), [unhold.call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-unhold-call), [make.call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-make-call), [disconnect.leg](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-disconnect-leg), [tag.call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-tag-call), [record.call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-record-call), [add.coach](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-add-coach), [list.calls](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-list-calls), [send.dtmf](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-send-dtmf), [block.contact](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-block-contact), [restore.talk](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-restore-talk), [transfer.talk](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-transfer-talk), [list.talk_options](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-list-talk-options), [call.talk_option](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-call-talk-option) | Пакет позволяет управлять звонками. |
| Call API Информационный вызов | [start.informer_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-informer-call) | Пакет, который позволяет обзванивать клиентов и проигрывать им TTS или какой-то предустановленный файл. После окончания информационного сообщения вызов завершается |
| Call API Вызов сценария | [start.scenario_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-scenario-call) | Пакет, который позволяет совершать вызовы клиенту с использованием одного виртуального номера и набора сценариев. |

### Пользователи Call API

В личном кабинете "Аккаунт" -> "Управление пользователями" при подключённом компоненте `Call API Базовый набор` появляется возможность выбора нового набора прав доступа в виде чек-бокса `Доступ к Call API`.

> Все эти действия могут быть выполнены только администратором.

#### Доступ по логину и паролю

Используется аутентификация по логину и паролю пользователя, в ответе будет получен ключ доступа к Call API

> Время жизни сессии по умолчанию 1 час.

#### Доступ по ключу

В настройках пользователя есть возможность включить доступ по ключу, для этого необходимо в настройках пользователя поставить галку "Доступ по ключу". При этом, будьте внимательны: ключ виден в интерфейсе и доступен для копирования только в момент генерации, после сохранения настроек вы сможете его увидеть только в коде своего приложения или другом месте, куда вы сохранили этот ключ.

> Ключи могут действовать до определенной даты или быть бессрочными, в зависимости от ваших настроек.

### Статистика и отчёты

В личном кабинете "Отчеты" -> "Служебные" -> "Запросы к API" можно видеть все запросы к API, кроме запросов, которые закончились ошибками:

*   Если возвращются ошибки валидации JSON или структуры запроса - мнемоника "parse_error" или "invalid_request";
*   Если возвращается ошибка на вызов метода, который не существует - мнемоника "method_not_found";
*   Если возвращается ошибка связанная с аутентификацией - "access_token_blocked", "access_token_invalid", "access_token_expired", "auth_error";
*   Если возвращается ошибка при запросе с IP адреса, который не в белом списке - мнемоника "ip_not_whitelisted".

Полную информацию по звонку можно найти в "Отчеты" -> "Обращения" -> "Звонки" с фильтрацией по параметру "Индентификатор сессии звонка"

> Если в фильтрах отсутствует параметр "Идентификатор сессии звонка", то его нужно добавить в доступные столбцы отчета

## Общее

### Общие поля для всех методов

| Название | Тип | Обязательный | Допустимые значения | Описание |
| id | string или number | да | Уникальный идентификатор запроса к API. <blockquote>Не передается в уведомлениях. Фигурирует только в [Статистика и отчеты](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-users-statistics) параметр `Идентификатор запроса`</blockquote> |
| method | string | да | Вызываемый метод (см. [Список методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list)) |
| jsonrpc | string | да | 2.0 | Номер спецификации JSON-RPC |
| params | object | да | Содержит тело запроса к API. В зависимости от вызываемого метода тело запроса меняется. |

### Диаграмма состояний звонка

[![](./index-doc-call-api_files/state-diagram-call.jpg)](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#)

## Аутентификация

### Вход

| Метод | login.user |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| login | string | да | Логин пользователя |
| password | string | да | Пароль пользователя |

#### Параметры ответа

| Название | Тип | Обязательный | Описание |
| access_token | string | да | Ключ сессии аутентификации |
| expire_at | number | да | Timestamp когда выданный токен перестанет быть валидным |

> Время жизни полученного ключа сессии аутентификации после вызова метода `login.user` - 1 час. По истечению времени жизни ключа сессии его необходимо запрашивать заново, т.е. вызывать метод `login.user`.

> Для совершения запросов к API возможно использование постоянного ключа аутентификации, который доступен в личном кабинете (см. [Доступ по ключу](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-users-access-key)).

#### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "method": "login.user",
  "params": {
    "login": "your_login",
    "password": "your_password"
  }
}
```

#### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": {
      "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d",
      "expire_at": 1475853742
    }
  }
}
```

### Выход

| Метод | logout.user |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| access_token | string | да | Ключ сессии аутентификации |

#### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "method": "logout.user",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d"
  }
}
```

#### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": {
      "success": true
    }
  }
}
```

## Группа методов создания звонков

### Вызов на сотрудника

| Метод | start.employee_call |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

#### Параметры запроса

| Название | Тип | Обязательный | Допустимые значения | Описание |
| access_token | string | да | Ключ сессии аутентификации |
| first_call | string | да | contact, employee | 

Определяет номер, на который нужно дозвониться в первую очередь:

*   `employee` - сотрудник;
*   `contact` - вызываемая сторона (обычно клиент);

 |
| switch_at_once | boolean | нет | true, false | 

Значение по умолчанию `false`.

Если параметр `first_call` имеет значение `employee`, заданы параметры `employee_message` и `contact_message`, то:

*   Первый звонок совершается сотруднику из параметра `employee`;
*   Сотрудник после поднятия трубки слушает сообщение до конца и только потом совершается звонок на абонента заданного в параметре `contact`;
*   Во время дозвона до `contact`, `employee` слушает сообщение заданное в параметре `media_file_id`;
*   После ответа абонента из параметра `contact`, произойдет разговор с сотрудником из параметра `employee`, если `switch_at_once` имеет значение `true`;
*   После ответа абонента из параметра `contact`, он дослушает сообщение до конца, если `switch_at_once` имеет значение `false` и далее произойдет разговор с сотрудником из параметра `employee`.

Если параметр `first_call` имеет значение `contact`, задан параметр `contact_message` и задан параметр `employee_message`, то:

*   В первую очередь звонок совершается абоненту из параметр `contact`);
*   Абонент из параметра `contact` после поднятия трубки начинает слушать сообщение и в это же время совершается звонок на сотрудника из параметра `employee`;
*   После поднятия трубки сотрудником из параметра `employee` проигрывание сообщения будет прервано для абонента из параметра `contact` и далее состоится разговор, если параметр `switch_at_once` имеет значение `true`;
*   После поднятия трубки сотрудником из параметра `employee` сообщение будет проиграно до конца обоим и далее состоится разговор, если параметр `switch_at_once` имеет значение `false`;
*   Если у кого-то проигрывание сообщения закончилось раньше, то он слушает сообщение, заданное в параметре `media_file_id`.

> Если в параметре `contact_message type` имеет значение `tts`, то параметр `switch_at_once=true` не работает

 |
| early_switching | boolean | нет | true, false | 

Значение по умолчанию `false`.

Если параметр имеет значение `true`, то сотрудник при дозвоне до абонента будет слышать то, что происходит в абонентской линии.

К примеру, оператор ждет дозвона до абонента, а абонент недоступен и у него сработала голосовая почта, то при активации параметра `early_switching` = `true`, оператор сможет услышать сообщение о голосовой почте абонента. Если параметр `early_switching` = `false`, то оператор будет слушать музыку, которая задана в параметре "media_file_id"

> Параметр может иметь значение `true` если только параметр `first_call` имеет значение `employee` и параметр `switch_at_once` имеет значение значение `true`. В противном случае будет ошибка "-32602 invalid_parameters_combination The combination of parameters is not permitted" (см. раздел [коды ошибок](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-employee-call-errors)).

 |
| media_file_id | number | нет | 

Значение по умолчанию - системная мелодия "Музыка переадресации" (`dialing_music`).

Задаёт идентификатор звукового файла для музыки переадресации. Файл может быть как системным, так и пользовательским. Получить список системных или пользовательских файлов можно с помощью REST API - [Получение списка пользовательских файлов](https://www.comagic.ru/support/article/137/#media_files), [Получение списка системных файлов](https://www.comagic.ru/support/article/137/#system_media_files).

> Проигрывается всегда тому плечу, для которого не задан один из параметров `contact_message` или `employee_message`

 |
| virtual_phone_number | string | да | 

Виртуальный номер, арендуемый клиентом. Формат номера должен соответствовать международному стандарту E.164 (например, `74993720692`). Всегда используется в качестве номера звонящего при звонке на номер, указанный в параметре `contact`. Используется в качестве номера звонящего при звонке на номер, указанный в параметре `employee`, если параметр `show_virtual_phone_number` имеет значение `true`. Виртуальные номера можно получить с помощью REST API метода - [Получение списка виртуальных номеров](https://www.comagic.ru/support/article/137/#poluchenie_spiska_virtualnih_nomerov)

> В качестве виртуального номера запрещено использовать 800-е номера

 |
| show_virtual_phone_number | boolean | нет | true, false | 

Значение по умолчанию `true`.

Показывать ли виртуальный номер, заданный параметром `virtual_phone_number` в качестве номера звонящего абонента для сотрудника заданного в параметре `employee`.

 |
| contact | string | да | 

Номер абонента на который совершается вызов. Формат номера должен соответствовать международному стандарту E.164 (например, `79091234567`). В качестве номера может быть так же указан SIP номер сотрудника.

> Внутренние номера сотрудников не поддерживаются.

 |
| external_id | string | нет | Уникальный идентификатор, который может быть использован для связи события звонка с внешней системой. |
| dtmf_string | string | нет | 0-9, *, # | Задаёт DTMF, который будет отправлен абоненту, заданному в параметре `contact`. С помощью символа `.` = '1 секунда' можно задать таймаут по истечению которого символ DTMF будет отправлен. Пример: `.12.1..4`, т.е через 1 секунду будет отправлена цифра 12, далее через 1 секунду будет отправлена цифра 1 и через 2 секунды цифра 4. |
| direction | string | нет | in, out | Значение по умолчанию `out`.
Определяет направление звонка `in` - Входящий звонок, `out` - Исходящий звонок. |
| Сотрудник с которым будет соединён абонент из параметра `contact` |
| employee | object | да | Сотрудник с которым будет соединён абонент, указанный в параметре `contact`. |
 id | number | да | Уникальный идентификатор сотрудника. Данный идентификатор можно получить с помощью REST API - [Получение информации о сотруднике](https://www.comagic.ru/support/article/137/#employee)

> Если не указан параметр `phone_number`, то будет совершен последовательный обзвон всех активных номеров сотрудника.

 |
 phone_number | string | нет | 

Задаёт номер сотрудника с которым будет соединен абонент указанный в параметре `contact`. В качестве номера может быть указан SIP-номер, внутренний номер, номер в формате E.164\. Номер должен иметь статус `Активен` в личном кабинете и принадлежать сотруднику заданному в параметре `id`.

 |
| Сообщение для проигрывания абоненту, который задан в параметре `contact` |
| contact_message | object | нет | 

Определяет параметры сообщения, которое необходимо проиграть абоненту заданному в параметре `contact`.

> После окончания проигрывания сообщения, будет проигрываться покругу сообщение из параметра `media_file_id`

 |
 type | string | да | media, tts | 

Определяет тип сообщения. `media` - файл или `tts` - текст для услуги синтеза речи Text-to-Speech.

 |
 value | string | да | 

Если поле `type` имеет значение `media`, то в качестве значения принимается идентификатор файла для проигрывания. Файл для проигрывания может быть системным или пользовательским. Идентификатор файла для проигрывания можно получить с помощью REST API - [Получение списка пользовательских файлов](https://www.comagic.ru/support/article/137/#media_files), [Получение списка системных файлов](https://www.comagic.ru/support/article/137/#system_media_files)

Если поле `type` имеет значение `tts`, то в качестве значения принимается текст для синтезирования его в голосовое сообщение.

> Длина TTS сообщения регулируется тарифным планом и установленным лимитом.

 |
| Сообщение для проигрывания абоненту, который задан в параметре `employee` |
| employee_message | object | нет | 

Определяет параметры сообщения, которое необходимо проиграть абоненту заданному в параметре `employee`.

> После окончания проигрывания сообщения, будет проигрываться покругу сообщение из параметра `media_file_id`

 |
 type | string | да | media, tts | 

Определяет тип сообщения. `media` - файл или `tts` - текст для услуги синтеза речи Text-to-Speech.

 |
 value | string | да | 

Если поле `type` имеет значение `media`, то в качестве значения принимается идентификатор файла для проигрывания. Файл для проигрывания может быть системным или пользовательским. Идентификатор файла для проигрывания можно получить с помощью REST API - [Получение списка пользовательских файлов](https://www.comagic.ru/support/article/137/#media_files), [Получение списка системных файлов](https://www.comagic.ru/support/article/137/#system_media_files)

Если поле `type` имеет значение `tts`, то в качестве значения принимается текст для синтезирования его в голосовое сообщение.

> Длина TTS сообщения регулируется тарифным планом и установленным лимитом.

 |

#### Параметры ответа

| Название | Тип | Обязательный | Описание |
| call_session_id | number | да | Уникальный идентификатор сессии звонка |

#### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "method": "start.employee_call",
  "id": "req1",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d",
    "first_call": "employee",
    "switch_at_once": true,
    "media_file_id": 2701,
    "show_virtual_phone_number": false,
    "virtual_phone_number": "74993720692",
    "external_id": "334otr01",
    "dtmf_string": ".1.2.3",
    "direction": "in",
    "contact": "79260000000",
    "employee": {
      "id": 25,
      "phone_number": "79260000001"
    },
    "contact_message": {
      "type": "tts",
      "value": "Привет"
    },
    "employee_message": {
      "type": "media",
      "value": "2561"
    }
  }
}
```

#### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": {
      "call_session_id": 237859081
    }
  }
}
```

#### Список возвращаемых ошибок

| Текст ошибки | Код ошибки | Мнемоника | Описание |
| The maximum length of Text-to-Speech message is {`tts_message_max_length`}. The length of your message is {`sent_tts_message_length`} | -32602 | `tts_text_exceeded` | Длина сообщения превысила допустимое ограничение, установленное тарифным планом |
| The media file with id {`media_file_id`} not found | -32602 | `media_file_not_found` |
| Virtual phone number {`virtual_phone_number`} not found. It is not your virtual phone number. | -32007 | `virtual_phone_number_not_found` | Если используется виртуальный номер не принадлежащий клиенту или 800-й номер |
| Employee with id {`employee_id`} not found. It is not your employee. | -32602 | `employee_not_found` |
| The phone number does not exist or inactive | -32602 | `no_active_phone_number` | У сотрудника нет активных номеров в настройках сотрудника |
| Parameter `contact` can not contain own virtual phone number | -32602 | `own_virtual_phone_number_not_allowed` | Звонок на собственный виртуальный номер запрещён |
| The contact {`contact`} has been found in the blacklist | -32002 | `contact_in_blacklist` |
| The character encoding must be UTF-8 | -32602 | `character_encoding_not_allowed` |

См. также раздел [Список ошибок общих для всех методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#errors-list-all-methods)

### Вызов по сценарию

| Метод | start.scenario_call |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

#### Параметры запроса

| Название | Тип | Обязательный | Допустимые значения | Описание |
| access_token | string | да | Ключ сессии аутентификации |
| virtual_phone_number | string | да | 

Виртуальный номер, арендуемый клиентом. Формат номера должен соответствовать международному стандарту E.164 (например, `74993720692`). Всегда используется в качестве номера звонящего при звонке на номер, указанный в параметре `contact`. Используется в качестве номера звонящего при звонке на сотрудника, если в сценарии в операции "Переадресация" установлена настройка "Показывать при звонке" в значение "Номер услуги". Виртуальные номера можно получить с помощью REST API метода - [Получение списка виртуальных номеров](https://www.comagic.ru/support/article/137/#poluchenie_spiska_virtualnih_nomerov)

> В качестве виртуального номера запрещено использовать 800-е номера

 |
| external_id | string | нет | Уникальный идентификатор, который может быть использован для связи события звонка с внешней системой. |
| dtmf_string | string | нет | 0-9, *, # | Задаёт DTMF, который будет отправлен абоненту, заданному в параметре `contact`. С помощью символа `.` = '1 секунда' можно задать таймаут по истечению которого символ DTMF будет отправлен. Пример: `.12.1..4`, т.е через 1 секунду будет отправлена цифра 12, далее через 1 секунду будет отправлена цифра 1 и через 2 секунды цифра 4. |
| contact | string | да | 

Номер абонента на который совершается вызов. Формат номера должен соответствовать международному стандарту E.164 (например, `79091234567`). В качестве номера может быть так же указан SIP номер сотрудника.

> Если не задан параметр `contact_message`, то абоненту будет проигрываться системная мелодия - `Музыка переадресации (dialing_music)`

> Внутренние номера сотрудников не поддерживаются.

 |
| first_call | string | да | contact, employee | 

Определяет номер, на который нужно дозвониться в первую очередь:

*   `employee` - сотрудник;
*   `contact` - вызываемая сторона (обычно клиент);

 |
| switch_at_once | boolean | нет | true, false | 

Значение по умолчанию `false`.

Если параметр `first_call` имеет значение `employee`, задано сообщение сотруднику в сценарии и задан параметр `contact_message`, то:

*   Первый звонок совершается сотруднику из сценария;
*   Сотрудник после поднятия трубки слушает сообщение до конца и только потом совершается звонок на абонента заданного в параметре `contact`;
*   Во время дозвона до `contact`, сотрудник слушает системную мелодию - `Музыка переадресации (dialing_music)`;
*   После ответа абонента из параметра `contact`, произойдет разговор с сотрудником, если `switch_at_once` имеет значение `true`;
*   После ответа абонента из параметра `contact`, он дослушает сообщение до конца, если `switch_at_once` имеет значение `false` и далее произойдет разговор с сотрудником.

Если параметр `first_call` имеет значение `contact`, задан параметр `contact_message` и задано сообщение сотруднику в сценарии, то:

*   В первую очередь звонок совершается абоненту из параметр `contact`);
*   Абонент из параметра `contact` после поднятия трубки начинает слушать сообщение и в это же время совершается звонок на сотрудника из сценария;
*   После поднятия трубки сотрудником проигрывание сообщения будет прервано для абонента из параметра `contact` и далее состоится разговор, если параметр `switch_at_once` имеет значение `true`;
*   После поднятия трубки сотрудником сообщение будет проиграно до конца обоим и далее состоится разговор, если параметр `switch_at_once` имеет значение `false`;
*   Если у кого-то проигрывание сообщения закончилось раньше, то он слушает системную мелодию - `Музыка переадресации (dialing_music)`.

> Если в параметре `contact_message type` имеет значение `tts`, то параметр `switch_at_once=true` не работает

 |
| scenario_id | number | да | Уникальный идентификатор сценария, который может быть получен с помощью REST API - [Получение списка сценариев](https://www.comagic.ru/support/article/137/#poluchenie-spiska-scenariev). |
| direction | string | нет | in, out | Значение по умолчанию `out`.
Определяет направление звонка `in` - Входящий звонок, `out` - Исходящий звонок. |
| Сообщение для проигрывания абоненту, который задан в параметре `contact` |
| contact_message | object | нет | 

Определяет параметры сообщения, которое необходимо проиграть абоненту заданному в параметре `contact`.

> После окончания проигрывания сообщения, будет проигрываться покругу системная мелодия - `Музыка переадресации (dialing_music)`

 |
 type | string | да | media, tts | 

Определяет тип сообщения. `media` - файл или `tts` - текст для услуги синтеза речи Text-to-Speech.

 |
 value | string | да | 

Если поле `type` имеет значение `media`, то в качестве значения принимается идентификатор файла для проигрывания. Файл для проигрывания может быть системным или пользовательским. Идентификатор файла для проигрывания можно получить с помощью REST API - [Получение списка пользовательских файлов](https://www.comagic.ru/support/article/137/#media_files), [Получение списка системных файлов](https://www.comagic.ru/support/article/137/#system_media_files)

Если поле `type` имеет значение `tts`, то в качестве значения принимается текст для синтезирования его в голосовое сообщение.

> Длина TTS сообщения регулируется тарифным планом и установленным лимитом.

 |

#### Параметры ответа

| Название | Тип | Обязательный | Описание |
| call_session_id | number | да | Уникальный идентификатор сессии звонка |

#### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "method": "start.scenario_call",
  "id": "req1",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d",
    "virtual_phone_number": "74993720692",
    "external_id": "34rty567",
    "dtmf_string": "..1.2.3",
    "contact": "79260000000",
    "first_call": "employee",
    "switch_at_once": false,
    "scenario_id": 23456,
    "direction": "in",
    "contact_message": {
      "type": "media",
      "value": "237"
    }
  }
}
```

#### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": {
      "call_session_id": 234568
    }
  }
}
```

#### Список возвращаемых ошибок

| Текст ошибки | Код ошибки | Мнемоника ошибки | Описание |
| The maximum length of Text-to-Speech message is {`tts_message_max_length`}. The length of your message is {`sent_tts_message_length`} | -32602 | `tts_text_exceeded` | Длина сообщения превысила допустимое ограничение, установленное тарифным планом |
| The media file with id {`media_file_id`} not found | -32602 | `media_file_not_found` |
| Virtual phone number {`virtual_phone_number`} not found. It is not your virtual phone number. | -32007 | `virtual_phone_number_not_found` | Если используется виртуальный номер не принадлежащий клиенту или 800-й номер |
| Scenario with id {`scenario_id`} not found | -32602 | `scenario_not_found` |
| Parameter `contact` can not contain own virtual phone number | -32602 | `own_virtual_phone_number_not_allowed` | Звонок на собственный виртуальный номер запрещён |
| The contact {`contact`} has been found in the blacklist | -32602 | `contact_in_blacklist` |
| The character encoding must be UTF-8 | -32602 | `character_encoding_not_allowed` |

См. также раздел [Список ошибок общих для всех методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#errors-list-all-methods)

### Вызов на виртуальный номер

| Метод | start.vnumber_call |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

#### Параметры запроса

| Название | Тип | Обязательный | Допустимые значения | Описание |
| access_token | string | да | Ключ сессии аутентификации |
| virtual_phone_number | string | да | 

Виртуальный номер, арендуемый клиентом. Формат номера должен соответствовать международному стандарту E.164 (например, `74993720692`). Всегда используется в качестве номера звонящего при звонке на номер, указанный в параметре `contact`. Используется в качестве номера звонящего при звонке на сотрудника, если в сценарии в операции "Переадресация" установлена настройка "Показывать при звонке" в значение "Номер услуги". Виртуальные номера можно получить с помощью REST API метода - [Получение списка виртуальных номеров](https://www.comagic.ru/support/article/137/#poluchenie_spiska_virtualnih_nomerov)

> В качестве виртуального номера запрещено использовать 800-е номера

 |
| external_id | string | нет | Уникальный идентификатор, который может быть использован для связи события звонка с внешней системой. |
| dtmf_string | string | нет | 0-9, *, # | Задаёт DTMF, который будет отправлен абоненту, заданному в параметре `contact`. С помощью символа `.` = '1 секунда' можно задать таймаут по истечению которого символ DTMF будет отправлен. Пример: `.12.1..4`, т.е через 1 секунду будет отправлена цифра 12, далее через 1 секунду будет отправлена цифра 1 и через 2 секунды цифра 4. |
| direction | string | нет | in, out | Значение по умолчанию `out`.
Определяет направление звонка `in` - Входящий звонок, `out` - Исходящий звонок. |
| contact | string | да | 

Номер абонента на который совершается вызов. Формат номера должен соответствовать международному стандарту E.164 (например, `79091234567`). В качестве номера может быть так же указан SIP номер сотрудника.

> Если не задан параметр `contact_message`, то абоненту будет проигрываться системная мелодия - `Музыка переадресации (dialing_music)`

> Внутренние номера сотрудников не поддерживаются.

 |
| first_call | string | да | contact, employee | 

Определяет номер, на который нужно дозвониться в первую очередь:

*   `employee` - сотрудник;
*   `contact` - вызываемая сторона (обычно клиент);

 |
| switch_at_once | boolean | нет | true, false | 

Значение по умолчанию `false`.

Если параметр `first_call` имеет значение `employee`, задано сообщение сотруднику в сценарии и задан параметр `contact_message`, то:

*   Первый звонок совершается сотруднику из сценария;
*   Сотрудник после поднятия трубки слушает сообщение до конца и только потом совершается звонок на абонента заданного в параметре `contact`;
*   Во время дозвона до `contact`, сотрудник слушает системную мелодию - `Музыка переадресации (dialing_music)`;
*   После ответа абонента из параметра `contact`, произойдет разговор с сотрудником, если `switch_at_once` имеет значение `true`;
*   После ответа абонента из параметра `contact`, он дослушает сообщение до конца, если `switch_at_once` имеет значение `false` и далее произойдет разговор с сотрудником.

Если параметр `first_call` имеет значение `contact`, задан параметр `contact_message` и задано сообщение сотруднику в сценарии, то:

*   В первую очередь звонок совершается абоненту из параметр `contact`);
*   Абонент из параметра `contact` после поднятия трубки начинает слушать сообщение и в это же время совершается звонок на сотрудника из сценария;
*   После поднятия трубки сотрудником проигрывание сообщения будет прервано для абонента из параметра `contact` и далее состоится разговор, если параметр `switch_at_once` имеет значение `true`;
*   После поднятия трубки сотрудником сообщение будет проиграно до конца обоим и далее состоится разговор, если параметр `switch_at_once` имеет значение `false`;
*   Если у кого-то проигрывание сообщения закончилось раньше, то он слушает системную мелодию - `Музыка переадресации (dialing_music)`.

> Если в параметре `contact_message type` имеет значение `tts`, то параметр `switch_at_once=true` не работает

 |
| Сообщение для проигрывания абоненту, который задан в параметре `contact` |
| contact_message | object | нет | 

Определяет параметры сообщения, которое необходимо проиграть абоненту заданному в параметре `contact`.

> После окончания проигрывания сообщения, будет проигрываться покругу системная мелодия - `Музыка переадресации (dialing_music)`

 |
 type | string | да | media, tts | 

Определяет тип сообщения. `media` - файл или `tts` - текст для услуги синтеза речи Text-to-Speech.

 |
 value | string | да | 

Если поле `type` имеет значение `media`, то в качестве значения принимается идентификатор файла для проигрывания. Файл для проигрывания может быть системным или пользовательским. Идентификатор файла для проигрывания можно получить с помощью REST API - [Получение списка пользовательских файлов](https://www.comagic.ru/support/article/137/#media_files), [Получение списка системных файлов](https://www.comagic.ru/support/article/137/#system_media_files)

Если поле `type` имеет значение `tts`, то в качестве значения принимается текст для синтезирования его в голосовое сообщение.

> Длина TTS сообщения регулируется тарифным планом и установленным лимитом.

 |

#### Параметры ответа

| Название | Тип | Обязательный | Описание |
| call_session_id | number | да | Уникальный идентификатор сессии звонка |

#### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "method": "start.vnumber_call",
  "id": "req1",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d",
    "virtual_phone_number": "74993720692",
    "external_id": "34rty567",
    "dtmf_string": "..1.2.3",
    "direction": "in",
    "contact": "79260000000",
    "first_call": "employee",
    "switch_at_once": false,
    "show_virtual_phone_number": true,
    "contact_message": {
      "type": "media",
      "value": "237"
    }
  }
}
```

#### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": {
      "call_session_id": 123467589
    }
  }
}
```

#### Список возвращаемых ошибок

| Текст ошибки | Код ошибки | Мнемоника ошибки | Описание |
| The maximum length of Text-to-Speech message is {`tts_message_max_length`}. The length of your message is {`sent_tts_message_length`} | -32602 | `tts_text_exceeded` | Длина сообщения превысила допустимое ограничение, установленное тарифным планом |
| The media file with id {`media_file_id`} not found | -32602 | `media_file_not_found` |
| Virtual phone number {`virtual_phone_number`} not found. It is not your virtual phone number. | -32007 | `virtual_phone_number_not_found` | Если используется виртуальный номер не принадлежащий клиенту или 800-й номер |
| Virtual phone number does not have a scenario | -32007 | `scenario_not_found` |
| Parameter `contact` can not contain own virtual phone number | -32602 | `own_virtual_phone_number_not_allowed` | Звонок на собственный виртуальный номер запрещён |
| The contact {`contact`} has been found in the blacklist | -32602 | `contact_in_blacklist` |
| The character encoding must be UTF-8 | -32602 | `character_encoding_not_allowed` |

См. также раздел [Список ошибок общих для всех методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#errors-list-all-methods)

### Информационный вызов

| Метод | start.informer_call |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

> После окончания проигрывания сообщения абоненту вызов завершается.

#### Параметры запроса

| Название | Тип | Обязательный | Допустимые значения | Описание |
| access_token | string | да | Ключ сессии аутентификации |
| virtual_phone_number | string | да | 

Виртуальный номер, арендуемый клиентом. Формат номера должен соответствовать международному стандарту E.164 (например, `74993720692`). Всегда используется в качестве номера звонящего при звонке на номер, указанный в параметре `contact`. Виртуальные номера можно получить с помощью REST API метода - [Получение списка виртуальных номеров](https://www.comagic.ru/support/article/137/#poluchenie_spiska_virtualnih_nomerov)

> В качестве виртуального номера запрещено использовать 800-е номера

 |
| external_id | string | нет | Уникальный идентификатор, который может быть использован для связи события звонка с внешней системой. |
| dtmf_string | string | нет | 0-9, *, # | Задаёт DTMF, который будет отправлен абоненту, заданному в параметре `contact`. С помощью символа `.` = '1 секунда' можно задать таймаут по истечению которого символ DTMF будет отправлен. Пример: `.12.1..4`, т.е через 1 секунду будет отправлена цифра 12, далее через 1 секунду будет отправлена цифра 1 и через 2 секунды цифра 4. |
| direction | string | нет | in, out | Значение по умолчанию `out`.
Определяет направление звонка `in` - Входящий звонок, `out` - Исходящий звонок. |
| contact | string | да | 

Номер абонента на который совершается вызов. Формат номера должен соответствовать международному стандарту E.164 (например, `79091234567`). В качестве номера может быть так же указан SIP номер сотрудника.

> Внутренние номера сотрудников не поддерживаются.

 |
| dialing_timeout | number | нет | до 120 секунд | Значение по умолчанию `30`. Время ожидания ответа от номера, заданного в параметре `contact`, если ответ не был получен, то вызов завершается. Время задаётся в секундах. |
| Сообщение для проигрывания абоненту, который задан в параметре `contact` |
| contact_message | object | да | 

Определяет параметры сообщения, которое необходимо проиграть абоненту заданному в параметре `contact`.

 |
 type | string | да | media, tts | 

Определяет тип сообщения. `media` - файл или `tts` - текст для услуги синтеза речи Text-to-Speech.

 |
 value | string | да | 

Если поле `type` имеет значение `media`, то в качестве значения принимается идентификатор файла для проигрывания. Файл для проигрывания может быть системным или пользовательским. Идентификатор файла для проигрывания можно получить с помощью REST API - [Получение списка пользовательских файлов](https://www.comagic.ru/support/article/137/#media_files), [Получение списка системных файлов](https://www.comagic.ru/support/article/137/#system_media_files)

Если поле `type` имеет значение `tts`, то в качестве значения принимается текст для синтезирования его в голосовое сообщение.

> Длина TTS сообщения регулируется тарифным планом и установленным лимитом.

 |

#### Параметры ответа

| Название | Тип | Обязательный | Описание |
| call_session_id | number | да | Уникальный идентификатор сессии звонка |

#### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "method": "start.informer_call",
  "id": "req1",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d",
    "virtual_phone_number": "74993720692",
    "external_id": "34rty567",
    "dtmf_string": "..1.2.3",
    "direction": "in",
    "dialing_timeout": 25,
    "contact": "79260000000",
    "contact_message": {
      "type": "tts",
      "value": "Тестовое сообщение"
    }
  }
}
```

#### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": {
      "call_session_id": 1238694
    }
  }
}
```

#### Список возвращаемых ошибок

| Текст ошибки | Код ошибки | Мнемоника ошибки | Описание |
| The maximum length of Text-to-Speech message is {`tts_message_max_length`}. The length of your message is {`sent_tts_message_length`} | -32602 | `tts_text_exceeded` | Длина сообщения превысила допустимое ограничение, установленное тарифным планом |
| The media file with id {`media_file_id`} not found | -32602 | `media_file_not_found` |
| Virtual phone number {`virtual_phone_number`} not found. It is not your virtual phone number. | -32007 | `virtual_phone_number_not_found` | Если используется виртуальный номер не принадлежащий клиенту или 800-й номер |
| Parameter `contact` can not contain own virtual phone number | -32602 | `own_virtual_phone_number_not_allowed` | Звонок на собственный виртуальный номер запрещён |
| The contact {`contact`} has been found in the blacklist | -32602 | `contact_in_blacklist` |
| The character encoding must be UTF-8 | -32602 | `character_encoding_not_allowed` |

См. также раздел [Список ошибок общих для всех методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#errors-list-all-methods)

### Простой звонок

| Метод | start.simple_call |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

#### Параметры запроса

| Название | Тип | Обязательный | Допустимые значения | Описание |
| access_token | string | да | Ключ сессии аутентификации |
| first_call | string | да | contact, operator | 

Определяет номер, на который нужно дозвониться в первую очередь:

*   `operator` - оператор, которому будет доступно управление звонком через опции разговора;
*   `contact` - вызываемая сторона (обычно клиент);

 |
| switch_at_once | boolean | нет | true, false | 

Значение по умолчанию `false`.

Если параметр `first_call` имеет значение `operator` и задан параметр `operator_message`, то звонок совершается оператору, оператор после поднятия трубки слушает сообщение до конца, потом совершается звонок на `contact` и если параметр `switch_at_once` имеет значение `true` и задана параметр `contact_message`, то для плеча `contact` проигрываниесообщения будет прервано. Во время дозвона до `contact` `operator` слушает сообщение заданное в параметре `media_file_id`. Соответственно, если `switch_at_once` имеет значение `false` и задан параметр `contact_message`, то для плеча `contact` сообщение будет проиграно до конца. Если параметр `first_call` имеет значение `contact` и задан параметр `contact_message`, то звонок совершается клиенту, клиент после поднятия трубки начинает слушать сообщение и в это же время совершается звонок на `operator` и если параметр `switch_at_once` имеет значение `true` и задан параметр `operator_message`, то после поднятия трубки на `operator` проигрывание сообщений будет прервано для `contact` и `operator`.Соответственно, если `switch_at_once` имеет значение `false`, то для плеча `contact` и `operator` сообщение будет проиграно до конца. Если у кого-то плеча проигрывание сообщения закончилось раньше, то он слушает промт, заданный в параметре `media_file_id`. |
| early_switching | boolean | нет | true, false | 

Значение по умолчанию `false`.

Если параметр имеет значение `true`, то сотрудник при дозвоне до абонента будет слышать то, что происходит в абонентской линии.

К примеру, оператор ждет дозвона до абонента, а абонент недоступен и у него сработала голосовая почта, то при активации параметра `early_switching` = `true`, оператор сможет услышать сообщение о голосовой почте абонента. Если параметр `early_switching` = `false`, то оператор будет слушать музыку, которая задана в параметре "media_file_id"

> Параметр может иметь значение `true` если только параметр `first_call` имеет значение `operator` и параметр `switch_at_once` имеет значение значение `true`. В противном случае будет ошибка "-32602 invalid_parameters_combination The combination of parameters is not permitted" (см. раздел [коды ошибок](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-simple-call-errors)).

 |
| media_file_id | number | нет | 

Значение по умолчанию - системная мелодия "Музыка переадресации" (`dialing_music`).

Задаёт идентификатор звукового файла для музыки переадресации. Файл может быть как системным, так и пользовательским. Получить список системных или пользовательских файлов можно с помощью REST API - [Получение списка пользовательских файлов](https://www.comagic.ru/support/article/137/#media_files), [Получение списка системных файлов](https://www.comagic.ru/support/article/137/#system_media_files).

> Проигрывается всегда тому плечу, для которого не задан один из параметров `contact_message` или `operator_message`

 |
| virtual_phone_number | string | да | 

Виртуальный номер, арендуемый клиентом. Формат номера должен соответствовать международному стандарту E.164 (например, `74993720692`). Всегда используется в качестве номера звонящего при звонке на номер, указанный в параметре `contact`. Используется в качестве номера звонящего при звонке на номер, указанный в параметре `operator`, если параметр `show_virtual_phone_number` имеет значение `true`. Виртуальные номера можно получить с помощью REST API метода - [Получение списка виртуальных номеров](https://www.comagic.ru/support/article/137/#poluchenie_spiska_virtualnih_nomerov)

> В качестве виртуального номера запрещено использовать 800-е номера

 |
| show_virtual_phone_number | boolean | нет | true, false | 

Значение по умолчанию `true`.

Показывать ли виртуальный номер, заданный параметром `virtual_phone_number` в качестве номера звонящего абонента для оператора заданного в параметре `operator`.

 |
| contact | string | да | 

Номер абонента на который совершается вызов. Формат номера должен соответствовать международному стандарту E.164 (например, `79091234567`). В качестве номера может быть так же указан SIP номер сотрудника.

> Внутренние номера сотрудников не поддерживаются.

 |
| external_id | string | нет | Уникальный идентификатор, который может быть использован для связи события звонка с внешней системой. |
| dtmf_string | string | нет | 0-9, *, # | Задаёт DTMF, который будет отправлен абоненту, заданному в параметре `contact`. С помощью символа `.` = '1 секунда' можно задать таймаут по истечению которого символ DTMF будет отправлен. Пример: `.12.1..4`, т.е через 1 секунду будет отправлена цифра 12, далее через 1 секунду будет отправлена цифра 1 и через 2 секунды цифра 4. |
| direction | string | нет | in, out | Значение по умолчанию `out`.
Определяет направление звонка `in` - Входящий звонок, `out` - Исходящий звонок. |
| Оператор с которым будет соединён абонент из параметра `contact` |
| operator | object | да | Номер оператора с которым будет соединен абонент из параметра `contact`. Оператору доступно управление звонком - опции разговора. Формат номера должен соответствовать международному стандарту E.164 (например, “79091234567”).

> Не является сотрудником и в отчетах не будет фигурировать как сотрудник

 |
| contact_message | object | нет | 

Определяет параметры сообщения, которое необходимо проиграть абоненту заданному в параметре `contact`.

> После окончания проигрывания сообщения, будет проигрываться покругу сообщение из параметра `media_file_id`

 |
 type | string | да | media, tts | 

Определяет тип сообщения. `media` - файл или `tts` - текст для услуги синтеза речи Text-to-Speech.

 |
 value | string | да | 

Если поле `type` имеет значение `media`, то в качестве значения принимается идентификатор файла для проигрывания. Файл для проигрывания может быть системным или пользовательским. Идентификатор файла для проигрывания можно получить с помощью REST API - [Получение списка пользовательских файлов](https://www.comagic.ru/support/article/137/#media_files), [Получение списка системных файлов](https://www.comagic.ru/support/article/137/#system_media_files)

Если поле `type` имеет значение `tts`, то в качестве значения принимается текст для синтезирования его в голосовое сообщение.

> Длина TTS сообщения регулируется тарифным планом и установленным лимитом.

 |
| Сообщение для проигрывания абоненту, который задан в параметре `operator` |
| operator_message | object | нет | 

Определяет параметры сообщения, которое необходимо проиграть абоненту заданному в параметре `operator`.

> После окончания проигрывания сообщения, будет проигрываться покругу сообщение из параметра `media_file_id`

 |
 type | string | да | media, tts | 

Определяет тип сообщения. `media` - файл или `tts` - текст для услуги синтеза речи Text-to-Speech.

 |
 value | string | да | 

Если поле `type` имеет значение `media`, то в качестве значения принимается идентификатор файла для проигрывания. Файл для проигрывания может быть системным или пользовательским. Идентификатор файла для проигрывания можно получить с помощью REST API - [Получение списка пользовательских файлов](https://www.comagic.ru/support/article/137/#media_files), [Получение списка системных файлов](https://www.comagic.ru/support/article/137/#system_media_files)

Если поле `type` имеет значение `tts`, то в качестве значения принимается текст для синтезирования его в голосовое сообщение.

> Длина TTS сообщения регулируется тарифным планом и установленным лимитом.

 |

#### Параметры ответа

| Название | Тип | Обязательный | Описание |
| call_session_id | number | да | Уникальный идентификатор сессии звонка |

#### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "method": "start.simple_call",
  "id": "req1",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d",
    "first_call": "operator",
    "switch_at_once": true,
    "media_file_id": 2701,
    "show_virtual_phone_number": false,
    "virtual_phone_number": "74993720692",
    "external_id": "334otr01",
    "dtmf_string": ".1.2.3",
    "direction": "in",
    "contact": "79260000000",
    "operator": {
      "id": 25,
      "phone_number": "79260000001"
    },
    "contact_message": {
      "type": "tts",
      "value": "Привет"
    },
    "operator_message": {
      "type": "media",
      "value": "2561"
    }
  }
}
```

#### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": {
      "call_session_id": 237859081
    }
  }
}
```

#### Список возвращаемых ошибок

| Текст ошибки | Код ошибки | Мнемоника | Описание |
| The maximum length of Text-to-Speech message is {`tts_message_max_length`}. The length of your message is {`sent_tts_message_length`} | -32602 | `tts_text_exceeded` | Длина сообщения превысила допустимое ограничение, установленное тарифным планом |
| The media file with id {`media_file_id`} not found | -32602 | `media_file_not_found` |
| Virtual phone number {`virtual_phone_number`} not found. It is not your virtual phone number. | -32007 | `virtual_phone_number_not_found` | Если используется виртуальный номер не принадлежащий клиенту или 800-й номер |
| Parameter `contact` can not contain own virtual phone number | -32602 | `own_virtual_phone_number_not_allowed` | Звонок на собственный виртуальный номер запрещён |
| The contact {`contact`} has been found in the blacklist | -32002 | `contact_in_blacklist` |
| The character encoding must be UTF-8 | -32602 | `character_encoding_not_allowed` |

См. также раздел [Список ошибок общих для всех методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#errors-list-all-methods)



<div class="bs-docs-section">

## Группа методов управления вызовами

### Создать звонок для трансфера или консультации

| Метод | make.call |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

> Метод доступен для использования только после вызова "[hold.call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-hold-call)", см. раздел "[Диаграмма состояний звонка](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#state-diagram-call)"

#### Параметры запроса

| Название | Тип | Обязательный | Допустимые значения | Описание |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов [start.informer_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-informer-call), [start.vnumber_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-vnumber-call), [start.scenario_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-scenario-call), [start.employee_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-employee-call), с помощью [сервера уведомлений](http://help.comagic.ru/topics/86-nastrojka-uvedomlenij/) или [REST API](https://www.comagic.ru/support/article/137/#poluchenie-informacii-o-zvonkah). |
| to | string | да | 

Номер абонента на который совершаем звонок. Формат номера должен соответствовать международному стандарту E.164

> Может быть внутренний номер, внешний номер, номер сотрудника, sip-номер

 |
| Сообщение для проигрывания абоненту, который задан в параметре `to` |
| to_message | object | нет | 

Определяет параметры сообщения, которое необходимо проиграть абоненту заданному в параметре `to_message`.

> Абонент, который стоит на удержании, будет ожидать ответа, пока не будет прослушано сообщение до конца

 |
 type | string | да | media, tts | 

Определяет тип сообщения. `media` - файл или `tts` - текст для услуги синтеза речи Text-to-Speech.

 |
 value | string | да | 

Если поле `type` имеет значение `media`, то в качестве значения принимается идентификатор файла для проигрывания. Файл для проигрывания может быть системным или пользовательским. Идентификатор файла для проигрывания можно получить с помощью REST API - [Получение списка пользовательских файлов](https://www.comagic.ru/support/article/137/#media_files), [Получение списка системных файлов](https://www.comagic.ru/support/article/137/#system_media_files)

Если поле `type` имеет значение `tts`, то в качестве значения принимается текст для синтезирования его в голосовое сообщение.

> Длина TTS сообщения регулируется тарифным планом и установленным лимитом.

 |

#### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "method": "make.call",
  "id": "req1",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d",
    "call_session_id": 2354891,
    "to": "79260000000"
  }
}
```

#### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": {
      "success": "true"
    }
  }
}
```

#### Список возвращаемых ошибок

| Текст ошибки | Код ошибки | Мнемоника ошибки | Описание |
| This method can not be called in this state | -32004 | `invalid_state` | Метод может быть вызван только после метода [hold.call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-hold-call) |
| The character encoding must be UTF-8 | -32602 | `character_encoding_not_allowed` |
| The phone number does not exist or inactive | -32602 | `no_active_phone_number` | У сотрудника могут отсутствовать активные номера |

См. также раздел [Список ошибок общих для всех методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#errors-list-all-methods)

### Сделать трансфер звонка

| Метод | transfer.talk |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

> Метод доступен для использования только после вызова "[make.call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-make-call)", см. раздел "[Диаграмма состояний звонка](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#state-diagram-call)"

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов [start.vnumber_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-vnumber-call), [start.scenario_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-scenario-call), [start.employee_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-employee-call), с помощью [сервера уведомлений](http://help.comagic.ru/topics/86-nastrojka-uvedomlenij/) или [REST API](https://www.comagic.ru/support/article/137/#poluchenie-informacii-o-zvonkah). |

#### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "method": "transfer.talk",
  "id": "req1",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d",
    "call_session_id": 2786459
  }
}
```

#### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": {
      "success": "true"
    }
  }
}
```

#### Список возвращаемых ошибок

| Текст ошибки | Код ошибки | Мнемоника ошибки | Описание |
| This method can not be called in this state | -32004 | `invalid_state` | Метод может быть вызван, только после метода [make.call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-make-call) и в состоянии `dialing` или `operator_talk` (см. [Диаграмма состояний звонка](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#state-diagram-call)) |

См. также раздел [Список ошибок общих для всех методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#errors-list-all-methods)

### Возвращение в разговор

| Метод | restore.talk |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

> Метод доступен для использования только после вызова "[make.call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-make-call)", см. раздел "[Диаграмма состояний звонка](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#state-diagram-call)"

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов [start.vnumber_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-vnumber-call), [start.scenario_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-scenario-call), [start.employee_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-employee-call), с помощью [сервера уведомлений](http://help.comagic.ru/topics/86-nastrojka-uvedomlenij/) или [REST API](https://www.comagic.ru/support/article/137/#poluchenie-informacii-o-zvonkah). |

#### Параметры запроса

```json
{
  "jsonrpc": "2.0",
  "method": "restore.talk",
  "id": "req1",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d",
    "call_session_id": 25374860
  }
}
```

#### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": {
      "success": "true"
    }
  }
}
```

#### Список возвращаемых ошибок

| Текст ошибки | Код ошибки | Мнемоника ошибки | Описание |
| This method can not be called in this state | -32004 | `invalid_state` | Метод может быть вызван, только после метода [make.call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-make-call) и в состоянии `dialing` или `operator_talk` (см. [Диаграмма состояний звонка](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#state-diagram-call)) |

См. также раздел [Список ошибок общих для всех методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#errors-list-all-methods)

### Постановка вызова на удержание

| Метод | hold.call |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

#### Параметры запроса

| Название | Тип | Обязательный | Допустимые значения | Описание |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов [start.vnumber_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-vnumber-call), [start.scenario_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-scenario-call), [start.employee_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-employee-call), с помощью [сервера уведомлений](http://help.comagic.ru/topics/86-nastrojka-uvedomlenij/) или [REST API](https://www.comagic.ru/support/article/137/#poluchenie-informacii-o-zvonkah). |
| Проигрывание сообщения вызывающему абоненту |
| contact_message | object | да | Определяет параметры сообщения, которое необходимо проиграть вызывающему абоненту. Вызывающий абонент - это номер, указанный в параметре `contact` в методах [start.informer_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-informer-call), [start.vnumber_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-vnumber-call), [start.scenario_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-scenario-call), [start.employee_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-employee-call) или абонент позвонивший на виртуальную АТС. |
 type | string | да | media, tts | 

Определяет тип сообщения. `media` - файл или `tts` - текст для услуги синтеза речи Text-to-Speech.

 |
 value | string | да | 

Если поле `type` имеет значение `media`, то в качестве значения принимается идентификатор файла для проигрывания. Файл для проигрывания может быть системным или пользовательским. Идентификатор файла для проигрывания можно получить с помощью REST API - [Получение списка пользовательских файлов](https://www.comagic.ru/support/article/137/#media_files), [Получение списка системных файлов](https://www.comagic.ru/support/article/137/#system_media_files)

Если поле `type` имеет значение `tts`, то в качестве значения принимается текст для синтезирования его в голосовое сообщение.

> Длина TTS сообщения регулируется тарифным планом и установленным лимитом.

 |

#### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "method": "hold.call",
  "id": "req1",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d",
    "call_session_id": 23465781,
    "contact_message": {
      "type": "media",
      "value": "2034"
    }
  }
}
```

#### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": {
      "success": "true"
    }
  }
}
```

#### Список возвращаемых ошибок

| Текст ошибки | Код ошибки | Мнемоника ошибки | Описание |
| This method can not be called in this state | -32004 | `invalid_state` | Метод может быть вызван только в состоянии `Talk` (см. [Диаграмма состояний звонка](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#state-diagram-call)) |
| The media file with id {`media_file_id`} not found | -32602 | `media_file_not_found` |
| The maximum length of Text-to-Speech message is {`tts_message_max_length`}. The length of your message is {`sent_tts_message_length`} | -32602 | `tts_text_exceeded` | Длина сообщения превысила допустимое ограничение, установленное тарифным планом |
| The character encoding must be UTF-8 | -32602 | `character_encoding_not_allowed` |

См. также раздел [Список ошибок общих для всех методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#errors-list-all-methods)

### Снятие вызова с удержания

| Метод | unhold.call |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

> Метод доступен для использования только после вызова "[hold.call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-hold-call)", см. раздел "[Диаграмма состояний звонка](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#state-diagram-call)"

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов [start.vnumber_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-vnumber-call), [start.scenario_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-scenario-call), [start.employee_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-employee-call), с помощью [сервера уведомлений](http://help.comagic.ru/topics/86-nastrojka-uvedomlenij/) или [REST API](https://www.comagic.ru/support/article/137/#poluchenie-informacii-o-zvonkah). |

#### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "method": "unhold.call",
  "id": "req1",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d",
    "call_session_id": 2846590
  }
}
```

#### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": {
      "success": "true"
    }
  }
}
```

#### Список возвращаемых ошибок

| Текст ошибки | Код ошибки | Мнемоника ошибки | Описание |
| This method can not be called in this state | -32004 | `invalid_state` | Метод может быть вызван только в состоянии `hold` (см. [Диаграмма состояний звонка](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#state-diagram-call)) |

См. также раздел [Список ошибок общих для всех методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#errors-list-all-methods)

### Простановка тэга

| Метод | tag.call |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

> Тег может быть проставлен только на активный вызов. На завершенный вызов тег можно проставить с помощью REST API метода - [Установка тега обращению](https://www.comagic.ru/support/article/137/#ustanovka-tega-obrasheniya)

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов [start.informer_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-informer-call), [start.vnumber_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-vnumber-call), [start.scenario_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-scenario-call), [start.employee_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-employee-call), с помощью [сервера уведомлений](http://help.comagic.ru/topics/86-nastrojka-uvedomlenij/) или [REST API](https://www.comagic.ru/support/article/137/#poluchenie-informacii-o-zvonkah). |
| tag_id | number | да | Уникальный идентификатор тега, который можно получить с помощью REST API метода - [Получение списка тегов](https://www.comagic.ru/support/article/137/#poluchenie-spiska-tegov) |

#### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "method": "tag.call",
  "id": "req1",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d",
    "call_session_id": 2846590,
    "tag_id": 36
  }
}
```

#### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": {
      "success": "true"
    }
  }
}
```

#### Список возвращаемых ошибок

| Текст ошибки | Код ошибки | Мнемоника ошибки | Описание |
| Tag with {`tag_id`} not found | -32602 | `tag_not_found` |
| Duplicate tag | -32602 | `duplicate_tag` |

См. также раздел [Список ошибок общих для всех методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#errors-list-all-methods)

### Завершение разговора отдельного участника

| Метод | disconnect.leg |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

> Завершение плеча `contact` (см. методы [start.employee_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-employee-call), [start.scenario_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-scenario-call), [start.informer_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-informer-call), [start.vnumber_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-vnumber-call)) приводит к завершению всей сессии звонка

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов [start.informer_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-informer-call), [start.vnumber_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-vnumber-call), [start.scenario_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-scenario-call), [start.employee_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-employee-call), с помощью [сервера уведомлений](http://help.comagic.ru/topics/86-nastrojka-uvedomlenij/) или [REST API](https://www.comagic.ru/support/article/137/#poluchenie-informacii-o-zvonkah). |
| leg_id | number | да | Уникальный идентификатор участника разговора, который может быть получен через [сервера уведомлений](http://help.comagic.ru/topics/86-nastrojka-uvedomlenij/). |

#### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "method": "disconnect.leg",
  "id": "req1",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d",
    "call_session_id": 2875654,
    "leg_id": 9875
  }
}
```

#### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": {
      "success": "true"
    }
  }
}
```

#### Список возвращаемых ошибок

| Текст ошибки | Код ошибки | Мнемоника ошибки | Описание |
| The call's leg not found | -32602 | `leg_not_found` |

См. также раздел [Список ошибок общих для всех методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#errors-list-all-methods)

### Подключение тренера к разговору

| Метод | add.coach |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

> Метод позволяет подключить к разговору тренера, который задан в личном кабинете или указан в параметре `phone_number`

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов [start.vnumber_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-vnumber-call), [start.scenario_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-scenario-call), [start.employee_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-employee-call), с помощью [сервера уведомлений](http://help.comagic.ru/topics/86-nastrojka-uvedomlenij/) или [REST API](https://www.comagic.ru/support/article/137/#poluchenie-informacii-o-zvonkah). |
| phone_number | string | нет | Номер тренера, если не используется тренер указанный в личном кабинете.

> Если номер тренера не задан, то подключается тренер из настроек сотрудника в личном кабинете

 |

#### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "method": "add.coach",
  "id": "req1",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d",
    "call_session_id": 27934036,
    "phone_number": "79260000000"
  }
}
```

#### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": {
      "success": "true"
    }
  }
}
```

#### Список возвращаемых ошибок

| Текст ошибки | Код ошибки | Мнемоника ошибки | Описание |
| The coach not found in your settings | -32602 | `coach_not_found` |
| The phone number does not exist or inactive | -32602 | `no_active_phone_number` |

См. также раздел [Список ошибок общих для всех методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#errors-list-all-methods)

### Управление записью разговора

| Метод | record.call |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

> Метод позволяет управлять записью активного разговора. Глобальную запись разговора выключить невозможно.

#### Параметры запроса

| Название | Тип | Обязательный | Допустимые значения | Описание |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов [start.informer_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-informer-call), [start.vnumber_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-vnumber-call), [start.scenario_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-scenario-call), [start.employee_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-employee-call), с помощью [сервера уведомлений](http://help.comagic.ru/topics/86-nastrojka-uvedomlenij/) или [REST API](https://www.comagic.ru/support/article/137/#poluchenie-informacii-o-zvonkah). |
| action | string | да | on, off | Действие над записью разговора. `on` - включение записи разговора, `off` - выключение записи разговора |

#### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "method": "record.call",
  "id": "req1",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d",
    "call_session_id": 235496,
    "action": "on"
  }
}
```

#### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": {
      "success": "true"
    }
  }
}
```

#### Список возвращаемых ошибок

| Текст ошибки | Код ошибки | Мнемоника ошибки | Описание |
| Permission denied | -32003 | `forbidden` | Невозможно отключить глобальную запись разговора. Глобальной записью разговора является настройка на уровне настройки сотрудника. |
| The call record is already started | -32602 | `record_already_started` | Запись разговора уже включена |
| The call record is not enabled | -32602 | `record_already_stopped` | Запись разговора не была включена |

См. также раздел [Список ошибок общих для всех методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#errors-list-all-methods)

### Занести абонента в чёрный список

| Метод | block.contact |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов [start.informer_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-informer-call), [start.vnumber_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-vnumber-call), [start.scenario_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-scenario-call), [start.employee_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-employee-call), с помощью [сервера уведомлений](http://help.comagic.ru/topics/86-nastrojka-uvedomlenij/) или [REST API](https://www.comagic.ru/support/article/137/#poluchenie-informacii-o-zvonkah). |

#### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "method": "block.contact",
  "id": "req1",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d",
    "call_session_id": 27485639
  }
}
```

#### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": {
      "success": "true"
    }
  }
}
```

#### Список возвращаемых ошибок

| Текст ошибки | Код ошибки | Мнемоника ошибки | Описание |
| The contact with phone number {`contact_phone_number`} already exists | -32602 | `duplicate_contact` |

См. также раздел [Список ошибок общих для всех методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#errors-list-all-methods)

### Использование опций разговора

> Опции разговора настраиваются в личном кабинете "Виртуальная АТС" -> "Опции разговора". Повторный вызов отдельных опций разговора отключает их, примерами таких опций являются `Диктофон`, `Позвонить тренеру` (см. метод [list.talk_options](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-list-talk-options))

| Метод | call.talk_option |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов [start.informer_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-informer-call), [start.vnumber_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-vnumber-call), [start.scenario_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-scenario-call), [start.employee_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-employee-call), с помощью [сервера уведомлений](http://help.comagic.ru/topics/86-nastrojka-uvedomlenij/) или [REST API](https://www.comagic.ru/support/article/137/#poluchenie-informacii-o-zvonkah). |
| button | string | да | Клавиша, вызывающая опцию разговора.

> Клавиши и действия настраиваются в личном кабинете.

 |

#### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "method": "call.talk_option",
  "id": "req1",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d",
    "call_session_id": 246578,
    "button": "1"
  }
}
```

#### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": {
      "success": "true"
    }
  }
}
```

#### Список возвращаемых ошибок

| Текст ошибки | Код ошибки | Мнемоника ошибки | Описание |
| This method can not be called in this state | -32004 | `invalid_state` |
| Internal error, contact the support service | -32603 | `internal_error` |
| Permission denied | -32003 | `forbidden` | Невозможно отключить глобальную запись разговора на уровне настройки сотрудника |
| Call session not found | -32602 | `call_session_not_found` |
| Talk option not found | -32602 | `talk_option_not_found` | Клавиша не назначена |

См. также раздел [Список ошибок общих для всех методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#errors-list-all-methods)

### Отправка DTMF сотрудником

| Метод | send.dtmf |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

#### Параметры запроса

| Название | Тип | Обязательный | Допустимые значения | Описание |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов [start.informer_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-informer-call), [start.vnumber_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-vnumber-call), [start.scenario_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-scenario-call), [start.employee_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-employee-call), с помощью [сервера уведомлений](http://help.comagic.ru/topics/86-nastrojka-uvedomlenij/) или [REST API](https://www.comagic.ru/support/article/137/#poluchenie-informacii-o-zvonkah). |
| dtmf_string | string | да | 0-9, *, # | Задаёт DTMF, который будет отправлен абоненту, который задан в параметре `contact`. В случае исходящего вызова с виртуальной АТС в сторону вызываемого абонента.

> Отправка DTMF возможна только одиночными символами.

 |

#### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "method": "send.dtmf",
  "id": "req1",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d",
    "call_session_id": 27934036,
    "dtmf_string": "1"
  }
}
```

#### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": {
      "success": "true"
    }
  }
}
```

### Получить список опций разговора

| Метод | list.talk_options |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

> Опции разговора настраиваются в личном кабинете "Виртуальная АТС" -> "Опции разговора".

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| access_token | string | да | Ключ сессии аутентификации |

#### Параметры ответа

| Название | Тип | Обязательный | Описание |
| button | string | да | Клавиша, которая вызывает опцию разговора |
| mnemonic | string | да | Мнемоническое имя опции разговора Возможные значения:

*   finish_conversation - Завершить разговор
*   tag_call - Проставить тег
*   record_talk - Диктофон (запись разговора)
*   transfer_call - Трансфер
*   run_scenario - Выполнить сценарий
*   call_coach - Позвонить тренеру
*   receive_fax - Принять факс
*   save_numa - Сохранить АОН в группу

 |
| name | string | да | Название опции разговора |
| Значение опции |
| button_value | object | нет | Некоторые опции могут содержать название тега, название сценария и т.д. Данный объект содержит дополнительную информацию об опции разговора |
 id | number | да | Уникальный идентификатор объекта, котрый активируется при вызове опции разговора. К примеру, идентификатор сценария |
 value | string | да | Название объекта, который активируется при вызове опции разговора. К примеру, название сценария |

#### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "method": "list.talk_options",
  "id": "req1",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d"
  }
}
```

#### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": [
      {
        "button": "1",
        "mnemonic": "tag_call",
        "name": "Проставить тег",
        "button_value": {
          "id": 254,
          "value": "Целевой"
        }
      }
    ]
  }
}
```

#### Получить список активных разговоров

| Метод | list.calls |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

##### Параметры запроса

| Название | Тип | Обязательный | Допустимые значения | Описание |
| access_token | string | да | Ключ сессии аутентификации |
| direction | string | нет | in, out | Указывает какие сессии выводить - входящие, исходящие. Если параметр не задан, то выводятся все сессии. |
| virtual_phone_number | string | нет | Указывает с каким виртуальным номер активные вызовы показывать.

> Номер должен начинаться с 7

 |

##### Параметры ответа

| Название | Тип | Обязательный | Допустимые значения | Описание |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов [start.informer_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-informer-call), [start.vnumber_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-vnumber-call), [start.scenario_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-scenario-call), [start.employee_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-employee-call), с помощью [сервера уведомлений](http://help.comagic.ru/topics/86-nastrojka-uvedomlenij/) или [REST API](https://www.comagic.ru/support/article/137/#poluchenie-informacii-o-zvonkah). |
| direction | string | да | in, out | Направление сесси звонка |
| start_time | string | да | Время начала вызова. Формат `YYYY-MM-DD hh:mm:ss` |
| virtual_phone_number | string | да | Виртуальный номер, который был использован как номер представления. |
| contact_phone_number | string | да | Номер абонента |
| external_id | string | да | Уникальный идентификатор запроса во внешней системе клиента |
| Список проставленных тегов |
| tags | array | да | Список проставленных тегов |
 tag_id | number | да | Уникальный идентификатор тега |
 tag_name | string | да | Имя тега |
| Участники сессии звонка |
| legs | array | да | Список участников сессии звонка |
 leg_id | number | да | Уникальный идентификатор плеча |
 calling_phone_number | string | да | Номер вызывающего абонента |
 called_phone_number | string | да | Номер вызываемого абонента |
 is_operator | boolean | да | true, false | Признак, который идентифицирует плечо, у которого есть права на выполнение опций разговора |
 employee_id | number | да | Уникальный идентификатор сотрудника |
 employee_full_name | string | да | Ф.И.О сотрудника |
 record_call_enabled | boolean | да | true, false | 

Включена/Выключена запись разговора.
`true` - включена, `false` - выключена

 |
 state | string | да | 

Состояние участника разговора

Возможные значения:

*   Дозвон
*   Разговор
*   На очереди
*   Разъединено
*   Разговор операторов
*   На удержании
*   Перевод вызова
*   Отправка dtmf
*   Получение факса
*   Факс получен
*   Отправка факса
*   Факс отправлен

 |

##### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "method": "list.calls",
  "id": "req1",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d",
    "direction": "in",
    "virtual_phone_number": "74951045771"
  }
}
```

##### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": [
      {
        "call_session_id": 206597836,
        "direction": "in",
        "start_time": "2016-10-19T12:26:48.418",
        "virtual_phone_number": "74951045771",
        "contact_phone_number": "74959268686",
        "external_id": null,
        "tags": [
          {
            "tag_id": 456,
            "tag_name": "Целевой"
          }
        ],
        "legs": [
          {
            "leg_id": 287866245,
            "calling_phone_number": "74951045771",
            "called_phone_number": "74959268686...9.2.3.3",
            "is_operator": false,
            "employee_id": null,
            "employee_full_name": null,
            "record_call_enabled": true,
            "state": "Разговор"
          },
          {
            "leg_id": 287866221,
            "calling_phone_number": "74959268686",
            "called_phone_number": "79262444393",
            "is_operator": true,
            "employee_id": 2345,
            "employee_full_name": "Тест",
            "record_call_enabled": true,
            "state": "Разговор"
          }
        ]
      }
    ]
  }
}
```

#### Завершение сессии звонка

| Метод | release.call |
| Версия API | v4.0 |
| [Вернуться к списку методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list) |

##### Параметры запроса

| Название | Тип | Обязательный | Описание |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов [start.informer_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-informer-call), [start.vnumber_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-vnumber-call), [start.scenario_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-scenario-call), [start.employee_call](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#api-methods-start-employee-call), с помощью [сервера уведомлений](http://help.comagic.ru/topics/86-nastrojka-uvedomlenij/) или [REST API](https://www.comagic.ru/support/article/137/#poluchenie-informacii-o-zvonkah). |

##### Пример запроса

```json
{
  "jsonrpc": "2.0",
  "method": "release.call",
  "id": "req1",
  "params": {
    "access_token": "2fRN4g217ca0b4224a67988aff3e584f91964a692045415f36fa66146f5a3c1ae1f6093d",
    "call_session_id": 28575639
  }
}
```

##### Пример ответа

```json
{
  "jsonrpc": "2.0",
  "id": "req1",
  "result": {
    "data": {
      "success": "true"
    }
  }
}
```

## Сообщения об ошибках

| Название | Тип | Обязательный | Допустимые значения | Описание |
| error | object | да | Объект с содержимым ошибки |
 code | number | да | Код ошибки (см. раздел [Группы кодов ошибок](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#errors-code-groups)) |
 message | string | да | Сообщение об ошибке (см. раздел [Список ошибок общих для всех методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#errors-list-all-methods)) |
 data | object | да | Объект с деталями ошибки |
 mnemonic | string | да | Уникальный текстовый код ошибки (см. раздел [Список ошибок общих для всех методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#errors-list-all-methods), а так же существуют ошибки специфичные для отдельных методов) |
 field | string | нет | 

Название параметра, с которым связана ошибка

> 

Вложенные параметры отображаем через разделитель "точка": `.`

К примеру: `employee.phone_number`



 |
 value | string | нет | 

Содержит то, что передал пользователь без изменений

> В некоторых случаях может отсутствовать. К примеру, обязательный параметр вообще не был заполнен.

 |
 params | object | нет | Карта подстановок параметров для шаблона с текстом об ошибке. Т.е. содержит динамически изменяемые значения, к примеру, лимиты, длина TTS сообщения. Значения указанные в этом параметре могут быть использованы в сообщениях об ошибках в интерфейсе над Call API (рабочее место оператора). |
 extended_helper | string | нет | Ссылка на более подробное описание ошибки и возможные решения |

### Пример ошибки

```json
{
  "jsonrpc": "2.0",
  "id": null,
  "error": {
    "code": -32602,
    "message": "Data supplied is of wrong type",
    "data": {
      "mnemonic": "data_type_error",
      "field": "contact",
      "value": "номер",
      "params": {
        "object": null
      },
      "extended_helper": null
    }
  }
}
```

### Группы кодов ошибок

| Код ошибки | Описание |
| -32700 | Ошибки связанные с валидацией JSON |
| -32600 | Ошибки связанные с валидацией параметров запроса - `id`, `jsonrpc` |
| -32601 | Ошибки связанные с методом |
| -32602 | Ошибки связанные с валидацией параметров в вызываемом методе |
| -32603 | Внутренние ошибки JSON RPC сервера

> В случае возникновения ошибки, необходимо обратиться в службу технической поддержки, передав запрос, который вызвал ошибку и время запроса.

 |
| -32001 | Ошибки аутентификации и ошибки с ключами |
| -32002 | Ошибки связанные с: правила безопасности запрещают звонок по указанному направлению (см. раздел [Правила безопасности](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#safety-management)) и черным списком |
| -32003 | Ошибки с правами доступа - ip адрес не в белом списке, нет прав у пользователя |
| -32004 | Ошибки связанные с неверной последовательностью вызываемых методов |
| -32007 | Ошибки связанные с виртуальным номером |
| -32008 | Ошибки связанные с компонентами |
| -32009 | Ошибки связанные с аккаунтом

> В случае возникновения ошибки, необходимо обратиться в службу технической поддержки, передав запрос, который вызвал ошибку и время запроса.

 |
| -32029 | Ошибки связанные с лимитами |
| -32099 | Ошибки связанные с поддержкой различных частей спецификации JSON RPC 2.0 - Групповые операции, Уведомления |

### Список ошибок общих для всех методов

| Текст сообщения | Код | Мнемоника | Описание |
| Invalid Request The JSON sent is not a valid Request object | -32600 | `invalid_request` | Ошибки связанные с валидацией параметров запроса - `id`, `jsonrpc` |
| Access token has been expired | -32001 | `access_token_expired` | Применяется только к постоянному токену. Если время жизни постоянного токена истекло, то возвращается указанная ошибка |
| Access token has been blocked | -32001 | `access_token_blocked` | Если постоянный токен заблокирован, то возвращается указанная ошибка |
| Access token is invalid | -32001 | `access_token_invalid` | Указанная ошибка возвращается, если постоянный/временный токен не найден или истекло время жизни временного токена |
| Limit per {limit_type} has been exceeded. Value of current limit per {limit_type} is {limit_max_value} | -32029 | `limit_exceeded` | Превышены установленные лимиты согласно тарифного правила (см. раздел [Лимиты и ограничения](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#limits)) |
| Component {component_name} has been disabled | -32008 | `component_disabled` |
| Your IP {ip} is not whitelisted | -32003 | `ip_not_whitelisted` | IP адрес, с которого идет запрос не находится в белом списке (см. личный кабинет "Аккаунт" -> "Call API" вкладка "IP адреса" |
| Login or password is wrong | -32001 | `auth_error` | Некорректный логин или пароль |
| Your account has been disabled, contact the support service | -32009 | `account_inactive` | Аккаунт заблокирован

> В случае возникновения ошибки, необходимо обратиться в службу технической поддержки, передав запрос, который вызвал ошибку и время запроса.

 |
| Call session not found | -32602 | `call_session_not_found` | Если передали ID сессии, который неизвестен |
| Internal error, contact the support service | -32603 | `internal_error` | 

> В случае возникновения ошибки, необходимо обратиться в службу технической поддержки, передав запрос, который вызвал ошибку и время запроса.

 |
| Data supplied is of wrong type | -32602 | `data_type_error` | К примеру, если ожидаем `string` а передали `int` |
| The method does not exist / is not available | -32601 | `method_not_found` | Вызывается метод, который отсутствует в спецификации (см. раздел [Список методов](https://www.comagic.ru/upload/iblock/ec0/index-doc-call-api.html#methods-list)) |
| Permission denied | -32003 | `forbidden` | Нет прав на доступ к методу или API, или запрещено выполнять какое-либо действие |
| Invalid JSON was received by the server. | -32700 | `parse_error` | Невалидный JSON |
| Batch operations not supported | -32099 | `batch_opreations_not_supported` | Групповые операции не поддерживаются (см. [JSON-RPC 2.0](http://www.jsonrpc.org/specification)) |
| Notifications not supported | -32099 | `notifications_not_supported` | Уведомления не поддерживаются (см. [JSON-RPC 2.0](http://www.jsonrpc.org/specification)) |
| The required parameter has been missed | -32602 | `required_parameter_missed` | Обязательный параметр не передали |
| Invalid parameter value | -32602 | `invalid_parameter_value` | Возвращается во всех случаях, если было передано некорректное значение параметра или переданное значение не соответствует требуемому формату ввода |
| Unexpected method parameter(s) | -32602 | `unexpected_parameters` | Если в `params` были переданы параметры, которые не предусмотрены JSON структурой метода |



