# Call API

## Знакомство

### Введение

Настоящий документ описывает Call API, которое позволяет совершать звонки и управлять ими.

> Управлять можно так же звонками, которые поступили на виртуальную АТС. Для этого необходимо получить уникальный идентификатор сессии звонка. Его можно получить с помощью сервера уведомлений, REST API.

API реализован на основе спецификации JSON-RPC 2.0 без поддержки групповых операций.

При запросах к API в качестве транспортного протокола используется HTTP/1.1 с защитой соединения с помощью SSL/TLS, при этом соблюдая следующие требования:

*   Заголовок `Content-Type` должен быть `application/json; charset=UTF-8`
*   Заголовок `Content-Length` должен содержать корректную длину сообщения, следуя спецификации HTTP/1.1

### Список методов

<table>
    <tbody><tr>
        <th>Название</th>
        <th>Описание</th>
    </tr>
    <tr>
    <td class="color-bg" colspan="4" align="center">Аутентификация</td>
    </tr>
    <tr>
        <td><code>login.user</code></td>
        <td>Аутентификация: Вход</td>
    </tr>
    <tr>
        <td><code>logout.user</code></td>
        <td>Аутентификация: Выход</td>
    </tr>
    <tr>
        <td class="color-bg" colspan="4" align="center">Группа методов создания звонков</td>
    </tr>
    <tr>
        <td><code>start.employee_call</code></td>
        <td>Вызов на сотрудника. Создает прямой звонок абонента с сотрудником без использования сценария.</td>
    </tr>
    <tr>
        <td><code>start.scenario_call</code></td>
        <td>Вызов по сценарию. Создает звонок согласно настроенному сценарию. Для использования метода достаточно одного виртуального номера и N сценариев.</td>
    </tr>
    <tr>
        <td>
        </td>
        <td>Вызов на виртуальный номер АТС, динамического или статического коллтрекинга</td>
    </tr>
    <tr>
        <td><code>start.informer_call</code></td>
        <td>Информационный вызов с возможностью проиграть абоненту файл или текстовое сообщение. После окончания проигрывания сообщения вызов завершается автоматически.</td>
    </tr>
    <tr>
        <td><code>start.simple_call</code></td>
        <td>Звонок на любые номера кроме собственных виртуальных</td>
    </tr>                   
    <tr>
        <td class="color-bg" colspan="4" align="center">Группа методов управления звонками</td>
    </tr>
    <tr>
        <td><code>make.call</code></td>
        <td>Создать звонок для трансфера или консультации</td>
    </tr>
    <tr>
        <td><code>transfer.talk</code></td>
        <td>Метод позволяет произвести трансфер звонка другому сотруднику или на внешний номер (см. раздел "Диаграмма состояний звонка")</td>
    </tr>
    <tr>
        <td><code>restore.talk</code></td>
        <td>Метод позволяет вернуться в разговор с абонентом после получения консультации от другого сотрудника (см. раздел "Диаграмма состояний звонка")</td>
    </tr>
    <tr>
        <td><code>hold.call</code></td>
        <td>Постановка вызова на удержание</td>
    </tr>
    <tr>
        <td><code>unhold.call</code></td>
        <td>Снятие вызова с удержания</td>
    </tr>
    <tr>
        <td><code>add.coach</code></td>
        <td>Подключение тренера к разговору</td>
    </tr>
    <tr>
        <td><code>tag.call</code></td>
        <td>Простановка тега для активного вызова</td>
    </tr>
    <tr>
        <td><code>record.call</code></td>
        <td>
            Управление записью разговора<br>
            <i class="fa fa-exclamation-triangle" aria-hidden="true"></i> Отключить запись разговора, настроенную через личный кабинет невозможно
        </td>
    </tr>
    <tr>
        <td><code>disconnect.leg</code></td>
        <td>Метод позволяет разъединять отдельных участников разговора. Уникальный идентификатор каждого участника разговора можно получить используя метод "list.calls" или сервер уведомлений</td>
    </tr>
    <tr>
        <td><code>send.dtmf</code></td>
        <td>Отправка DTMF сотрудником</td>
    </tr>
    <tr>
        <td><code>block.contact</code></td>
        <td>Занести абонента в чёрный список во время разговора</td>
    </tr>
    <tr>
        <td><code>list.calls</code></td>
        <td>Получить список активных вызовов и их участников</td>
    </tr>
    <tr>
        <td><code>list.talk_options</code></td>
        <td>Получение списка опций разговора настроенных в Личном кабинете виртуальной АТС</td>
    </tr>
    <tr>
        <td><code>call.talk_option</code></td>
        <td>Вызов опции разговора, которая настроена в Личном кабинете виртуальной АТС</td>
    </tr>
    <tr>
        <td><code>release.call</code></td>
        <td>Завершить вызов</td>
    </tr>
</tbody></table>

### Соглашения

Приняты следующие соглашения:

*   Пустые поля всегда возвращаются в ответе со значением `null`. В случае массива возвращается пустой массив, в случае объекта возвращается пустой объект;
*   Все поля связанные с датой и временем передаются согласно ISO 8601 в формате `YYYY-MM-DDT hh:mm:ssZ`;
*   Запросы к API выполняются всегда с помощью метода POST;
*   Все параметры в запросах/ответах, а также в структурах данных в формате JSON и название методов именуются в стиле Snake Case - разделение слов через нижнее подчёркивание;
*   Данные возвращаются только в JSON формате согласно спецификации RFC 7159;
*   Кодировка данных UTF-8;

### Формат возвращаемых данных

Формат | MIME Type
--- | ---
JSON | application/json

> По умолчанию используется формат JSON. Заголовок Accept игнорируется

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

`<version>` - версия API (см. раздел Версионность)

### Версионность

Call API поддерживает версионность. Версия указывается в базовом URL как vX.Y, где X - номер мажорной версии, Y - номер минорной версии

> Версия должна указываться через точку, к примеру 4.0

> Если была выпущена новая версия, то старая считается устаревшей и соответственно при обращении к старой версии API в мета-параметрах (см. раздел Мета-параметры) будет возвращаться параметр `current_version_depricated` со значением `true`. Это говорит о том, что в ближайшие пару месяцев старая версия может стать недоступной.

Пример:

```
https://callapi.comagic.ru/v4.0 \b https://callapi.comagic.ru/v4.0
```

> Максимальное количество поддерживаемых версий - 2  
> Период поддержки устаревшей версии 2 месяца

### Лимиты и ограничения

Лимиты построены по бальной системе, т.е каждый метод имеет свой вес в баллах.

> Баллы списываются только за успешные запросы, т.е в отчете по запросам он помечен как успешный. Успешным считается запрос, если в `result` был возвращен статус `success` = `true` или идентификатор сессии звонка

> Лимиты привязаны к компоненту `Call API Базовый набор` (см. Компоненты) и учитываются в зависимости от стоимости метода в баллах
>  
> Информация о лимитах возвращается во всех ответах в мета-парметрах (см. Мета-параметры), кроме случаев когда лимиты не учитываются;
>  
> *   Если возвращются ошибки валидации JSON или структуры запроса - мнемоника "parse_error" или "invalid_request";
> *   Если возвращается ошибка на вызов метода, который не существует - мнемоника "method_not_found";
> *   Если возвращается ошибка связанная с аутентификацией - "access_token_blocked", "access_token_invalid", "access_token_expired", "auth_error";
> *   Если возвращается ошибка при запросе с IP адреса, который не в белом списке - мнемоника "ip_not_whitelisted".

Информация о лимитах возвращается в мета-параметрах (см. Мета-параметры)

#### Расширение лимитов

Лимитами можно управлять в личном кабинете на странице "Аккаунт" -> "Тарифы и опции".

Название лимитов:

*   Баллов Call API в минуту;
*   Баллов Call API в день;
*   Длина TTS сообщения.

> Отсутствие лимитов в личном кабинете может быть связанно с тем, что имеются ограничения в тарифном плане. В этом случае необходимо связаться с персональным менеджером или службой технической поддержки.

#### Управление безопасностью звонка

Управлять безопасностю звонков (мобильные, междугородние, международные направления) можно в личном кабинете на странице "Аккаунт" -> "Правила и настройки безопасности" -> "API".

> По умолчанию запрещено международное направление, остальные направления разрешены.

Разрешения для направлений звонка разбиты по компонентам Call API (см. Компоненты)

### Мета-параметры

> Возвращаются в ответ на вызов метода. Присутствуют как в ошибочном, так и в успешном ответе

> Параметр `api_version` возвращается только для версий которые `deprecated`.

Описание параметров

| Название параметра | Описание |
| --- | --- |
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
| --- | --- | --- |
| Call API Базовый набор | start.employee_call, start.vnumber_call, start.simple_call, login.user, logout.user, release.call | Базовый пакет Call API |
| Call API Управление вызовами | hold.call, unhold.call, make.call, disconnect.leg, tag.call, record.call, add.coach, list.calls, send.dtmf, block.contact, restore.talk, transfer.talk, list.talk_options, call.talk_option | Пакет позволяет управлять звонками. |
| Call API Информационный вызов | start.informer_call | Пакет, который позволяет обзванивать клиентов и проигрывать им TTS или какой-то предустановленный файл. После окончания информационного сообщения вызов завершается |
| Call API Вызов сценария | start.scenario_call | Пакет, который позволяет совершать вызовы клиенту с использованием одного виртуального номера и набора сценариев. |

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
| --- | --- | --- | --- | --- |
| id | string или number | да | | Уникальный идентификатор запроса к API. <blockquote>Не передается в уведомлениях. Фигурирует только в Статистика и отчеты параметр `Идентификатор запроса`</blockquote> |
| method | string | да | | Вызываемый метод (см. Список методов) |
| jsonrpc | string | да | 2.0 | Номер спецификации JSON-RPC |
| params | object | да | | Содержит тело запроса к API. В зависимости от вызываемого метода тело запроса меняется. |

### Диаграмма состояний звонка

