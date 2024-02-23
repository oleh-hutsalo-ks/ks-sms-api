# Київстар Відкритий Телеком API
# **Вступ**
  API від Київстар Відкритий Телеком (КВТ) дозволяє використовувати можливості Київстар для розробки нових продуктів, створення додатків та автоматизації бізнес-процесів з мінімальним показником Time to First Successful API Call.
  У цій API-документації є вся необхідна інформація, щоб зробити інтеграцію з продуктами КВТ (від опису ендпойнтів до можливих відповідей та помилок).
  Вона постійно оновлюється та завжди є актуальною. Також документація є інтерактивною — можна зробити запит прямо з неї та отримати відповідь із сервера.
# **Автентифікація**
## **Client ID, Client Secret і access-токен**
  Client ID та Client Secret — це ідентифікатор та пароль до кожного з ваших додатків. З ними ви зможете згенерувати access-токен, який дозволяє звертатися до серверів платформи КВТ.
  Access-токен потрібно періодично оновлювати, оскільки він має обмежений строк дії.

  **Зверніть увагу**. Одразу після реєстрації, вам буде доступний лише моковий додаток. Його Client ID та Client Secret підходитимуть лише до мокового сервера.
  Додаток, що дозволить звертатися до базового серверу, з'явиться в вашому особистому кабінеті, якщо у вас буде «ранній доступ».
  Заявку на такий доступ можна подати в <a href="https://api-market.kyivstar.ua" target="_blank">особистому кабінеті</a>.
## **Як отримати access-токен**
  Перш ніж робити запити до платформи КВТ, зареєструйтеся в <a href="https://api-market.kyivstar.ua" target="_blank">особистому кабінеті</a>.
  Після реєстрації отримайте Client ID та Client Secret в особистому кабінеті на сторінці відповідного додатка. Їх можна використати для запиту access-токена. Якщо запит успішний, КВТ поверне access-токен (дійсний протягом 8 годин).
  Отримавши access-токен, необхідно його передавати в Authorization Header з кожним API запитом. Таким чином ви робите безпечну та авторизовану транзакцію.
  <br>
  ![процес автентифікації](/api/public/auth_kot_get_token.png)
  <br>
  <br>
  <details>
  <summary>Отримати токен у терміналі (curl)</summary>
  ```bash
  curl 'https://<server_url>/idp/oauth2/token' -X POST -d 'grant_type=client_credentials' --user <your_client_id>:<your_client_secret>
  ```
  </details>
  <br>
  <details>
  <summary>Отримати токен за допомогою Python</summary>

  ```python
  import requests
  from base64 import b64encode
  import json
  # SET HERE YOUR url, clientId and secret
  authServerTokenUrl = "https://<server_url>/idp/oauth2/token"
  clientId = <your_client_id>
  clientSecret = <your_client_secret>
  def authenticate(authUrl, username, password):
    encoded = (username + ":" + password).encode("utf-8")
    userAndPass = b64encode(encoded).decode("ascii")
    headers = { 'Authorization' : 'Basic %s' %  userAndPass }
    payload = 'grant_type=client_credentials'
    headers = {
      'Content-Type': 'application/x-www-form-urlencoded',
      'Authorization': 'Basic %s' %  userAndPass
    }
    response = requests.request("POST", authUrl, headers=headers, data = payload)
    data = json.loads(response.text.encode('utf8'))
    return data["access_token"]
  authToken = authenticate(authServerTokenUrl, clientId, clientSecret)
  print("Auth token: " + authToken)
  ```
  </details>

## **Як оновити access-токен**
  Строк дії виданого access-токена закінчується через 8 годин (28799 секунд).
  Вам необхідно розробити своє програмне забезпечення таким чином, щоб ваш код міг відстежити коли строк дії access-токена минає і що його потрібно оновити.
  Це можна відстежити за допомогою поля "expires_in", що повертається разом з access-токеном.
  Також ви можете використовувати помилку з кодом 401, щоб відстежувати недійсність access-токена.
  <br>
  ![процес автентифікації](/api/public/auth_kot_renew_token.png)
  <br>
# **Сервери**
  Окрім сервера з реальними даними, АРІ КВТ надає можливість скористатися моковим сервером.
  Щоб вибрати сервер, виберіть відповідний пункт у списку на нашій документації або вкажіть потрібний URL у коді.
  * **Базовий сервер** - повертає реальні дані. Виклики на цей сервер тарифікуються в звичайному порядку.
  * **Моковий сервер** - повертає дані, які мають структуру, ідентичну до реальної, проте не мають жодного сенсу з точки зору змісту. Виклики на цей сервер ніяк не вплинуть на стан вашого рахунку. Таким чином ви можете провести інтеграцію з продуктами КВТ на цих даних і бути впевненими, що інтеграцію на реальних даних буде налаштовано вірно. **Зверніть увагу**, що Client ID і Client Secret для мокового сервера відрізняються від тих, що використовуються для базового серверу.
  <br> Для викликів до мокового серверу, потрібно використовувати приклади, що вказані в документації.