![](https://www.comagic.ru/support/api/call-api/index-doc-call-api_files/state-diagram-call.jpg)

## Аутентификация

### Вход

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>login.user</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
| login | string | да | Логин пользователя |
| password | string | да | Пароль пользователя |

#### Параметры ответа

| Название | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
| access_token | string | да | Ключ сессии аутентификации |
| expire_at | number | да | Timestamp когда выданный токен перестанет быть валидным |

> Время жизни полученного ключа сессии аутентификации после вызова метода `login.user` - 1 час. По истечению времени жизни ключа сессии его необходимо запрашивать заново, т.е. вызывать метод `login.user`.

> Для совершения запросов к API возможно использование постоянного ключа аутентификации, который доступен в личном кабинете (см. Доступ по ключу).

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

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>logout.user</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
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

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>start.employee_call</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

#### Параметры запроса

<table>
    <tbody>
        <tr>
            <th colspan="2">Название</th>
            <th>Тип</th>
            <th>Обязательный</th>
            <th>Допустимые значения</th>
            <th>Описание</th>
        </tr>
        <tr>
            <td colspan="2">access_token</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Ключ сессии аутентификации</td>
        </tr>
        <tr>
            <td colspan="2">first_call</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center">contact, employee</td>
            <td>
                <p>Определяет номер, на который нужно дозвониться в первую очередь:</p>
                <ul>
                    <li><code>employee</code> - сотрудник;</li>
                    <li><code>contact</code> - вызываемая сторона (обычно клиент);</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td colspan="2">switch_at_once</td>
            <td>boolean</td>
            <td class="text-center">нет</td>
            <td class="text-center">true, false</td>
            <td>
                <p>Значение по умолчанию <code>false</code>.</p>
                <p>Если параметр <code>first_call</code> имеет значение <code>employee</code>, заданы параметры <code>employee_message</code>
                    и <code>contact_message</code>, то: 
                </p>
                <ul>
                    <li>Первый звонок совершается сотруднику из параметра <code>employee</code>;</li>
                    <li>Сотрудник после поднятия трубки слушает сообщение до конца и только потом совершается звонок на абонента заданного в параметре <code>contact</code>;</li>
                    <li>Во время дозвона до <code>contact</code>, <code>employee</code> слушает сообщение заданное в параметре <code>media_file_id</code>;</li>
                    <li>После ответа абонента из параметра <code>contact</code>, произойдет разговор с сотрудником из параметра <code>employee</code>, если <code>switch_at_once</code> имеет значение <code>true</code>;</li>
                    <li>После ответа абонента из параметра <code>contact</code>, он дослушает сообщение до конца, если <code>switch_at_once</code> имеет значение <code>false</code> и далее произойдет разговор с сотрудником из параметра <code>employee</code>.</li>
                </ul>
                <p></p>
                <p>Если параметр <code>first_call</code> имеет значение <code>contact</code>, задан параметр <code>contact_message</code> и задан
                    параметр <code>employee_message</code>, то:
                </p>
                <ul>
                    <li>В первую очередь звонок совершается абоненту из параметр <code>contact</code>);</li>
                    <li>Абонент из параметра <code>contact</code> после поднятия трубки начинает слушать сообщение и в это же время совершается звонок на сотрудника из параметра <code>employee</code>;</li>
                    <li>После поднятия трубки сотрудником из параметра <code>employee</code> проигрывание сообщения будет прервано для абонента из параметра <code>contact</code> и далее состоится разговор, если параметр <code>switch_at_once</code> имеет значение <code>true</code>;</li>
                    <li>После поднятия трубки сотрудником из параметра <code>employee</code> сообщение будет проиграно до конца обоим и далее состоится разговор, если параметр <code>switch_at_once</code> имеет значение <code>false</code>;</li>
                    <li>Если у кого-то проигрывание сообщения закончилось раньше, то он слушает сообщение, заданное в параметре <code>media_file_id</code>.</li>
                </ul>
                <p></p>
                <blockquote class="bs-callout bs-callout-info">Если в параметре <code>contact_message type</code> имеет значение <code>tts</code>, то параметр <code>switch_at_once=true</code> не работает</blockquote>
            </td>
        </tr>
        <tr>
            <td colspan="2">early_switching</td>
            <td>boolean</td>
            <td class="text-center">нет</td>
            <td class="text-center">true, false</td>
            <td>
                <p>Значение по умолчанию <code>false</code>.</p>
                <p>Если параметр имеет значение <code>true</code>, то сотрудник при дозвоне до абонента будет слышать то, что происходит в абонентской линии.</p>
                <p>К примеру, оператор ждет дозвона до абонента, а абонент недоступен и у него сработала голосовая почта, то при активации параметра <code>early_switching</code> = <code>true</code>, оператор сможет услышать сообщение о голосовой почте абонента. Если параметр <code>early_switching</code> = <code>false</code>, то оператор будет слушать музыку, которая задана в параметре "media_file_id" </p>
                <blockquote class="bs-callout bs-callout-info">Параметр может иметь значение <code>true</code> если только параметр <code>first_call</code> имеет значение <code>employee</code> и параметр <code>switch_at_once</code> имеет значение значение <code>true</code>. В противном случае будет ошибка "-32602 invalid_parameters_combination The combination of parameters is not permitted" (см. раздел коды ошибок). </blockquote>
            </td>
        </tr>
        <tr>
            <td colspan="2">media_file_id</td>
            <td>number</td>
            <td class="text-center">нет</td>
            <td class="text-center"></td>
            <td>
                <p>Значение по умолчанию - системная мелодия "Музыка переадресации" (<code>dialing_music</code>).</p>
                <p>Задаёт идентификатор звукового файла для музыки переадресации. Файл может быть как системным, так
                    и пользовательским. Получить список системных или пользовательских файлов можно с помощью 
                    REST API - Получение списка пользовательских файлов, 
                    Получение списка системных файлов.
                </p>
                <blockquote class="bs-callout bs-callout-info">Проигрывается всегда тому плечу, для которого не задан один из параметров <code>contact_message</code> или <code>employee_message</code></blockquote>
                <p></p>
            </td>
        </tr>
        <tr>
            <td colspan="2">virtual_phone_number</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Виртуальный номер, арендуемый клиентом. Формат номера должен соответствовать международному
                    стандарту E.164 (например, <code>74993720692</code>). Всегда используется в качестве номера звонящего при звонке
                    на номер, указанный в параметре <code>contact</code>. Используется в качестве номера звонящего при звонке на
                    номер, указанный в параметре <code>employee</code>, если параметр <code>show_virtual_phone_number</code> имеет значение
                    <code>true</code>.
                    Виртуальные номера можно получить с помощью REST API метода - Получение списка виртуальных номеров
                </p>
                <blockquote class="bs-callout bs-callout-info"> В качестве виртуального номера запрещено использовать 800-е номера</blockquote>
            </td>
        </tr>
        <tr>
            <td colspan="2">show_virtual_phone_number</td>
            <td>boolean</td>
            <td class="text-center">нет</td>
            <td class="text-center">true, false</td>
            <td>
                <p>Значение по умолчанию <code>true</code>.</p>
                <p>Показывать ли виртуальный номер, заданный параметром <code>virtual_phone_number</code> в качестве номера звонящего абонента для сотрудника заданного в параметре <code>employee</code>.</p>
            </td>
        </tr>
        <tr>
            <td colspan="2">contact</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Номер абонента на который совершается вызов. Формат номера должен соответствовать
                    международному стандарту E.164 (например, <code>79091234567</code>). В качестве номера может быть так же указан SIP номер сотрудника.
                </p>
                <blockquote class="bs-callout bs-callout-info">Внутренние номера сотрудников не поддерживаются.</blockquote>
            </td>
        </tr>
        <tr>
            <td colspan="2">external_id</td>
            <td>string</td>
            <td class="text-center">нет</td>
            <td class="text-center"></td>
            <td>Уникальный идентификатор, который может быть использован для связи события звонка с внешней системой.</td>
        </tr>
        <tr>
            <td colspan="2">dtmf_string</td>
            <td>string</td>
            <td class="text-center">нет</td>
            <td class="text-center">0-9, *, #</td>
            <td>Задаёт DTMF, который будет отправлен абоненту, заданному в параметре <code>contact</code>. С
                помощью символа <code>.</code> = '1 секунда' можно задать таймаут по истечению которого символ DTMF будет отправлен. Пример: <code>.12.1..4</code>, т.е через 1 секунду
                будет отправлена цифра 12, далее через 1 секунду будет отправлена цифра 1 и через 2 секунды цифра 4.
            </td>
        </tr>
        <tr>
            <td colspan="2">direction</td>
            <td>string</td>
            <td class="text-center">нет</td>
            <td class="text-center">in, out</td>
            <td>Значение по умолчанию <code>out</code>. <br>
                Определяет направление звонка <code>in</code> - Входящий звонок, <code>out</code> - Исходящий звонок.
            </td>
        </tr>
        <tr>
            <td class="color-bg" colspan="6" align="center">Сотрудник с которым будет соединён абонент из параметра <code>contact</code></td>
        </tr>
        <tr>
            <td colspan="2">employee</td>
            <td>object</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Сотрудник с которым будет соединён абонент, указанный в параметре <code>contact</code>.</td>
        </tr>
        <tr>
            <td width="15"></td>
            <td>id</td>
            <td>number</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                Уникальный идентификатор сотрудника. Данный идентификатор можно получить с помощью REST API - Получение информации о сотруднике
                <blockquote class="bs-callout bs-callout-info">Если не указан параметр <code>phone_number</code>, то будет совершен последовательный обзвон всех активных номеров сотрудника.</blockquote>
            </td>
        </tr>
        <tr>
            <td></td>
            <td>phone_number</td>
            <td>string</td>
            <td class="text-center">нет</td>
            <td class="text-center"></td>
            <td>
                <p>Задаёт номер сотрудника с которым будет соединен абонент указанный в параметре <code>contact</code>.
                    В качестве номера может быть указан SIP-номер, внутренний номер, номер в формате E.164. Номер должен
                    иметь статус <code>Активен</code> в личном кабинете и принадлежать сотруднику заданному в параметре <code>id</code>.
                </p>
            </td>
        </tr>
        <tr>
            <td class="color-bg" colspan="6" align="center">Сообщение для проигрывания абоненту, который задан в параметре <code>contact</code></td>
        </tr>
        <tr>
            <td colspan="2">contact_message</td>
            <td>object</td>
            <td class="text-center">нет</td>
            <td class="text-center"></td>
            <td>
                <p>Определяет параметры сообщения, которое необходимо проиграть абоненту заданному в параметре <code>contact</code>.</p>
                <blockquote class="bs-callout bs-callout-info"> После окончания проигрывания сообщения, будет проигрываться покругу сообщение из параметра <code>media_file_id</code></blockquote>
            </td>
        </tr>
        <tr>
            <td></td>
            <td>type</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center">media, tts</td>
            <td>
                <p>Определяет тип сообщения. <code>media</code> - файл или <code>tts</code> - текст для услуги синтеза речи Text-to-Speech.</p>
            </td>
        </tr>
        <tr>
            <td></td>
            <td>value</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Если поле <code>type</code> имеет значение <code>media</code>, то в качестве значения принимается идентификатор файла для
                    проигрывания. Файл для проигрывания может быть системным или пользовательским. Идентификатор
                    файла для проигрывания можно получить с помощью REST API - Получение списка пользовательских файлов, Получение списка системных файлов
                </p>
                <p>Если поле <code>type</code> имеет значение <code>tts</code>, то в качестве значения принимается текст для синтезирования его в
                    голосовое сообщение.
                </p>
                <blockquote class="bs-callout bs-callout-info"> Длина TTS сообщения регулируется тарифным планом и установленным лимитом.</blockquote>
            </td>
        </tr>
        <tr>
            <td class="color-bg" colspan="6" align="center">Сообщение для проигрывания абоненту, который задан в параметре <code>employee</code></td>
        </tr>
        <tr>
            <td colspan="2">employee_message</td>
            <td>object</td>
            <td class="text-center">нет</td>
            <td class="text-center"></td>
            <td>
                <p>Определяет параметры сообщения, которое необходимо проиграть абоненту заданному в параметре <code>employee</code>.</p>
                <blockquote class="bs-callout bs-callout-info"> После окончания проигрывания сообщения, будет проигрываться покругу сообщение из параметра <code>media_file_id</code></blockquote>
            </td>
        </tr>
        <tr>
            <td></td>
            <td>type</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center">media, tts</td>
            <td>
                <p>Определяет тип сообщения. <code>media</code> - файл или <code>tts</code> - текст для услуги синтеза речи Text-to-Speech.</p>
            </td>
        </tr>
        <tr>
            <td></td>
            <td>value</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Если поле <code>type</code> имеет значение <code>media</code>, то в качестве значения принимается идентификатор файла для
                    проигрывания. Файл для проигрывания может быть системным или пользовательским. Идентификатор
                    файла для проигрывания можно получить с помощью REST API - Получение списка пользовательских файлов, Получение списка системных файлов
                </p>
                <p>Если поле <code>type</code> имеет значение <code>tts</code>, то в качестве значения принимается текст для синтезирования его в
                    голосовое сообщение.
                </p>
                <blockquote class="bs-callout bs-callout-info"> Длина TTS сообщения регулируется тарифным планом и установленным лимитом.</blockquote>
            </td>
        </tr>
    </tbody>
</table>

#### Параметры ответа

| Название | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
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
| --- | --- | --- | --- |
| The maximum length of Text-to-Speech message is {`tts_message_max_length`}. The length of your message is {`sent_tts_message_length`} | -32602 | `tts_text_exceeded` | Длина сообщения превысила допустимое ограничение, установленное тарифным планом |
| The media file with id {`media_file_id`} not found | -32602 | `media_file_not_found` |
| Virtual phone number {`virtual_phone_number`} not found. It is not your virtual phone number. | -32007 | `virtual_phone_number_not_found` | Если используется виртуальный номер не принадлежащий клиенту или 800-й номер |
| Employee with id {`employee_id`} not found. It is not your employee. | -32602 | `employee_not_found` |
| The phone number does not exist or inactive | -32602 | `no_active_phone_number` | У сотрудника нет активных номеров в настройках сотрудника |
| Parameter `contact` can not contain own virtual phone number | -32602 | `own_virtual_phone_number_not_allowed` | Звонок на собственный виртуальный номер запрещён |
| The contact {`contact`} has been found in the blacklist | -32002 | `contact_in_blacklist` |
| The character encoding must be UTF-8 | -32602 | `character_encoding_not_allowed` |

См. также раздел Список ошибок общих для всех методов

### Вызов по сценарию

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>start.scenario_call</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

#### Параметры запроса

<table>
    <tbody>
        <tr>
            <th colspan="2">Название</th>
            <th>Тип</th>
            <th>Обязательный</th>
            <th>Допустимые значения</th>
            <th>Описание</th>
        </tr>
        <tr>
            <td colspan="2">access_token</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Ключ сессии аутентификации</td>
        </tr>
        <tr>
            <td colspan="2">virtual_phone_number</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Виртуальный номер, арендуемый клиентом. Формат номера должен соответствовать международному
                    стандарту E.164 (например, <code>74993720692</code>). Всегда используется в качестве номера звонящего при звонке
                    на номер, указанный в параметре <code>contact</code>. Используется в качестве номера звонящего при звонке на
                    сотрудника, если в сценарии в операции "Переадресация" установлена настройка "Показывать при звонке" в значение "Номер услуги".
                    Виртуальные номера можно получить с помощью REST API метода - Получение списка виртуальных номеров
                </p>
                <blockquote class="bs-callout bs-callout-info"> В качестве виртуального номера запрещено использовать 800-е номера</blockquote>
            </td>
        </tr>
        <tr>
            <td colspan="2">external_id</td>
            <td>string</td>
            <td class="text-center">нет</td>
            <td class="text-center"></td>
            <td>Уникальный идентификатор, который может быть использован для связи события звонка с внешней системой.</td>
        </tr>
        <tr>
            <td colspan="2">dtmf_string</td>
            <td>string</td>
            <td class="text-center">нет</td>
            <td class="text-center">0-9, *, #</td>
            <td>Задаёт DTMF, который будет отправлен абоненту, заданному в параметре <code>contact</code>. С
                помощью символа <code>.</code> = '1 секунда' можно задать таймаут по истечению которого символ DTMF будет отправлен. Пример: <code>.12.1..4</code>, т.е через 1 секунду
                будет отправлена цифра 12, далее через 1 секунду будет отправлена цифра 1 и через 2 секунды цифра 4.
            </td>
        </tr>
        <tr>
            <td colspan="2">contact</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Номер абонента на который совершается вызов. Формат номера должен соответствовать
                    международному стандарту E.164 (например, <code>79091234567</code>). В качестве номера может быть так же указан SIP номер сотрудника.
                </p>
                <blockquote class="bs-callout bs-callout-info">Если не задан параметр <code>contact_message</code>, то абоненту будет проигрываться системная мелодия - <code>Музыка переадресации (dialing_music)</code></blockquote>
                <blockquote class="bs-callout bs-callout-info">Внутренние номера сотрудников не поддерживаются.</blockquote>
            </td>
        </tr>
        <tr>
            <td colspan="2">first_call</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center">contact, employee</td>
            <td>
                <p>Определяет номер, на который нужно дозвониться в первую очередь:</p>
                <ul>
                    <li><code>employee</code> - сотрудник;</li>
                    <li><code>contact</code> - вызываемая сторона (обычно клиент);</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td colspan="2">switch_at_once</td>
            <td>boolean</td>
            <td class="text-center">нет</td>
            <td class="text-center">true, false</td>
            <td>
                <p>Значение по умолчанию <code>false</code>.</p>
                <p>Если параметр <code>first_call</code> имеет значение <code>employee</code>, задано сообщение сотруднику в сценарии
                    и задан параметр <code>contact_message</code>, то: 
                </p>
                <ul>
                    <li>Первый звонок совершается сотруднику из сценария;</li>
                    <li>Сотрудник после поднятия трубки слушает сообщение до конца и только потом совершается звонок на абонента заданного в параметре <code>contact</code>;</li>
                    <li>Во время дозвона до <code>contact</code>, сотрудник слушает системную мелодию - <code>Музыка переадресации (dialing_music)</code>;</li>
                    <li>После ответа абонента из параметра <code>contact</code>, произойдет разговор с сотрудником, если <code>switch_at_once</code> имеет значение <code>true</code>;</li>
                    <li>После ответа абонента из параметра <code>contact</code>, он дослушает сообщение до конца, если <code>switch_at_once</code> имеет значение <code>false</code> и далее произойдет разговор с сотрудником.</li>
                </ul>
                <p></p>
                <p>Если параметр <code>first_call</code> имеет значение <code>contact</code>, задан параметр <code>contact_message</code> и задано
                    сообщение сотруднику в сценарии, то:
                </p>
                <ul>
                    <li>В первую очередь звонок совершается абоненту из параметр <code>contact</code>);</li>
                    <li>Абонент из параметра <code>contact</code> после поднятия трубки начинает слушать сообщение и в это же время совершается звонок на сотрудника из сценария;</li>
                    <li>После поднятия трубки сотрудником проигрывание сообщения будет прервано для абонента из параметра <code>contact</code> и далее состоится разговор, если параметр <code>switch_at_once</code> имеет значение <code>true</code>;</li>
                    <li>После поднятия трубки сотрудником сообщение будет проиграно до конца обоим и далее состоится разговор, если параметр <code>switch_at_once</code> имеет значение <code>false</code>;</li>
                    <li>Если у кого-то проигрывание сообщения закончилось раньше, то он слушает системную мелодию - <code>Музыка переадресации (dialing_music)</code>.</li>
                </ul>
                <p></p>
                <blockquote class="bs-callout bs-callout-info">Если в параметре <code>contact_message type</code> имеет значение <code>tts</code>, то параметр <code>switch_at_once=true</code> не работает</blockquote>
            </td>
        </tr>
        <tr>
            <td colspan="2">scenario_id</td>
            <td>number</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Уникальный идентификатор сценария, который может быть получен с 
                помощью REST API - Получение списка сценариев.
            </td>
        </tr>
        <tr>
            <td colspan="2">direction</td>
            <td>string</td>
            <td class="text-center">нет</td>
            <td class="text-center">in, out</td>
            <td>Значение по умолчанию <code>out</code>. <br>
                Определяет направление звонка <code>in</code> - Входящий звонок, <code>out</code> - Исходящий звонок.
            </td>
        </tr>
        <tr>
            <td class="color-bg" colspan="6" align="center">Сообщение для проигрывания абоненту, который задан в параметре <code>contact</code></td>
        </tr>
        <tr>
            <td colspan="2">contact_message</td>
            <td>object</td>
            <td class="text-center">нет</td>
            <td class="text-center"></td>
            <td>
                <p>Определяет параметры сообщения, которое необходимо проиграть абоненту заданному в параметре <code>contact</code>.</p>
                <blockquote class="bs-callout bs-callout-info"> После окончания проигрывания сообщения, будет проигрываться покругу системная мелодия - <code>Музыка переадресации (dialing_music)</code></blockquote>
            </td>
        </tr>
        <tr>
            <td></td>
            <td>type</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center">media, tts</td>
            <td>
                <p>Определяет тип сообщения. <code>media</code> - файл или <code>tts</code> - текст для услуги синтеза речи Text-to-Speech.</p>
            </td>
        </tr>
        <tr>
            <td></td>
            <td>value</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Если поле <code>type</code> имеет значение <code>media</code>, то в качестве значения принимается идентификатор файла для
                    проигрывания. Файл для проигрывания может быть системным или пользовательским. Идентификатор
                    файла для проигрывания можно получить с помощью REST API - Получение списка пользовательских файлов, Получение списка системных файлов
                </p>
                <p>Если поле <code>type</code> имеет значение <code>tts</code>, то в качестве значения принимается текст для синтезирования его в
                    голосовое сообщение.
                </p>
                <blockquote class="bs-callout bs-callout-info"> Длина TTS сообщения регулируется тарифным планом и установленным лимитом.</blockquote>
            </td>
        </tr>
    </tbody>
</table>

#### Параметры ответа