# **Як робити виклики з інтерактивної документації?**
1. Виберіть сервер, до якого хочете надіслати запит.
2. Візьміть відповідні Client ID та Client secret зі сторінки додатка в особистому кабінеті, підставте їх у блок аутентифікації та натисніть кнопку "Get Token".
3. Оберіть продукт, підставте відповідні значення для виклику та натисніть кнопку "Try", щоб отримати відповідь.
**Зверніть увагу**: якщо ви вибрали моковий сервер, використовуйте приклади викликів, які вказані в документації.

# **Як користуватися пісочницею**
1. Виберіть сервер пісочниці.
2. Візьміть відповідні Client ID та Client secret зі сторінки додатка в особистому кабінеті, підставте їх у блок аутентифікації та натисніть кнопку "Get Token".
3. Оберіть продукт, підставте відповідні значення для виклику та натисніть кнопку "Try", щоб отримати відповідь.
**Зверніть увагу**: також, ви можете робити виклики за вашого ПЗ або Postman та ін. Кількість викликів обмежена, залишок можна подивитися на сторінці Пісочниці в особистому кабінеті.

## Version: Closed Beta

### /subscribers/{phoneNumber}/verify-sim

#### POST
##### Summary:

Перевірка заміни SIM карти

##### Description:

Перевірка заміни SIM карти протягом певного проміжку часу повертає значення, чи була замінена SIM за вказаний період часу та чи є вона активною на момент запиту (1 — так, 0 — ні). Детальніше про продукт <a href="https://kyivstar.ua/uk/business/open-api/imsi" target="_blank">тут</a>.


##### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| phoneNumber | path | Мобільний номер у міжнародному форматі | Yes | [subscriber-phone-number](#subscriber-phone-number) |
| Content-Type | header |  | No | string |

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | OK |
| 400 | Bad Request |
| 401 | Unauthorized |
| 404 | Not Found |
| 422 | Unprocessable Entity |
| 429 | Too Many Requests |
| 500 | Internal Server Error |
| 504 | Gateway Timeout |

##### Security

| Security Schema | Scopes |
| --- | --- |
| OAuth2 | |

### /subscribers/{phoneNumber}/check-sim-count

#### POST
##### Summary:

Перевірка кількості SIM карт протягом певного проміжку часу

##### Description:

Перевірка кількості SIM карт протягом певного проміжку часу. Повертає значення кількості SIM карт за номером телефону абонента за вказаний період часу (1-120 днів). Мінімальне значення - 1, максимальне значення - 10 (в абонента було 10 або більше SIM карт за період).


##### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| phoneNumber | path | Мобільний номер у міжнародному форматі | Yes | [subscriber-phone-number](#subscriber-phone-number) |
| Content-Type | header |  | No | string |

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | OK |
| 400 | Bad Request |
| 401 | Unauthorized |
| 404 | Not Found |
| 422 | Unprocessable Entity |
| 429 | Too Many Requests |
| 500 | Internal Server Error |
| 504 | Gateway Timeout |

##### Security

| Security Schema | Scopes |
| --- | --- |
| OAuth2 | |

### /subscribers/{phoneNumber}/device-check

#### GET
##### Summary:

Верифікація IMEI ідентифікатора пристрою користувача

##### Description:

Верифікація IMEI ідентифікатора пристрою користувача.
Використовуйте один будь-який query-string параметр (IMEI або daysPeriod) для запиту.


##### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| phoneNumber | path | Мобільний номер у міжнародному форматі | Yes | [subscriber-phone-number](#subscriber-phone-number) |
| IMEI | query | Ідентифікатор IMEI | No | string |
| daysPeriod | query | Період активності пристрою (в днях). Мінімальне значення - 1, максимальне - 120. | No | integer |
| Content-Type | header |  | No | string |

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | OK |
| 400 | Bad Request |
| 401 | Unauthorized |
| 404 | Not Found |
| 422 | Unprocessable Entity |
| 500 | Internal Server Error |

##### Security

| Security Schema | Scopes |
| --- | --- |
| OAuth2 | |

### /subscribers/{phoneNumber}/lifetime-check

#### POST
##### Summary:

Перевірка активації\зміни SIM-карти абонента

##### Description:

Запит на перевірку часу активації\зміни SIM-карти за останні 30 днів.


##### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| phoneNumber | path | Мобільний номер у міжнародному форматі | Yes | [subscriber-phone-number](#subscriber-phone-number) |
| Content-Type | header |  | No | string |

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | OK |
| 400 | Bad Request |
| 401 | Unauthorized |
| 404 | Not Found |
| 422 | Unprocessable Entity |
| 500 | Internal Server Error |