| Название | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
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
| --- | --- | --- | --- |
| The maximum length of Text-to-Speech message is {`tts_message_max_length`}. The length of your message is {`sent_tts_message_length`} | -32602 | `tts_text_exceeded` | Длина сообщения превысила допустимое ограничение, установленное тарифным планом |
| The media file with id {`media_file_id`} not found | -32602 | `media_file_not_found` |
| Virtual phone number {`virtual_phone_number`} not found. It is not your virtual phone number. | -32007 | `virtual_phone_number_not_found` | Если используется виртуальный номер не принадлежащий клиенту или 800-й номер |
| Scenario with id {`scenario_id`} not found | -32602 | `scenario_not_found` |
| Parameter `contact` can not contain own virtual phone number | -32602 | `own_virtual_phone_number_not_allowed` | Звонок на собственный виртуальный номер запрещён |
| The contact {`contact`} has been found in the blacklist | -32602 | `contact_in_blacklist` |
| The character encoding must be UTF-8 | -32602 | `character_encoding_not_allowed` |

См. также раздел Список ошибок общих для всех методов

### Вызов на виртуальный номер

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>start.vnumber_call</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

#### Параметры запроса

<table>
    <tbody>
        <tr>
            <th colspan="2">Название</th>
            <th>Тип</th>
            <th>Обязательный</th>
            <th>Допустимые значения</th>
            <th>Описание</th>
        </tr>
        <tr>
            <td colspan="2">access_token</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Ключ сессии аутентификации</td>
        </tr>
        <tr>
            <td colspan="2">virtual_phone_number</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Виртуальный номер, арендуемый клиентом. Формат номера должен соответствовать международному
                    стандарту E.164 (например, <code>74993720692</code>). Всегда используется в качестве номера звонящего при звонке
                    на номер, указанный в параметре <code>contact</code>. Используется в качестве номера звонящего при звонке на
                    сотрудника, если в сценарии в операции "Переадресация" установлена настройка "Показывать при звонке" в значение "Номер услуги". 
                    Виртуальные номера можно получить с помощью REST API метода - Получение списка виртуальных номеров
                </p>
                <blockquote class="bs-callout bs-callout-info"> В качестве виртуального номера запрещено использовать 800-е номера</blockquote>
            </td>
        </tr>
        <tr>
            <td colspan="2">external_id</td>
            <td>string</td>
            <td class="text-center">нет</td>
            <td class="text-center"></td>
            <td>Уникальный идентификатор, который может быть использован для связи события звонка с внешней системой.</td>
        </tr>
        <tr>
            <td colspan="2">dtmf_string</td>
            <td>string</td>
            <td class="text-center">нет</td>
            <td class="text-center">0-9, *, #</td>
            <td>Задаёт DTMF, который будет отправлен абоненту, заданному в параметре <code>contact</code>. С
                помощью символа <code>.</code> = '1 секунда' можно задать таймаут по истечению которого символ DTMF будет отправлен. Пример: <code>.12.1..4</code>, т.е через 1 секунду
                будет отправлена цифра 12, далее через 1 секунду будет отправлена цифра 1 и через 2 секунды цифра 4.
            </td>
        </tr>
        <tr>
            <td colspan="2">direction</td>
            <td>string</td>
            <td class="text-center">нет</td>
            <td class="text-center">in, out</td>
            <td>Значение по умолчанию <code>out</code>. <br>
                Определяет направление звонка <code>in</code> - Входящий звонок, <code>out</code> - Исходящий звонок.
            </td>
        </tr>
        <tr>
            <td colspan="2">contact</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Номер абонента на который совершается вызов. Формат номера должен соответствовать
                    международному стандарту E.164 (например, <code>79091234567</code>). В качестве номера может быть так же указан SIP номер сотрудника.
                </p>
                <blockquote class="bs-callout bs-callout-info">Если не задан параметр <code>contact_message</code>, то абоненту будет проигрываться системная мелодия - <code>Музыка переадресации (dialing_music)</code></blockquote>
                <blockquote class="bs-callout bs-callout-info">Внутренние номера сотрудников не поддерживаются.</blockquote>
            </td>
        </tr>
        <tr>
            <td colspan="2">first_call</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center">contact, employee</td>
            <td>
                <p>Определяет номер, на который нужно дозвониться в первую очередь:</p>
                <ul>
                    <li><code>employee</code> - сотрудник;</li>
                    <li><code>contact</code> - вызываемая сторона (обычно клиент);</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td colspan="2">switch_at_once</td>
            <td>boolean</td>
            <td class="text-center">нет</td>
            <td class="text-center">true, false</td>
            <td>
                <p>Значение по умолчанию <code>false</code>.</p>
                <p>Если параметр <code>first_call</code> имеет значение <code>employee</code>, задано сообщение сотруднику в сценарии
                    и задан параметр <code>contact_message</code>, то: 
                </p>
                <ul>
                    <li>Первый звонок совершается сотруднику из сценария;</li>
                    <li>Сотрудник после поднятия трубки слушает сообщение до конца и только потом совершается звонок на абонента заданного в параметре <code>contact</code>;</li>
                    <li>Во время дозвона до <code>contact</code>, сотрудник слушает системную мелодию - <code>Музыка переадресации (dialing_music)</code>;</li>
                    <li>После ответа абонента из параметра <code>contact</code>, произойдет разговор с сотрудником, если <code>switch_at_once</code> имеет значение <code>true</code>;</li>
                    <li>После ответа абонента из параметра <code>contact</code>, он дослушает сообщение до конца, если <code>switch_at_once</code> имеет значение <code>false</code> и далее произойдет разговор с сотрудником.</li>
                </ul>
                <p></p>
                <p>Если параметр <code>first_call</code> имеет значение <code>contact</code>, задан параметр <code>contact_message</code> и задано
                    сообщение сотруднику в сценарии, то:
                </p>
                <ul>
                    <li>В первую очередь звонок совершается абоненту из параметр <code>contact</code>);</li>
                    <li>Абонент из параметра <code>contact</code> после поднятия трубки начинает слушать сообщение и в это же время совершается звонок на сотрудника из сценария;</li>
                    <li>После поднятия трубки сотрудником проигрывание сообщения будет прервано для абонента из параметра <code>contact</code> и далее состоится разговор, если параметр <code>switch_at_once</code> имеет значение <code>true</code>;</li>
                    <li>После поднятия трубки сотрудником сообщение будет проиграно до конца обоим и далее состоится разговор, если параметр <code>switch_at_once</code> имеет значение <code>false</code>;</li>
                    <li>Если у кого-то проигрывание сообщения закончилось раньше, то он слушает системную мелодию - <code>Музыка переадресации (dialing_music)</code>.</li>
                </ul>
                <p></p>
                <blockquote class="bs-callout bs-callout-info">Если в параметре <code>contact_message type</code> имеет значение <code>tts</code>, то параметр <code>switch_at_once=true</code> не работает</blockquote>
            </td>
        </tr>
        <tr>
            <td class="color-bg" colspan="6 align="center"">Сообщение для проигрывания абоненту, который задан в параметре <code>contact</code></td>
        </tr>
        <tr>
            <td colspan="2">contact_message</td>
            <td>object</td>
            <td class="text-center">нет</td>
            <td class="text-center"></td>
            <td>
                <p>Определяет параметры сообщения, которое необходимо проиграть абоненту заданному в параметре <code>contact</code>.</p>
                <blockquote class="bs-callout bs-callout-info"> После окончания проигрывания сообщения, будет проигрываться покругу системная мелодия - <code>Музыка переадресации (dialing_music)</code></blockquote>
            </td>
        </tr>
        <tr>
            <td></td>
            <td>type</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center">media, tts</td>
            <td>
                <p>Определяет тип сообщения. <code>media</code> - файл или <code>tts</code> - текст для услуги синтеза речи Text-to-Speech.</p>
            </td>
        </tr>
        <tr>
            <td></td>
            <td>value</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Если поле <code>type</code> имеет значение <code>media</code>, то в качестве значения принимается идентификатор файла для
                    проигрывания. Файл для проигрывания может быть системным или пользовательским. Идентификатор
                    файла для проигрывания можно получить с помощью REST API - Получение списка пользовательских файлов, Получение списка системных файлов
                </p>
                <p>Если поле <code>type</code> имеет значение <code>tts</code>, то в качестве значения принимается текст для синтезирования его в
                    голосовое сообщение.
                </p>
                <blockquote class="bs-callout bs-callout-info"> Длина TTS сообщения регулируется тарифным планом и установленным лимитом.</blockquote>
            </td>
        </tr>
    </tbody>
</table>

#### Параметры ответа

| Название | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
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
| --- | --- | --- | --- |
| The maximum length of Text-to-Speech message is {`tts_message_max_length`}. The length of your message is {`sent_tts_message_length`} | -32602 | `tts_text_exceeded` | Длина сообщения превысила допустимое ограничение, установленное тарифным планом |
| The media file with id {`media_file_id`} not found | -32602 | `media_file_not_found` |
| Virtual phone number {`virtual_phone_number`} not found. It is not your virtual phone number. | -32007 | `virtual_phone_number_not_found` | Если используется виртуальный номер не принадлежащий клиенту или 800-й номер |
| Virtual phone number does not have a scenario | -32007 | `scenario_not_found` |
| Parameter `contact` can not contain own virtual phone number | -32602 | `own_virtual_phone_number_not_allowed` | Звонок на собственный виртуальный номер запрещён |
| The contact {`contact`} has been found in the blacklist | -32602 | `contact_in_blacklist` |
| The character encoding must be UTF-8 | -32602 | `character_encoding_not_allowed` |

См. также раздел Список ошибок общих для всех методов

### Информационный вызов

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>start.informer_call</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

> После окончания проигрывания сообщения абоненту вызов завершается.

#### Параметры запроса

<table>
    <tbody>
        <tr>
            <th colspan="2">Название</th>
            <th>Тип</th>
            <th>Обязательный</th>
            <th>Допустимые значения</th>
            <th>Описание</th>
        </tr>
        <tr>
            <td colspan="2">access_token</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Ключ сессии аутентификации</td>
        </tr>
        <tr>
            <td colspan="2">virtual_phone_number</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Виртуальный номер, арендуемый клиентом. Формат номера должен соответствовать международному
                    стандарту E.164 (например, <code>74993720692</code>). Всегда используется в качестве номера звонящего при звонке
                    на номер, указанный в параметре <code>contact</code>. Виртуальные номера можно получить с помощью REST 
                    API метода - Получение списка виртуальных номеров
                </p>
                <blockquote class="bs-callout bs-callout-info"> В качестве виртуального номера запрещено использовать 800-е номера</blockquote>
            </td>
        </tr>
        <tr>
            <td colspan="2">external_id</td>
            <td>string</td>
            <td class="text-center">нет</td>
            <td class="text-center"></td>
            <td>Уникальный идентификатор, который может быть использован для связи события звонка с внешней системой.</td>
        </tr>
        <tr>
            <td colspan="2">dtmf_string</td>
            <td>string</td>
            <td class="text-center">нет</td>
            <td class="text-center">0-9, *, #</td>
            <td>Задаёт DTMF, который будет отправлен абоненту, заданному в параметре <code>contact</code>. С
                помощью символа <code>.</code> = '1 секунда' можно задать таймаут по истечению которого символ DTMF будет отправлен. Пример: <code>.12.1..4</code>, т.е через 1 секунду
                будет отправлена цифра 12, далее через 1 секунду будет отправлена цифра 1 и через 2 секунды цифра 4.
            </td>
        </tr>
        <tr>
            <td colspan="2">direction</td>
            <td>string</td>
            <td class="text-center">нет</td>
            <td class="text-center">in, out</td>
            <td>Значение по умолчанию <code>out</code>. <br>
                Определяет направление звонка <code>in</code> - Входящий звонок, <code>out</code> - Исходящий звонок.
            </td>
        </tr>
        <tr>
            <td colspan="2">contact</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Номер абонента на который совершается вызов. Формат номера должен соответствовать
                    международному стандарту E.164 (например, <code>79091234567</code>). В качестве номера может быть так же указан SIP номер сотрудника.
                </p>
                <blockquote class="bs-callout bs-callout-info">Внутренние номера сотрудников не поддерживаются.</blockquote>
            </td>
        </tr>
        <tr>
            <td colspan="2">dialing_timeout</td>
            <td>number</td>
            <td class="text-center">нет</td>
            <td class="text-center">до 120 секунд</td>
            <td>Значение по умолчанию <code>30</code>. Время ожидания ответа от номера, заданного в параметре <code>contact</code>, 
                если ответ не был получен, то вызов завершается. Время задаётся в секундах.
            </td>
        </tr>
        <tr>
            <td class="color-bg" colspan="6" align="center">Сообщение для проигрывания абоненту, который задан в параметре <code>contact</code></td>
        </tr>
        <tr>
            <td colspan="2">contact_message</td>
            <td>object</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Определяет параметры сообщения, которое необходимо проиграть абоненту заданному в параметре <code>contact</code>.</p>
            </td>
        </tr>
        <tr>
            <td></td>
            <td>type</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center">media, tts</td>
            <td>
                <p>Определяет тип сообщения. <code>media</code> - файл или <code>tts</code> - текст для услуги синтеза речи Text-to-Speech.</p>
            </td>
        </tr>
        <tr>
            <td></td>
            <td>value</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Если поле <code>type</code> имеет значение <code>media</code>, то в качестве значения принимается идентификатор файла для
                    проигрывания. Файл для проигрывания может быть системным или пользовательским. Идентификатор
                    файла для проигрывания можно получить с помощью REST API - Получение списка пользовательских файлов, Получение списка системных файлов
                </p>
                <p>Если поле <code>type</code> имеет значение <code>tts</code>, то в качестве значения принимается текст для синтезирования его в
                    голосовое сообщение.
                </p>
                <blockquote class="bs-callout bs-callout-info"> Длина TTS сообщения регулируется тарифным планом и установленным лимитом.</blockquote>
            </td>
        </tr>
    </tbody>
</table>

#### Параметры ответа

| Название | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
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
| --- | --- | --- | --- |
| The maximum length of Text-to-Speech message is {`tts_message_max_length`}. The length of your message is {`sent_tts_message_length`} | -32602 | `tts_text_exceeded` | Длина сообщения превысила допустимое ограничение, установленное тарифным планом |
| The media file with id {`media_file_id`} not found | -32602 | `media_file_not_found` |
| Virtual phone number {`virtual_phone_number`} not found. It is not your virtual phone number. | -32007 | `virtual_phone_number_not_found` | Если используется виртуальный номер не принадлежащий клиенту или 800-й номер |
| Parameter `contact` can not contain own virtual phone number | -32602 | `own_virtual_phone_number_not_allowed` | Звонок на собственный виртуальный номер запрещён |
| The contact {`contact`} has been found in the blacklist | -32602 | `contact_in_blacklist` |
| The character encoding must be UTF-8 | -32602 | `character_encoding_not_allowed` |

См. также раздел Список ошибок общих для всех методов

### Простой звонок

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>start.simple_call</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

#### Параметры запроса

<table>
    <tbody>
        <tr>
            <th colspan="2">Название</th>
            <th>Тип</th>
            <th>Обязательный</th>
            <th>Допустимые значения</th>
            <th>Описание</th>
        </tr>
        <tr>
            <td colspan="2">access_token</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Ключ сессии аутентификации</td>
        </tr>
        <tr>
            <td colspan="2">first_call</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center">contact, operator</td>
            <td>
                <p>Определяет номер, на который нужно дозвониться в первую очередь:</p>
                <ul>
                    <li><code>operator</code> - оператор, которому будет доступно управление звонком через опции разговора;</li>
                    <li><code>contact</code> - вызываемая сторона (обычно клиент);</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td colspan="2">switch_at_once</td>
            <td>boolean</td>
            <td class="text-center">нет</td>
            <td class="text-center">true, false</td>
            <td>
                <p>Значение по умолчанию <code>false</code>.</p>
                Если параметр <code>first_call</code> имеет значение <code>operator</code> и задан параметр <code>operator_message</code>, то звонок совершается оператору, оператор после поднятия трубки слушает сообщение до конца, потом совершается звонок на <code>contact</code> и если параметр <code>switch_at_once</code> имеет значение <code>true</code> и задана параметр <code>contact_message</code>, то для плеча <code>contact</code> проигрывание
                <p></p>
                сообщения будет прервано. Во время дозвона до <code>contact</code> <code>operator</code> слушает сообщение заданное в параметре <code>media_file_id</code>. Соответственно, если <code>switch_at_once</code> имеет значение <code>false</code> и задан параметр <code>contact_message</code>, то для плеча <code>contact</code> сообщение будет проиграно до конца.
                Если параметр <code>first_call</code> имеет значение <code>contact</code> и задан параметр <code>contact_message</code>, то звонок совершается клиенту, клиент после поднятия трубки начинает слушать сообщение и в это же время совершается звонок на <code>operator</code> и если параметр <code>switch_at_once</code> имеет значение <code>true</code> и задан параметр <code>operator_message</code>, то после поднятия трубки на <code>operator</code> проигрывание сообщений будет прервано для <code>contact</code> и <code>operator</code>.Соответственно, если <code>switch_at_once</code> имеет значение <code>false</code>, то для плеча <code>contact</code> и <code>operator</code> сообщение будет проиграно до конца. Если у кого-то плеча проигрывание сообщения закончилось раньше, то он слушает промт, заданный в параметре <code>media_file_id</code>.
            </td>
        </tr>
        <tr>
            <td colspan="2">early_switching</td>
            <td>boolean</td>
            <td class="text-center">нет</td>
            <td class="text-center">true, false</td>
            <td>
                <p>Значение по умолчанию <code>false</code>.</p>
                <p>Если параметр имеет значение <code>true</code>, то сотрудник при дозвоне до абонента будет слышать то, что происходит в абонентской линии.</p>
                <p>К примеру, оператор ждет дозвона до абонента, а абонент недоступен и у него сработала голосовая почта, то при активации параметра <code>early_switching</code> = <code>true</code>, оператор сможет услышать сообщение о голосовой почте абонента. Если параметр <code>early_switching</code> = <code>false</code>, то оператор будет слушать музыку, которая задана в параметре "media_file_id" </p>
                <blockquote class="bs-callout bs-callout-info">Параметр может иметь значение <code>true</code> если только параметр <code>first_call</code> имеет значение <code>operator</code> и параметр <code>switch_at_once</code> имеет значение значение <code>true</code>. В противном случае будет ошибка "-32602 invalid_parameters_combination The combination of parameters is not permitted" (см. раздел коды ошибок). </blockquote>
            </td>
        </tr>
        <tr>
            <td colspan="2">media_file_id</td>
            <td>number</td>
            <td class="text-center">нет</td>
            <td class="text-center"></td>
            <td>
                <p>Значение по умолчанию - системная мелодия "Музыка переадресации" (<code>dialing_music</code>).</p>
                <p>Задаёт идентификатор звукового файла для музыки переадресации. Файл может быть как системным, так
                    и пользовательским. Получить список системных или пользовательских файлов можно с помощью 
                    REST API - Получение списка пользовательских файлов, 
                    Получение списка системных файлов.
                </p>
                <blockquote class="bs-callout bs-callout-info">Проигрывается всегда тому плечу, для которого не задан один из параметров <code>contact_message</code> или <code>operator_message</code></blockquote>
                <p></p>
            </td>
        </tr>
        <tr>
            <td colspan="2">virtual_phone_number</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Виртуальный номер, арендуемый клиентом. Формат номера должен соответствовать международному
                    стандарту E.164 (например, <code>74993720692</code>). Всегда используется в качестве номера звонящего при звонке
                    на номер, указанный в параметре <code>contact</code>. Используется в качестве номера звонящего при звонке на
                    номер, указанный в параметре <code>operator</code>, если параметр <code>show_virtual_phone_number</code> имеет значение
                    <code>true</code>.
                    Виртуальные номера можно получить с помощью REST API метода - Получение списка виртуальных номеров
                </p>
                <blockquote class="bs-callout bs-callout-info"> В качестве виртуального номера запрещено использовать 800-е номера</blockquote>
            </td>
        </tr>
        <tr>
            <td colspan="2">show_virtual_phone_number</td>
            <td>boolean</td>
            <td class="text-center">нет</td>
            <td class="text-center">true, false</td>
            <td>
                <p>Значение по умолчанию <code>true</code>.</p>
                <p>Показывать ли виртуальный номер, заданный параметром <code>virtual_phone_number</code> в качестве номера звонящего абонента для оператора заданного в параметре <code>operator</code>.</p>
            </td>
        </tr>
        <tr>
            <td colspan="2">contact</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Номер абонента на который совершается вызов. Формат номера должен соответствовать
                    международному стандарту E.164 (например, <code>79091234567</code>). В качестве номера может быть так же указан SIP номер сотрудника.
                </p>
                <blockquote class="bs-callout bs-callout-info">Внутренние номера сотрудников не поддерживаются.</blockquote>
            </td>
        </tr>
        <tr>
            <td colspan="2">external_id</td>
            <td>string</td>
            <td class="text-center">нет</td>
            <td class="text-center"></td>
            <td>Уникальный идентификатор, который может быть использован для связи события звонка с внешней системой.</td>
        </tr>
        <tr>
            <td colspan="2">dtmf_string</td>
            <td>string</td>
            <td class="text-center">нет</td>
            <td class="text-center">0-9, *, #</td>
            <td>Задаёт DTMF, который будет отправлен абоненту, заданному в параметре <code>contact</code>. С
                помощью символа <code>.</code> = '1 секунда' можно задать таймаут по истечению которого символ DTMF будет отправлен. Пример: <code>.12.1..4</code>, т.е через 1 секунду
                будет отправлена цифра 12, далее через 1 секунду будет отправлена цифра 1 и через 2 секунды цифра 4.
            </td>
        </tr>
        <tr>
            <td colspan="2">direction</td>
            <td>string</td>
            <td class="text-center">нет</td>
            <td class="text-center">in, out</td>
            <td>Значение по умолчанию <code>out</code>. <br>
                Определяет направление звонка <code>in</code> - Входящий звонок, <code>out</code> - Исходящий звонок.
            </td>
        </tr>
        <tr>
            <td class="color-bg" colspan="6" align="center">Оператор с которым будет соединён абонент из параметра <code>contact</code></td>
        </tr>
        <tr>
            <td colspan="2">operator</td>
            <td>object</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                Номер оператора с которым будет соединен абонент из параметра <code>contact</code>. Оператору доступно управление звонком - опции разговора. Формат номера должен соответствовать международному стандарту E.164 (например, “79091234567”).
                <blockquote class="bs-callout bs-callout-info">Не является сотрудником и в отчетах не будет фигурировать как сотрудник</blockquote>
            </td>
        </tr>
        <tr>
            <td colspan="2">contact_message</td>
            <td>object</td>
            <td class="text-center">нет</td>
            <td class="text-center"></td>
            <td>
                <p>Определяет параметры сообщения, которое необходимо проиграть абоненту заданному в параметре <code>contact</code>.</p>
                <blockquote class="bs-callout bs-callout-info"> После окончания проигрывания сообщения, будет проигрываться покругу сообщение из параметра <code>media_file_id</code></blockquote>
            </td>
        </tr>
        <tr>
            <td></td>
            <td>type</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center">media, tts</td>
            <td>
                <p>Определяет тип сообщения. <code>media</code> - файл или <code>tts</code> - текст для услуги синтеза речи Text-to-Speech.</p>
            </td>
        </tr>
        <tr>
            <td></td>
            <td>value</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Если поле <code>type</code> имеет значение <code>media</code>, то в качестве значения принимается идентификатор файла для
                    проигрывания. Файл для проигрывания может быть системным или пользовательским. Идентификатор
                    файла для проигрывания можно получить с помощью REST API - Получение списка пользовательских файлов, Получение списка системных файлов
                </p>
                <p>Если поле <code>type</code> имеет значение <code>tts</code>, то в качестве значения принимается текст для синтезирования его в
                    голосовое сообщение.
                </p>
                <blockquote class="bs-callout bs-callout-info"> Длина TTS сообщения регулируется тарифным планом и установленным лимитом.</blockquote>
            </td>
        </tr>
        <tr>
            <td class="color-bg" colspan="6" align="center">Сообщение для проигрывания абоненту, который задан в параметре <code>operator</code></td>
        </tr>
        <tr>
            <td colspan="2">operator_message</td>
            <td>object</td>
            <td class="text-center">нет</td>
            <td class="text-center"></td>
            <td>
                <p>Определяет параметры сообщения, которое необходимо проиграть абоненту заданному в параметре <code>operator</code>.</p>
                <blockquote class="bs-callout bs-callout-info"> После окончания проигрывания сообщения, будет проигрываться покругу сообщение из параметра <code>media_file_id</code></blockquote>
            </td>
        </tr>
        <tr>
            <td></td>
            <td>type</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center">media, tts</td>
            <td>
                <p>Определяет тип сообщения. <code>media</code> - файл или <code>tts</code> - текст для услуги синтеза речи Text-to-Speech.</p>
            </td>
        </tr>
        <tr>
            <td></td>
            <td>value</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Если поле <code>type</code> имеет значение <code>media</code>, то в качестве значения принимается идентификатор файла для
                    проигрывания. Файл для проигрывания может быть системным или пользовательским. Идентификатор
                    файла для проигрывания можно получить с помощью REST API - Получение списка пользовательских файлов, Получение списка системных файлов
                </p>
                <p>Если поле <code>type</code> имеет значение <code>tts</code>, то в качестве значения принимается текст для синтезирования его в
                    голосовое сообщение.
                </p>
                <blockquote class="bs-callout bs-callout-info"> Длина TTS сообщения регулируется тарифным планом и установленным лимитом.</blockquote>
            </td>
        </tr>
    </tbody>