##### Security

| Security Schema | Scopes |
| --- | --- |
| OAuth2 | |

### /sms

#### POST
##### Summary:

Відправка SMS

##### Description:

Запит на відправку програмованого SMS. Ініціює надсилання SMS та повертає ідентифікатор повідомлення, яким можна скористатися, щоб отримати статус доставки. [Детальніше](https://kyivstar.ua/uk/business/open-api/transactional-sms)
<br> <br> <div style="background-color:var(--light-blue); border-radius:4px; padding:16px; color:var(--fg); line-height:24px;"> **Зверніть увагу!** <br> Щоб надіслати цей запит, вам потрібно отримати альфа-імʼя. Для цього пройдіть верифікацію та подайте заявку в <a href="https://api-market.kyivstar.ua" target="_blank">особистому кабінеті</a> в налаштуваннях API-продукту. Термін погодження — **до 2 тижнів**.</div>

##### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Виклик повертає message ID для перевірки статуса доставки SMS. |
| 401 | Unauthorized |
| 422 | Unprocessable Entity |

##### Security

| Security Schema | Scopes |
| --- | --- |
| OAuth2 | |

### /sms/{msgId}

#### GET
##### Summary:

Статус доставки

##### Description:

Виклик повертає статус доставки SMS за message ID, який є у відповіді на запит відправки повідомлення.

##### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| msgId | path |  | Yes | string |

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Статус доставки повідомлення у форматі RFC3339 |
| 401 | Unauthorized |

##### Security

| Security Schema | Scopes |
| --- | --- |
| OAuth2 | |

### /subscribers/{phoneNumber}/score

#### GET
##### Summary:

Фінансовий скоринг абонента

##### Description:

Даний API повертає фінансовий скоринговий бал абонента Київстар за певною предиктивною моделлю. Щоб отримати предиктивну модель, котра якомога краще підійде вашій компанії, зверніться до support-api-market@kyivstar.net та подайте запит на розробку предиктивної моделі. [Детальніше](https://kyivstar.ua/uk/business/open-api/financial-scoring)

##### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| phoneNumber | path | Мобільний номер у міжнародному форматі | Yes | [subscriber-phone-number](#subscriber-phone-number) |
| modelId | query | Номер предиктивної моделі скорингу. Модель з номером 7 є попередньо налаштованою моделлю загального користування | No | string |

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | OK |
| 401 | Unauthorized |
| 404 | Скор бал недоступний |
| 422 | Incorrect parameters |
| 429 | Too Many Requests |

##### Security

| Security Schema | Scopes |
| --- | --- |
| OAuth2 | |

### /viber/transaction

#### POST
##### Summary:

Транзакційне повідомлення

##### Description:

Запит на відправку транзакційного (сервісного) повідомлення Viber. Ініціює надсилання повідомлення Viber та повертає ідентифікатор повідомлення, яким можна скористатися, щоб отримати статус доставки.
Транзакційними (сервісними) вважаються такі повідомлення Viber, які відповідають узгодженому шаблону.
У випадку розбіжності із узгодженим шаблоном повідомлення тарифікується як рекламне.
[Детальніше](https://kyivstar.ua/business/products/viber-own-data#sms)
<br> <br> <div style="background-color:var(--light-blue); border-radius:4px; padding:16px; color:var(--fg); line-height:24px;"> **Зверніть увагу!** <br> Щоб надіслати цей запит, вам потрібно отримати Viber альфа-імʼя. Для цього пройдіть верифікацію та подайте заявку в <a href="https://api-market.kyivstar.ua" target="_blank">особистому кабінеті</a> в налаштуваннях API-продукту. Термін погодження — **до 2 тижнів**.</div>


##### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Повертає message ID для перевірки статуса доставки Viber повідомлення. |
| 401 | Unauthorized |
| 422 | Unprocessable Entity |

##### Security

| Security Schema | Scopes |
| --- | --- |
| OAuth2 | |

### /viber/promotion

#### POST
##### Summary:

Рекламне повідомлення

##### Description:

Запит на відправку рекламного повідомлення Viber. Ініціює надсилання повідомлення Viber та повертає ідентифікатор повідомлення, яким можна скористатися, щоб отримати статус доставки.
Рекламні повідомлення можуть містити текст, картинку, кнопку-посилання.
[Детальніше про продукт, альфа-імена та шаблони Viber](https://kyivstar.ua/business/products/viber-own-data#sms)
<br> <br> <div style="background-color:var(--light-blue); border-radius:4px; padding:16px; color:var(--fg); line-height:24px;"> **Зверніть увагу!** <br> Щоб надіслати цей запит, вам потрібно отримати Viber альфа-імʼя. Для цього пройдіть верифікацію та подайте заявку в <a href="https://api-market.kyivstar.ua" target="_blank">особистому кабінеті</a> в налаштуваннях API-продукту. Термін погодження — **до 2 тижнів**.</div>


##### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Повертає message ID для перевірки статуса доставки Viber повідомлення. |
| 401 | Unauthorized |
| 422 | Unprocessable Entity |

##### Security

| Security Schema | Scopes |
| --- | --- |
| OAuth2 | |

### /viber/status/{mid}

#### GET
##### Summary:

Статус доставки

##### Description:

Виклик повертає статус доставки повідомлення Viber за message ID, який є у відповіді на запит відправки повідомлення.


##### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| mid | path |  | Yes | string |

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Статус доставки повідомлення у форматі RFC3339 |
| 401 | Unauthorized |

##### Security

| Security Schema | Scopes |
| --- | --- |
| OAuth2 | |

### /promo

#### POST
##### Summary:

Створення розсилки

##### Description:

Запит на створення розсилки SMS на свій список клієнтів. Ініціює створення чернетки розсилки та додає текст. Зверніть увагу, для створення розсилки необхідне альфа-імʼя. Запит повертає ідентифікатор, поточний і можливі подальші статуси розсилки, кількість змінних у тексті повідомлення, ідентифікатор автора розсилки.

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Розсилка успішно створена |
| 401 | Unauthorized |
| 500 | Помилка на сервісі розсилок |
| 503 | Сервіс розсилок тимчасово не доступний |

##### Security

| Security Schema | Scopes |
| --- | --- |
| OAuth2 | |

#### GET
##### Summary:

Отримання списку розсилок

##### Description:

Запит на отримання усіх розсилок, створених через АРІ

##### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| pageSize | query | Кількість елементів на сторінці | Yes | integer |
| pageNumber | query | Номер сторінки | Yes | integer |
| startDateFrom | query | Фільтрація по даті (початок) | No | string |
| startDateTo | query | Фільтрація по даті (кінець) | No | string |
| sortBy | query | Назва поля по якому провести сортування | No | string |
| sortDirection | query | Напрямок сортування | No | string |

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Список розсилок |

##### Security

| Security Schema | Scopes |
| --- | --- |
| OAuth2 | |

### /promo/{promoUUID}

#### GET
##### Summary:

Отримання інформації про розсилку

##### Description:

Запит на отримання поточної інформації про розсилку. Запит повертає поточний стан розсилки та можливі подальші стани.

##### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| promoUUID | path |  | Yes | [promoUUID](#promoUUID) |

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Інформація про розсилку |
| 401 | Unauthorized |
| 404 | Not Found |
| 500 | Помилка на сервісі розсилок |
| 503 | Сервіс розсилок тимчасово не доступний |

##### Security

| Security Schema | Scopes |
| --- | --- |
| OAuth2 | |

### /promo/{promoUUID}/audience

#### POST
##### Summary:

Додавання аудиторії

##### Description:

Запит на додавання аудиторії та параметрів зі списку власних номерів для розсилки SMS. Зверніть увагу, номери додаються частинами до 20 тисяч записів в одному запиті. Запит повертає унікальну назву списку номерів телефонів, на які буде здійснюватись розсилка.

##### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| promoUUID | path |  | Yes | [promoUUID](#promoUUID) |

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Інформація про додану аудиторію |
| 401 | Unauthorized |
| 500 | Помилка на сервісі розсилок |
| 503 | Сервіс розсилок тимчасово не доступний |

##### Security

| Security Schema | Scopes |
| --- | --- |
| OAuth2 | |

### /promo/{promoUUID}/status/{status}

#### PUT
##### Summary:

Управління статусом розсилки

##### Description:

Запит на зміну поточного статусу розсилки: переведення в активний стан, припинення активної розсилки. Запит повертає поточний стан розсилки та можливі подальші стани.

Можливі варіанти встановлення статусу розсилки:

- WAITING - встановлюється для постановки розсилки в чергу на виконання. Після початку розсилка автоматично буде переведена в статус ACTIVE
- SUSPENDED - встановлюється для зупинки розсилки

##### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| promoUUID | path |  | Yes | string (uuid) |
| status | path |  | Yes | string |

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Інформація про стан розсилки |
| 401 | Unauthorized |
| 404 | Not Found |
| 500 | Помилка на сервісі розсилок |
| 503 | Сервіс розсилок тимчасово не доступний |

##### Security

| Security Schema | Scopes |
| --- | --- |
| OAuth2 | |

### /promo/{promoUUID}/statistics

#### GET
##### Summary:

Отримання статистики по розсилці

##### Description:

Запит повертає агреговану статистику для послуги "Розсилка SMS на свій список клієнтів".

##### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| promoUUID | path |  | Yes | string (uuid) |

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Статистика розсилки |

##### Security

| Security Schema | Scopes |
| --- | --- |
| OAuth2 | |