</table>

#### Параметры ответа

| Название | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
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
| --- | --- | --- | --- |
| The maximum length of Text-to-Speech message is {`tts_message_max_length`}. The length of your message is {`sent_tts_message_length`} | -32602 | `tts_text_exceeded` | Длина сообщения превысила допустимое ограничение, установленное тарифным планом |
| The media file with id {`media_file_id`} not found | -32602 | `media_file_not_found` |
| Virtual phone number {`virtual_phone_number`} not found. It is not your virtual phone number. | -32007 | `virtual_phone_number_not_found` | Если используется виртуальный номер не принадлежащий клиенту или 800-й номер |
| Parameter `contact` can not contain own virtual phone number | -32602 | `own_virtual_phone_number_not_allowed` | Звонок на собственный виртуальный номер запрещён |
| The contact {`contact`} has been found in the blacklist | -32002 | `contact_in_blacklist` |
| The character encoding must be UTF-8 | -32602 | `character_encoding_not_allowed` |

См. также раздел Список ошибок общих для всех методов

## Группа методов управления вызовами

### Создать звонок для трансфера или консультации

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>make.call</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

> Метод доступен для использования только после вызова "hold.call", см. раздел "Диаграмма состояний звонка"

#### Параметры запроса

<table>
    <tbody>
        <tr>
            <th colspan="2">Название</th>
            <th>Тип</th>
            <th>Обязательный</th>
            <th>Допустимые значения</th>
            <th>Описание</th>
        </tr>
        <tr>
            <td colspan="2">access_token</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Ключ сессии аутентификации</td>
        </tr>
        <tr>
            <td colspan="2">call_session_id</td>
            <td>number</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове 
                методов start.informer_call, start.vnumber_call, start.scenario_call, start.employee_call, с помощью сервера уведомлений или REST API.
            </td>
        </tr>
        <tr>
            <td colspan="2">to</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Номер абонента на который совершаем звонок. Формат номера должен соответствовать международному стандарту E.164</p>
                <blockquote class="bs-callout bs-callout-info"> Может быть внутренний номер, внешний номер, номер сотрудника, sip-номер</blockquote>
            </td>
        </tr>
        <tr>
            <td class="color-bg" colspan="6" align="center">Сообщение для проигрывания абоненту, который задан в параметре <code>to</code></td>
        </tr>
        <tr>
            <td colspan="2">to_message</td>
            <td>object</td>
            <td class="text-center">нет</td>
            <td class="text-center"></td>
            <td>
                <p>Определяет параметры сообщения, которое необходимо проиграть абоненту заданному в параметре <code>to_message</code>.</p>
                <blockquote class="bs-callout bs-callout-info"> Абонент, который стоит на удержании, будет ожидать ответа, пока не будет прослушано сообщение до конца</blockquote>
            </td>
        </tr>
        <tr>
            <td></td>
            <td>type</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center">media, tts</td>
            <td>
                <p>Определяет тип сообщения. <code>media</code> - файл или <code>tts</code> - текст для услуги синтеза речи Text-to-Speech.</p>
            </td>
        </tr>
        <tr>
            <td></td>
            <td>value</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Если поле <code>type</code> имеет значение <code>media</code>, то в качестве значения принимается идентификатор файла для
                    проигрывания. Файл для проигрывания может быть системным или пользовательским. Идентификатор
                    файла для проигрывания можно получить с помощью REST API - Получение списка пользовательских файлов, Получение списка системных файлов
                </p>
                <p>Если поле <code>type</code> имеет значение <code>tts</code>, то в качестве значения принимается текст для синтезирования его в
                    голосовое сообщение.
                </p>
                <blockquote class="bs-callout bs-callout-info"> Длина TTS сообщения регулируется тарифным планом и установленным лимитом.</blockquote>
            </td>
        </tr>
    </tbody>
</table>

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
| --- | --- | --- | --- |
| This method can not be called in this state | -32004 | `invalid_state` | Метод может быть вызван только после метода hold.call |
| The character encoding must be UTF-8 | -32602 | `character_encoding_not_allowed` |
| The phone number does not exist or inactive | -32602 | `no_active_phone_number` | У сотрудника могут отсутствовать активные номера |

См. также раздел Список ошибок общих для всех методов

### Сделать трансфер звонка

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>transfer.talk</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

> Метод доступен для использования только после вызова "make.call", см. раздел "Диаграмма состояний звонка"

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов start.vnumber_call, start.scenario_call, start.employee_call, с помощью сервера уведомлений или REST API. |

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
|  --- | --- | --- | --- |
| This method can not be called in this state | -32004 | `invalid_state` | Метод может быть вызван, только после метода make.call и в состоянии `dialing` или `operator_talk` (см. Диаграмма состояний звонка) |

См. также раздел Список ошибок общих для всех методов

### Возвращение в разговор

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>restore.talk</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

> Метод доступен для использования только после вызова "make.call", см. раздел "Диаграмма состояний звонка"

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов start.vnumber_call, start.scenario_call, start.employee_call, с помощью сервера уведомлений или REST API. |

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
|  --- | --- | --- | --- |
| This method can not be called in this state | -32004 | `invalid_state` | Метод может быть вызван, только после метода make.call и в состоянии `dialing` или `operator_talk` (см. Диаграмма состояний звонка) |

См. также раздел Список ошибок общих для всех методов

### Постановка вызова на удержание

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>hold.call</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

#### Параметры запроса

<table>
    <tbody>
        <tr>
            <th colspan="2">Название</th>
            <th>Тип</th>
            <th>Обязательный</th>
            <th>Допустимые значения</th>
            <th>Описание</th>
        </tr>
        <tr>
            <td colspan="2">access_token</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Ключ сессии аутентификации</td>
        </tr>
        <tr>
            <td colspan="2">call_session_id</td>
            <td>number</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове 
                методов start.vnumber_call, start.scenario_call, start.employee_call, с помощью сервера уведомлений или REST API.
            </td>
        </tr>
        <tr>
            <td class="color-bg" colspan="6" align="center">Проигрывание сообщения вызывающему абоненту</td>
        </tr>
        <tr>
            <td colspan="2">contact_message</td>
            <td>object</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                Определяет параметры сообщения, которое необходимо проиграть вызывающему абоненту. 
                Вызывающий абонент - это номер, указанный в параметре <code>contact</code> в методах 
                start.informer_call, 
                start.vnumber_call, 
                start.scenario_call, 
                start.employee_call или абонент позвонивший на виртуальную АТС.
            </td>
        </tr>
        <tr>
            <td></td>
            <td>type</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center">media, tts</td>
            <td>
                <p>Определяет тип сообщения. <code>media</code> - файл или <code>tts</code> - текст для услуги синтеза речи Text-to-Speech.</p>
            </td>
        </tr>
        <tr>
            <td></td>
            <td>value</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Если поле <code>type</code> имеет значение <code>media</code>, то в качестве значения принимается идентификатор файла для
                    проигрывания. Файл для проигрывания может быть системным или пользовательским. Идентификатор
                    файла для проигрывания можно получить с помощью REST API - Получение списка пользовательских файлов, Получение списка системных файлов
                </p>
                <p>Если поле <code>type</code> имеет значение <code>tts</code>, то в качестве значения принимается текст для синтезирования его в
                    голосовое сообщение.
                </p>
                <blockquote class="bs-callout bs-callout-info"> Длина TTS сообщения регулируется тарифным планом и установленным лимитом.</blockquote>
            </td>
        </tr>
    </tbody>
</table>

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
| --- | --- | --- | --- |
| This method can not be called in this state | -32004 | `invalid_state` | Метод может быть вызван только в состоянии `Talk` (см. Диаграмма состояний звонка) |
| The media file with id {`media_file_id`} not found | -32602 | `media_file_not_found` |
| The maximum length of Text-to-Speech message is {`tts_message_max_length`}. The length of your message is {`sent_tts_message_length`} | -32602 | `tts_text_exceeded` | Длина сообщения превысила допустимое ограничение, установленное тарифным планом |
| The character encoding must be UTF-8 | -32602 | `character_encoding_not_allowed` |

См. также раздел Список ошибок общих для всех методов

### Снятие вызова с удержания

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>unhold.call</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

> Метод доступен для использования только после вызова "hold.call", см. раздел "Диаграмма состояний звонка"

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов start.vnumber_call, start.scenario_call, start.employee_call, с помощью сервера уведомлений или REST API. |

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
| --- | --- | --- | --- |
| This method can not be called in this state | -32004 | `invalid_state` | Метод может быть вызван только в состоянии `hold` (см. Диаграмма состояний звонка) |

См. также раздел Список ошибок общих для всех методов

### Простановка тэга

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>tag.call</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

> Тег может быть проставлен только на активный вызов. На завершенный вызов тег можно проставить с помощью REST API метода - Установка тега обращению

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов start.informer_call, start.vnumber_call, start.scenario_call, start.employee_call, с помощью сервера уведомлений или REST API. |
| tag_id | number | да | Уникальный идентификатор тега, который можно получить с помощью REST API метода - Получение списка тегов |

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
| --- | --- | --- | --- |
| Tag with {`tag_id`} not found | -32602 | `tag_not_found` |
| Duplicate tag | -32602 | `duplicate_tag` |

См. также раздел Список ошибок общих для всех методов

### Завершение разговора отдельного участника

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>disconnect.leg</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

> Завершение плеча `contact` (см. методы start.employee_call, start.scenario_call, start.informer_call, start.vnumber_call) приводит к завершению всей сессии звонка

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов start.informer_call, start.vnumber_call, start.scenario_call, start.employee_call, с помощью сервера уведомлений или REST API. |
| leg_id | number | да | Уникальный идентификатор участника разговора, который может быть получен через сервера уведомлений. |

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
| --- | --- | --- | --- |
| The call's leg not found | -32602 | `leg_not_found` |

См. также раздел Список ошибок общих для всех методов

### Подключение тренера к разговору

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>add.coach</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

> Метод позволяет подключить к разговору тренера, который задан в личном кабинете или указан в параметре `phone_number`

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов start.vnumber_call, start.scenario_call, start.employee_call, с помощью сервера уведомлений или REST API. |
| phone_number | string | нет | Номер тренера, если не используется тренер указанный в личном кабинете. <blockquote>Если номер тренера не задан, то подключается тренер из настроек сотрудника в личном кабинете</blockquote> |

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
| --- | --- | --- | --- |
| The coach not found in your settings | -32602 | `coach_not_found` |
| The phone number does not exist or inactive | -32602 | `no_active_phone_number` |

См. также раздел Список ошибок общих для всех методов

### Управление записью разговора

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>record.call</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

> Метод позволяет управлять записью активного разговора. Глобальную запись разговора выключить невозможно.

#### Параметры запроса

| Название | Тип | Обязательный | Допустимые значения | Описание |
| --- | --- | --- | --- | --- |
| access_token | string | да | | Ключ сессии аутентификации |
| call_session_id | number | да | | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов start.informer_call, start.vnumber_call, start.scenario_call, start.employee_call, с помощью сервера уведомлений или REST API. |
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
| --- | --- | --- | --- |
| Permission denied | -32003 | `forbidden` | Невозможно отключить глобальную запись разговора. Глобальной записью разговора является настройка на уровне настройки сотрудника. |
| The call record is already started | -32602 | `record_already_started` | Запись разговора уже включена |
| The call record is not enabled | -32602 | `record_already_stopped` | Запись разговора не была включена |

См. также раздел Список ошибок общих для всех методов

### Занести абонента в чёрный список

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>block.contact</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов start.informer_call, start.vnumber_call, start.scenario_call, start.employee_call, с помощью сервера уведомлений или REST API. |

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
| --- | --- | --- | --- |
| The contact with phone number {`contact_phone_number`} already exists | -32602 | `duplicate_contact` |

См. также раздел Список ошибок общих для всех методов

### Использование опций разговора

> Опции разговора настраиваются в личном кабинете "Виртуальная АТС" -> "Опции разговора". Повторный вызов отдельных опций разговора отключает их, примерами таких опций являются `Диктофон`, `Позвонить тренеру` (см. метод list.talk_options)

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>call.talk_option</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов start.informer_call, start.vnumber_call, start.scenario_call, start.employee_call, с помощью сервера уведомлений или REST API. |
| button | string | да | Клавиша, вызывающая опцию разговора. <blockquote>Клавиши и действия настраиваются в личном кабинете.</blockquote> |

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
| --- | --- | --- | --- |
| This method can not be called in this state | -32004 | `invalid_state` |
| Internal error, contact the support service | -32603 | `internal_error` |
| Permission denied | -32003 | `forbidden` | Невозможно отключить глобальную запись разговора на уровне настройки сотрудника |
| Call session not found | -32602 | `call_session_not_found` |
| Talk option not found | -32602 | `talk_option_not_found` | Клавиша не назначена |

См. также раздел Список ошибок общих для всех методов

### Отправка DTMF сотрудником

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>send.dtmf</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

#### Параметры запроса

| Название | Тип | Обязательный | Допустимые значения | Описание |
| --- | --- | --- | --- | --- |
| access_token | string | да | | Ключ сессии аутентификации |
| call_session_id | number | да | | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов start.informer_call, start.vnumber_call, start.scenario_call, start.employee_call, с помощью сервера уведомлений или REST API. |
| dtmf_string | string | да | 0-9, *, # | Задаёт DTMF, который будет отправлен абоненту, который задан в параметре `contact`. В случае исходящего вызова с виртуальной АТС в сторону вызываемого абонента. <blockquote>Отправка DTMF возможна только одиночными символами.</blockquote> |

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

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>list.talk_options</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

> Опции разговора настраиваются в личном кабинете "Виртуальная АТС" -> "Опции разговора".

#### Параметры запроса

| Название | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
| access_token | string | да | Ключ сессии аутентификации |

#### Параметры ответа

<table>
    <tbody>
        <tr>
            <th colspan="2">Название</th>
            <th>Тип</th>
            <th>Обязательный</th>
            <th>Описание</th>
        </tr>
        <tr>
            <td colspan="2">button</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td>Клавиша, которая вызывает опцию разговора</td>
        </tr>
        <tr>
            <td colspan="2">mnemonic</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td>
                Мнемоническое имя опции разговора
                Возможные значения:
                <ul>
                    <li>finish_conversation - Завершить разговор
                    </li>
                    <li>tag_call - Проставить тег
                    </li>
                    <li>record_talk - Диктофон (запись разговора)
                    </li>
                    <li>transfer_call - Трансфер
                    </li>
                    <li>run_scenario - Выполнить сценарий
                    </li>
                    <li>call_coach - Позвонить тренеру
                    </li>
                    <li>receive_fax - Принять факс
                    </li>
                    <li>save_numa - Сохранить АОН в группу
                    </li>
                </ul>
            </td>
        </tr>
        <tr>
            <td colspan="2">name</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td>Название опции разговора</td>
        </tr>
        <tr>
            <td class="color-bg" colspan="6" align="center">Значение опции</td>
        </tr>
        <tr>
            <td colspan="2">button_value</td>
            <td>object</td>
            <td class="text-center">нет</td>
            <td>Некоторые опции могут содержать название тега, название сценария и т.д. Данный объект содержит дополнительную информацию об опции разговора</td>
        </tr>
        <tr>
            <td width="15"></td>
            <td>id</td>
            <td>number</td>
            <td class="text-center">да</td>
            <td>Уникальный идентификатор объекта, котрый активируется при вызове опции разговора. К примеру, идентификатор сценария</td>
        </tr>
        <tr>
            <td></td>
            <td>value</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td>Название объекта, который активируется при вызове опции разговора. К примеру, название сценария</td>
        </tr>
    </tbody>
</table>

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

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>list.calls</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

##### Параметры запроса

| Название | Тип | Обязательный | Допустимые значения | Описание |
| --- | --- | --- | --- | --- |
| access_token | string | да |  | Ключ сессии аутентификации |
| direction | string | нет | in, out | Указывает какие сессии выводить - входящие, исходящие. Если параметр не задан, то выводятся все сессии. |
| virtual_phone_number | string | нет |  | Указывает с каким виртуальным номер активные вызовы показывать. <blockquote>Номер должен начинаться с 7</blockquote> |

##### Параметры ответа

<table>
    <tbody>
        <tr>
            <th colspan="2">Название</th>
            <th>Тип</th>
            <th>Обязательный</th>
            <th>Допустимые значения</th>
            <th>Описание</th>
        </tr>
        <tr>
            <td colspan="2">call_session_id</td>
            <td>number</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове 
                методов start.informer_call, start.vnumber_call, start.scenario_call, start.employee_call, с помощью сервера уведомлений или REST API.
            </td>
        </tr>
        <tr>
            <td colspan="2">direction</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center">in, out</td>
            <td>Направление сесси звонка</td>
        </tr>
        <tr>
            <td colspan="2">start_time</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Время начала вызова. Формат <code>YYYY-MM-DD hh:mm:ss</code></td>
        </tr>
        <tr>
            <td colspan="2">virtual_phone_number</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Виртуальный номер, который был использован как номер представления.</td>
        </tr>
        <tr>
            <td colspan="2">contact_phone_number</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Номер абонента</td>
        </tr>
        <tr>
            <td colspan="2">external_id</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Уникальный идентификатор запроса во внешней системе клиента</td>
        </tr>
        <tr>
            <td class="color-bg" colspan="6" align="center">Список проставленных тегов</td>
        </tr>
        <tr>
            <td colspan="2">tags</td>
            <td>array</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Список проставленных тегов</td>
        </tr>
        <tr>
            <td width="15"></td>
            <td>tag_id</td>
            <td>number</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Уникальный идентификатор тега</td>
        </tr>
        <tr>
            <td></td>
            <td>tag_name</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Имя тега</td>
        </tr>
        <tr>
            <td class="color-bg" colspan="6" align="center">Участники сессии звонка</td>
        </tr>
        <tr>
            <td colspan="2">legs</td>
            <td>array</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Список участников сессии звонка</td>
        </tr>
        <tr>
            <td></td>
            <td>leg_id</td>
            <td>number</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Уникальный идентификатор плеча</td>
        </tr>
        <tr>
            <td></td>
            <td>calling_phone_number</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Номер вызывающего абонента</td>
        </tr>
        <tr>
            <td></td>
            <td>called_phone_number</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Номер вызываемого абонента</td>
        </tr>
        <tr>
            <td></td>
            <td>is_operator</td>
            <td>boolean</td>
            <td class="text-center">да</td>
            <td class="text-center">true, false</td>
            <td>Признак, который идентифицирует плечо, у которого есть права на выполнение опций разговора</td>
        </tr>
        <tr>
            <td></td>
            <td>employee_id</td>
            <td>number</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Уникальный идентификатор сотрудника</td>
        </tr>
        <tr>
            <td></td>
            <td>employee_full_name</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>Ф.И.О сотрудника</td>
        </tr>
        <tr>
            <td></td>
            <td>record_call_enabled</td>
            <td>boolean</td>
            <td class="text-center">да</td>
            <td class="text-center">true, false</td>
            <td>
                <p>Включена/Выключена запись разговора.<br> <code>true</code> - включена, <code>false</code> - выключена</p>
            </td>
        </tr>
        <tr>
            <td></td>
            <td>state</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td class="text-center"></td>
            <td>
                <p>Состояние участника разговора</p>
                Возможные значения:
                <ul>
                    <li>Дозвон
                    </li>
                    <li>Разговор
                    </li>
                    <li>На очереди
                    </li>
                    <li>Разъединено
                    </li>
                    <li>Разговор операторов
                    </li>
                    <li>На удержании
                    </li>
                    <li>Перевод вызова
                    </li>
                    <li>Отправка dtmf
                    </li>
                    <li>Получение факса
                    </li>
                    <li>Факс получен
                    </li>
                    <li>Отправка факса
                    </li>
                    <li>Факс отправлен
                    </li>
                </ul>
            </td>
        </tr>
    </tbody>
</table>

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

<table>
    <tbody>
        <tr>
            <th>Метод</th>
            <th><code>release.call</code></th>
        </tr>
        <tr>
            <td>Версия API</td>
            <td>v4.0</td>
        </tr>
        <tr>
            <td colspan="2">Вернуться к списку методов</td>
        </tr>
    </tbody>
</table>

##### Параметры запроса

| Название | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
| access_token | string | да | Ключ сессии аутентификации |
| call_session_id | number | да | Уникальный идентификатор сессии звонка, который может быть получен в ответном сообщении при вызове методов start.informer_call, start.vnumber_call, start.scenario_call, start.employee_call, с помощью сервера уведомлений или REST API. |

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

<table>
    <tbody>
        <tr>
            <th colspan="3">Название</th>
            <th>Тип</th>
            <th>Обязательный</th>
            <th>Допустимые значения</th>
            <th>Описание</th>
        </tr>
        <tr>
            <td colspan="3">error</td>
            <td>object</td>
            <td class="text-center">да</td>
            <td></td>
            <td>Объект с содержимым ошибки</td>
        </tr>
        <tr>
            <td width="15"></td>
            <td colspan="2">code</td>
            <td>number</td>
            <td class="text-center">да</td>
            <td></td>
            <td>Код ошибки (см. раздел Группы кодов ошибок)</td>
        </tr>
        <tr>
            <td></td>
            <td colspan="2">message</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td></td>
            <td>Сообщение об ошибке (см. раздел Список ошибок общих для всех методов)</td>
        </tr>
        <tr>
            <td></td>
            <td colspan="2">data</td>
            <td>object</td>
            <td class="text-center">да</td>
            <td></td>
            <td>Объект с деталями ошибки</td>
        </tr>
        <tr>
            <td width="15"></td>
            <td width="15"></td>
            <td>mnemonic</td>
            <td>string</td>
            <td class="text-center">да</td>
            <td></td>
            <td>Уникальный текстовый код ошибки (см. раздел Список ошибок общих для всех методов, а так же существуют ошибки специфичные для отдельных методов)</td>
        </tr>
        <tr>
            <td></td>
            <td></td>
            <td>field</td>
            <td>string</td>
            <td class="text-center">нет</td>
            <td></td>
            <td>
                <p>Название параметра, с которым связана ошибка</p>
                <blockquote class="bs-callout bs-callout-info">
                    <p> Вложенные параметры отображаем через разделитель "точка": <code>.</code></p>
                    <p>К примеру: <code>employee.phone_number</code></p>
                </blockquote>
            </td>
        </tr>
        <tr>
            <td></td>
            <td></td>
            <td>value</td>
            <td>string</td>
            <td class="text-center">нет</td>
            <td></td>
            <td>
                <p>Содержит то, что передал пользователь без изменений</p>
                <blockquote class="bs-callout bs-callout-info"> В некоторых случаях может отсутствовать. К примеру, обязательный параметр вообще не был заполнен.</blockquote>
            </td>
        </tr>
        <tr>
            <td></td>
            <td></td>
            <td>params</td>
            <td>object</td>
            <td class="text-center">нет</td>
            <td></td>
            <td>Карта подстановок параметров для шаблона с текстом об ошибке. Т.е. содержит динамически изменяемые значения, к примеру, лимиты, длина TTS сообщения. Значения указанные в этом параметре могут быть использованы в сообщениях об ошибках в интерфейсе над Call API (рабочее место оператора).</td>
        </tr>
        <tr>
            <td></td>
            <td></td>
            <td>extended_helper</td>
            <td>string</td>
            <td class="text-center">нет</td>
            <td></td>
            <td>Ссылка на более подробное описание ошибки и возможные решения</td>
        </tr>
    </tbody>
</table>

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
| --- | --- |
| -32700 | Ошибки связанные с валидацией JSON |
| -32600 | Ошибки связанные с валидацией параметров запроса - `id`, `jsonrpc` |
| -32601 | Ошибки связанные с методом |
| -32602 | Ошибки связанные с валидацией параметров в вызываемом методе |
| -32603 | Внутренние ошибки JSON RPC сервера <blockquote>В случае возникновения ошибки, необходимо обратиться в службу технической поддержки, передав запрос, который вызвал ошибку и время запроса.</blockquote> |
| -32001 | Ошибки аутентификации и ошибки с ключами |
| -32002 | Ошибки связанные с: правила безопасности запрещают звонок по указанному направлению (см. раздел Правила безопасности) и черным списком |
| -32003 | Ошибки с правами доступа - ip адрес не в белом списке, нет прав у пользователя |
| -32004 | Ошибки связанные с неверной последовательностью вызываемых методов |
| -32007 | Ошибки связанные с виртуальным номером |
| -32008 | Ошибки связанные с компонентами |
| -32009 | Ошибки связанные с аккаунтом <blockquote>В случае возникновения ошибки, необходимо обратиться в службу технической поддержки, передав запрос, который вызвал ошибку и время запроса.</blockquote> |
| -32029 | Ошибки связанные с лимитами |
| -32099 | Ошибки связанные с поддержкой различных частей спецификации JSON RPC 2.0 - Групповые операции, Уведомления |

### Список ошибок общих для всех методов

| Текст сообщения | Код | Мнемоника | Описание |
| --- | --- | --- | --- |
| Invalid Request The JSON sent is not a valid Request object | -32600 | `invalid_request` | Ошибки связанные с валидацией параметров запроса - `id`, `jsonrpc` |
| Access token has been expired | -32001 | `access_token_expired` | Применяется только к постоянному токену. Если время жизни постоянного токена истекло, то возвращается указанная ошибка |
| Access token has been blocked | -32001 | `access_token_blocked` | Если постоянный токен заблокирован, то возвращается указанная ошибка |
| Access token is invalid | -32001 | `access_token_invalid` | Указанная ошибка возвращается, если постоянный/временный токен не найден или истекло время жизни временного токена |
| Limit per {limit_type} has been exceeded. Value of current limit per {limit_type} is {limit_max_value} | -32029 | `limit_exceeded` | Превышены установленные лимиты согласно тарифного правила (см. раздел Лимиты и ограничения) |
| Component {component_name} has been disabled | -32008 | `component_disabled` |
| Your IP {ip} is not whitelisted | -32003 | `ip_not_whitelisted` | IP адрес, с которого идет запрос не находится в белом списке (см. личный кабинет "Аккаунт" -> "Call API" вкладка "IP адреса" |
| Login or password is wrong | -32001 | `auth_error` | Некорректный логин или пароль |
| Your account has been disabled, contact the support service | -32009 | `account_inactive` | Аккаунт заблокирован <blockquote>В случае возникновения ошибки, необходимо обратиться в службу технической поддержки, передав запрос, который вызвал ошибку и время запроса.</blockquote> |
| Call session not found | -32602 | `call_session_not_found` | Если передали ID сессии, который неизвестен |
| Internal error, contact the support service | -32603 | `internal_error` | <blockquote>В случае возникновения ошибки, необходимо обратиться в службу технической поддержки, передав запрос, который вызвал ошибку и время запроса.</blockquote> |
| Data supplied is of wrong type | -32602 | `data_type_error` | К примеру, если ожидаем `string` а передали `int` |
| The method does not exist / is not available | -32601 | `method_not_found` | Вызывается метод, который отсутствует в спецификации (см. раздел Список методов) |
| Permission denied | -32003 | `forbidden` | Нет прав на доступ к методу или API, или запрещено выполнять какое-либо действие |
| Invalid JSON was received by the server. | -32700 | `parse_error` | Невалидный JSON |
| Batch operations not supported | -32099 | `batch_opreations_not_supported` | Групповые операции не поддерживаются (см. JSON-RPC 2.0) |
| Notifications not supported | -32099 | `notifications_not_supported` | Уведомления не поддерживаются (см. JSON-RPC 2.0) |
| The required parameter has been missed | -32602 | `required_parameter_missed` | Обязательный параметр не передали |
| Invalid parameter value | -32602 | `invalid_parameter_value` | Возвращается во всех случаях, если было передано некорректное значение параметра или переданное значение не соответствует требуемому формату ввода |
| Unexpected method parameter(s) | -32602 | `unexpected_parameters` | Если в `params` были переданы параметры, которые не предусмотрены JSON структурой метода |