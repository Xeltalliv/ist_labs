# Методичні рекомендації

## Опис API мікросервісів за допомогою OpenAPI

---

## 1. Призначення методичних рекомендацій

Ці методичні рекомендації призначені для використання в межах дисципліни **«Інженерія змін у мікросервісних системах»** під час виконання лабораторних робіт, пов’язаних із формалізацією архітектури, описом контрактів між сервісами та аналізом впливу змін.

У попередніх лабораторних роботах студенти описують предметну область, виділяють мікросервіси, визначають їхню відповідальність, дані та взаємодії. Наступним кроком є формальний опис API, оскільки саме API часто є основним контрактом між мікросервісами.

Мета цих рекомендацій — показати, як за допомогою **OpenAPI** описувати REST API мікросервісів так, щоб цей опис можна було використовувати для:

- документування взаємодій між сервісами;
- узгодження контрактів між командами;
- аналізу впливу змін;
- перевірки сумісності нових версій API;
- підготовки до contract testing;
- генерації технічної документації;
- використання API-опису в наступних лабораторних роботах.

У межах курсу не вимагається створювати промислову API-платформу. Основна увага приділяється тому, щоб студенти навчилися **точно описувати API-контракти** та розуміти, як їх зміни впливають на інші сервіси.

---

## 2. Очікувані результати

Після опрацювання методичних рекомендацій студент повинен уміти:

- пояснювати призначення OpenAPI у мікросервісній архітектурі;
- відрізняти API-контракт від реалізації сервісу;
- створювати базовий файл `openapi.yaml`;
- описувати endpoints, HTTP-методи, параметри, request body та responses;
- виносити спільні схеми в `components/schemas`;
- описувати типові помилки API;
- визначати, які зміни API є сумісними, а які — несумісними;
- пов’язувати зміни API з ризиком для інших сервісів;
- використовувати OpenAPI-опис як частину baseline-архітектури системи.

---

## 3. Вхідні умови

Для виконання завдань студенти повинні мати результати попередніх етапів роботи:

- назву та короткий опис мікросервісної системи;
- список сервісів;
- опис відповідальності кожного сервісу;
- опис взаємодій між сервісами;
- обрану зміну, яка буде аналізуватися в межах курсу;
- попередній перелік API або подій, які можуть бути зачеплені цією зміною.

Якщо реалізованого програмного коду ще немає, це не є проблемою. OpenAPI може використовуватися не тільки для документування готового API, а й для **проєктування майбутнього API**.

---

## 4. Коротка теорія

### 4.1. Що таке API-контракт

**API-контракт** — це формальний опис того, як один компонент системи може взаємодіяти з іншим компонентом через програмний інтерфейс.

У мікросервісній системі API-контракт визначає:

- які операції надає сервіс;
- які URL або endpoints доступні;
- які HTTP-методи використовуються;
- які параметри потрібно передати;
- яку структуру має тіло запиту;
- яку структуру має відповідь;
- які помилки можуть виникнути;
- які правила сумісності потрібно зберігати під час змін.

Наприклад, якщо `order-service` викликає `payment-service`, то між ними існує контракт. Якщо `payment-service` змінить формат відповіді, це може зламати `order-service`. Саме тому API потрібно описувати формально.

---

### 4.2. Що таке OpenAPI

**OpenAPI Specification** — це стандарт опису HTTP API у форматі, який зрозумілий і людям, і програмним інструментам.

OpenAPI-опис зазвичай зберігається у файлі:

```text
openapi.yaml
```

або

```text
openapi.json
```

У цьому курсі рекомендовано використовувати формат **YAML**, оскільки він зручніший для читання в навчальних звітах.

OpenAPI дозволяє описати:

- загальну інформацію про API;
- список endpoints;
- параметри запитів;
- структуру request body;
- структуру responses;
- схеми об’єктів даних;
- помилки;
- авторизацію;
- версії API;
- приклади запитів і відповідей.

У цих методичних рекомендаціях приклади наведено для OpenAPI 3.1.x. Для навчальних робіт цього достатньо. Якщо обраний інструмент підтримує новішу версію специфікації, її також можна використовувати, але структура опису API залишається подібною.

---

### 4.3. Чому OpenAPI важливий саме для інженерії змін

У курсі **«Інженерія змін у мікросервісних системах»** OpenAPI потрібен не тільки як документація.

Він використовується як інструмент для аналізу змін.

Наприклад, якщо потрібно додати підтримку промокодів, потрібно зрозуміти:

- чи зміниться endpoint створення замовлення;
- чи потрібно додати нове поле в request body;
- чи зміниться відповідь API;
- чи вплине це на frontend;
- чи вплине це на `payment-service`;
- чи потрібно оновити contract tests;
- чи буде зміна сумісною зі старими клієнтами.

Без формального API-опису ці питання доводиться вирішувати вручну. OpenAPI дозволяє зафіксувати контракт і потім порівнювати його версії.

---

## 5. Основна структура OpenAPI-файлу

Мінімальний OpenAPI-файл має таку структуру:

```yaml
openapi: 3.1.0
info:
  title: Order Service API
  version: 1.0.0
paths: {}
```

Основні розділи:

| Розділ | Призначення |
|---|---|
| `openapi` | Версія специфікації OpenAPI |
| `info` | Загальна інформація про API |
| `servers` | Адреси середовищ, де доступний API |
| `paths` | Список endpoints |
| `components` | Повторно використовувані схеми, параметри, responses |
| `security` | Загальні правила авторизації |
| `tags` | Групування операцій у документації |

Для лабораторної роботи обов’язковими є:

- `openapi`;
- `info`;
- `paths`;
- `components/schemas` для опису основних об’єктів.

---

## 6. Приклад 1. Мінімальний OpenAPI-опис сервісу

Нижче наведено приклад мінімального опису API для `order-service`.

```yaml
openapi: 3.1.0
info:
  title: Order Service API
  description: API for managing customer orders.
  version: 1.0.0

servers:
  - url: http://localhost:8080
    description: Local development server

paths:
  /orders:
    get:
      summary: Get list of orders
      description: Returns a list of customer orders.
      responses:
        "200":
          description: List of orders returned successfully
```

Цей опис ще дуже простий, але він уже містить:

- версію OpenAPI;
- назву API;
- версію API;
- адресу сервера;
- один endpoint;
- один HTTP-метод;
- один response.

---

## 7. Опис endpoint у OpenAPI

Endpoint описується в розділі `paths`.

Наприклад:

```yaml
paths:
  /orders:
    post:
      summary: Create order
      description: Creates a new order for a customer.
      responses:
        "201":
          description: Order created successfully
```

У цьому прикладі:

- `/orders` — шлях;
- `post` — HTTP-метод;
- `summary` — короткий опис;
- `description` — детальніший опис;
- `responses` — можливі відповіді сервера.

У навчальних роботах кожна операція має містити щонайменше:

- `summary`;
- `description`;
- параметри або request body, якщо вони потрібні;
- успішну відповідь;
- одну або кілька помилок.

---

## 8. Приклад 2. Опис створення замовлення

Розглянемо endpoint створення замовлення.

```yaml
openapi: 3.1.0
info:
  title: Order Service API
  version: 1.0.0

servers:
  - url: http://localhost:8080
    description: Local development server

paths:
  /orders:
    post:
      summary: Create order
      description: Creates a new order based on selected products and delivery address.
      operationId: createOrder
      tags:
        - Orders
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateOrderRequest'
            example:
              userId: "user-123"
              items:
                - productId: "product-1"
                  quantity: 2
                - productId: "product-2"
                  quantity: 1
              deliveryAddressId: "address-456"
      responses:
        "201":
          description: Order created successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/OrderResponse'
              example:
                orderId: "order-789"
                userId: "user-123"
                status: "CREATED"
                totalAmount: 1500.00
                currency: "UAH"
        "400":
          description: Invalid request
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        "409":
          description: Order cannot be created because one or more products are unavailable
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'

components:
  schemas:
    CreateOrderRequest:
      type: object
      required:
        - userId
        - items
        - deliveryAddressId
      properties:
        userId:
          type: string
          example: "user-123"
        items:
          type: array
          items:
            $ref: '#/components/schemas/OrderItemRequest'
        deliveryAddressId:
          type: string
          example: "address-456"

    OrderItemRequest:
      type: object
      required:
        - productId
        - quantity
      properties:
        productId:
          type: string
          example: "product-1"
        quantity:
          type: integer
          minimum: 1
          example: 2

    OrderResponse:
      type: object
      properties:
        orderId:
          type: string
          example: "order-789"
        userId:
          type: string
          example: "user-123"
        status:
          type: string
          enum:
            - CREATED
            - PAID
            - CANCELLED
            - COMPLETED
          example: "CREATED"
        totalAmount:
          type: number
          format: double
          example: 1500.00
        currency:
          type: string
          example: "UAH"

    ErrorResponse:
      type: object
      properties:
        code:
          type: string
          example: "INVALID_REQUEST"
        message:
          type: string
          example: "Request contains invalid data"
```

У цьому прикладі вже є повноцінний контракт:

- що потрібно передати для створення замовлення;
- які поля є обов’язковими;
- яку відповідь повертає сервіс;
- які помилки можуть виникнути;
- які схеми використовуються повторно.

---

## 9. Parameters, requestBody та responses

### 9.1. Path parameters

Path parameter використовується, коли значення є частиною URL.

Наприклад:

```yaml
paths:
  /orders/{orderId}:
    get:
      summary: Get order by id
      parameters:
        - name: orderId
          in: path
          required: true
          schema:
            type: string
          example: "order-789"
      responses:
        "200":
          description: Order returned successfully
        "404":
          description: Order not found
```

Тут `orderId` є частиною шляху `/orders/{orderId}`.

---

### 9.2. Query parameters

Query parameter використовується для фільтрації, пошуку, сортування або пагінації.

Наприклад:

```yaml
paths:
  /orders:
    get:
      summary: Get orders
      parameters:
        - name: userId
          in: query
          required: false
          schema:
            type: string
        - name: status
          in: query
          required: false
          schema:
            type: string
            enum:
              - CREATED
              - PAID
              - CANCELLED
              - COMPLETED
        - name: limit
          in: query
          required: false
          schema:
            type: integer
            default: 20
            minimum: 1
            maximum: 100
      responses:
        "200":
          description: Orders returned successfully
```

Приклад URL:

```text
GET /orders?userId=user-123&status=PAID&limit=20
```

---

### 9.3. Request body

`requestBody` використовується для опису тіла запиту, наприклад при `POST`, `PUT` або `PATCH`.

```yaml
requestBody:
  required: true
  content:
    application/json:
      schema:
        $ref: '#/components/schemas/CreateOrderRequest'
```

Важливо: для `GET` запитів тіло запиту зазвичай не використовують. Фільтрацію краще передавати через query parameters.

---

### 9.4. Responses

У кожної операції має бути описано відповіді.

Мінімально потрібно описати:

- одну успішну відповідь;
- типові помилки.

Наприклад:

```yaml
responses:
  "200":
    description: Order returned successfully
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/OrderResponse'
  "404":
    description: Order not found
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/ErrorResponse'
  "500":
    description: Internal server error
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/ErrorResponse'
```

---

## 10. Components та повторне використання схем

Якщо одна структура даних використовується в кількох місцях, її потрібно винести в `components/schemas`.

Поганий варіант — дублювати однакову структуру в кожному endpoint.

Кращий варіант:

```yaml
components:
  schemas:
    ErrorResponse:
      type: object
      required:
        - code
        - message
      properties:
        code:
          type: string
        message:
          type: string
        details:
          type: array
          items:
            type: string
```

Після цього схему можна використовувати через `$ref`:

```yaml
schema:
  $ref: '#/components/schemas/ErrorResponse'
```

У лабораторній роботі студенти повинні винести в `components/schemas` щонайменше:

- основний request object;
- основний response object;
- error response;
- вкладені об’єкти, якщо вони повторюються.

---

## 11. Приклад 3. Опис помилок API

У мікросервісній системі важливо стандартизувати помилки. Якщо кожен сервіс повертає помилки у власному форматі, клієнтам складно їх обробляти.

Рекомендований простий формат помилки:

```yaml
components:
  schemas:
    ErrorResponse:
      type: object
      required:
        - code
        - message
      properties:
        code:
          type: string
          example: "ORDER_NOT_FOUND"
        message:
          type: string
          example: "Order was not found"
        traceId:
          type: string
          example: "9f1c2a3b7d"
        details:
          type: array
          items:
            type: string
          example:
            - "Order with id order-789 does not exist"
```

Приклад використання:

```yaml
paths:
  /orders/{orderId}:
    get:
      summary: Get order by id
      parameters:
        - name: orderId
          in: path
          required: true
          schema:
            type: string
      responses:
        "200":
          description: Order returned successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/OrderResponse'
        "404":
          description: Order not found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
```

Для курсу бажано, щоб усі сервіси мали однакову базову структуру помилки.

---

## 12. OpenAPI у мікросервісній архітектурі

У мікросервісній системі OpenAPI-файл може бути створений для кожного сервісу окремо.

Наприклад:

```text
services/
  user-service/
    openapi.yaml
  catalog-service/
    openapi.yaml
  order-service/
    openapi.yaml
  payment-service/
    openapi.yaml
```

Для лабораторної роботи не обов’язково описувати всі сервіси. Достатньо вибрати 2–3 ключові сервіси, які важливі для обраної зміни.

Наприклад, якщо обрана зміна — **додавання промокодів**, доцільно описати API таких сервісів:

- `order-service`;
- `payment-service`;
- `promo-service`, якщо він додається;
- частково `catalog-service`, якщо промокоди залежать від товарів або категорій.

---

## 13. Приклад 4. Зміна API для підтримки промокодів

### 13.1. Початкова версія запиту

До зміни створення замовлення виглядає так:

```yaml
CreateOrderRequest:
  type: object
  required:
    - userId
    - items
    - deliveryAddressId
  properties:
    userId:
      type: string
    items:
      type: array
      items:
        $ref: '#/components/schemas/OrderItemRequest'
    deliveryAddressId:
      type: string
```

### 13.2. Сумісна зміна

Потрібно додати підтримку промокодів.

Сумісний варіант:

```yaml
CreateOrderRequest:
  type: object
  required:
    - userId
    - items
    - deliveryAddressId
  properties:
    userId:
      type: string
    items:
      type: array
      items:
        $ref: '#/components/schemas/OrderItemRequest'
    deliveryAddressId:
      type: string
    promoCode:
      type: string
      nullable: true
      description: Optional promo code applied to the order.
```

Чому це сумісна зміна:

- поле `promoCode` не є обов’язковим;
- старі клієнти можуть не передавати це поле;
- старий сценарій створення замовлення залишається працездатним.

---

### 13.3. Несумісна зміна

Поганий варіант:

```yaml
CreateOrderRequest:
  type: object
  required:
    - userId
    - items
    - deliveryAddressId
    - promoCode
  properties:
    userId:
      type: string
    items:
      type: array
      items:
        $ref: '#/components/schemas/OrderItemRequest'
    deliveryAddressId:
      type: string
    promoCode:
      type: string
```

Чому це несумісна зміна:

- `promoCode` став обов’язковим;
- старі клієнти не передають це поле;
- запити старих клієнтів можуть почати отримувати помилку `400 Bad Request`.

---

## 14. Сумісні та несумісні зміни API

### 14.1. Приклади сумісних змін

Зазвичай сумісними є такі зміни:

- додавання нового необов’язкового поля в request body;
- додавання нового поля у response body, якщо клієнти ігнорують невідомі поля;
- додавання нового endpoint;
- додавання нового необов’язкового query parameter;
- додавання нового значення, яке не ламає існуючу бізнес-логіку;
- розширення опису помилок без зміни базової структури.

Приклад:

```yaml
properties:
  discountAmount:
    type: number
    format: double
    description: Discount applied to the order.
```

Якщо це поле просто додається у відповідь і старі клієнти його ігнорують, така зміна може бути сумісною.

---

### 14.2. Приклади несумісних змін

Зазвичай несумісними є такі зміни:

- видалення поля з request або response;
- перейменування поля;
- зміна типу поля;
- перетворення необов’язкового поля на обов’язкове;
- зміна значення enum без збереження старих значень;
- видалення endpoint;
- зміна HTTP-методу;
- зміна структури помилки;
- зміна семантики існуючого поля.

Приклад несумісної зміни:

```yaml
# Було
amount:
  type: number

# Стало
amount:
  type: string
```

Навіть якщо назва поля залишилася такою самою, клієнт може зламатися через зміну типу.

---

## 15. Версіонування API

Під час еволюції API потрібно визначити, як підтримувати різні версії.

Можливі підходи:

1. Версія в URL:

```text
/api/v1/orders
/api/v2/orders
```

2. Версія в заголовку:

```text
Accept: application/vnd.company.orders.v1+json
```

3. Версія в OpenAPI-документі:

```yaml
info:
  version: 1.0.0
```

Для навчальних робіт рекомендовано використовувати простий варіант із версією в URL або окремими файлами:

```text
openapi-v1.yaml
openapi-v2.yaml
```

Наприклад:

```text
order-service/
  openapi-v1.yaml
  openapi-v2.yaml
```

Це дозволяє студентам явно порівняти, що змінилося між версіями контракту.

---

## 16. Приклад 5. Порівняння двох версій API

### Версія 1.0

```yaml
OrderResponse:
  type: object
  properties:
    orderId:
      type: string
    status:
      type: string
    totalAmount:
      type: number
```

### Версія 1.1

```yaml
OrderResponse:
  type: object
  properties:
    orderId:
      type: string
    status:
      type: string
    totalAmount:
      type: number
    discountAmount:
      type: number
    promoCode:
      type: string
      nullable: true
```

Це може бути сумісною зміною, якщо:

- старі поля не видалені;
- типи старих полів не змінені;
- нові поля не стають обов’язковими для старих клієнтів;
- клієнти коректно ігнорують невідомі поля.

---

## 17. Рекомендований обсяг OpenAPI-опису для лабораторної роботи

Для однієї бригади студентів достатньо описати:

- 2–3 сервіси;
- мінімум 5 endpoints загалом;
- мінімум 3 request/response схеми;
- мінімум 1 стандартну схему помилки;
- мінімум 1 приклад сумісної зміни;
- мінімум 1 приклад несумісної зміни;
- мінімум 1 висновок щодо впливу обраної зміни на API.

Не потрібно описувати весь API системи. Завдання полягає не в кількості endpoints, а в якості формалізації контрактів.

---

## 18. Завдання для студентів

### Завдання 1. Вибрати сервіси для опису API

На основі архітектури, сформованої в попередніх лабораторних роботах, потрібно вибрати 2–3 сервіси, API яких буде описано.

Обирати потрібно не випадкові сервіси, а ті, які:

- беруть участь у ключових бізнес-сценаріях;
- мають взаємодії з іншими сервісами;
- можуть бути зачеплені обраною зміною;
- мають API, важливе для інших компонентів системи.

Результат:

- список вибраних сервісів;
- коротке пояснення, чому вибрано саме ці сервіси.

---

### Завдання 2. Створити OpenAPI-файл для одного основного сервісу

Потрібно створити файл:

```text
openapi.yaml
```

У ньому потрібно описати API одного основного сервісу.

Файл має містити:

- `openapi`;
- `info`;
- `servers`;
- `paths`;
- `components/schemas`;
- мінімум 3 endpoints;
- мінімум 2 схеми даних;
- мінімум 1 схему помилки.

Результат:

- файл `openapi.yaml`;
- коротке пояснення його структури у звіті.

---

### Завдання 3. Описати ключові endpoints

Для кожного endpoint потрібно описати:

- призначення;
- HTTP-метод;
- шлях;
- параметри;
- request body, якщо є;
- responses;
- можливі помилки;
- приклад запиту або відповіді.

Рекомендований мінімум:

| Тип операції | Приклад |
|---|---|
| Створення ресурсу | `POST /orders` |
| Отримання ресурсу | `GET /orders/{orderId}` |
| Отримання списку | `GET /orders` |
| Оновлення ресурсу | `PATCH /orders/{orderId}` |
| Скасування або видалення | `DELETE /orders/{orderId}` або `POST /orders/{orderId}/cancel` |

Результат:

- формально описані endpoints у OpenAPI;
- короткий опис операцій у звіті.

---

### Завдання 4. Описати схеми даних

Потрібно описати основні request/response об’єкти в `components/schemas`.

Наприклад:

- `CreateOrderRequest`;
- `OrderResponse`;
- `OrderItemRequest`;
- `ErrorResponse`.

Для кожної схеми потрібно вказати:

- тип об’єкта;
- обов’язкові поля;
- типи полів;
- обмеження, якщо вони є;
- приклади значень.

Результат:

- структурований розділ `components/schemas`;
- відсутність дублювання однакових схем у різних endpoint.

---

### Завдання 5. Додати опис помилок

Потрібно описати типові помилки API.

Мінімально:

- `400 Bad Request`;
- `404 Not Found`;
- `409 Conflict` або інша бізнес-помилка;
- `500 Internal Server Error`.

Для кожної помилки потрібно вказати:

- HTTP-код;
- опис;
- формат відповіді;
- приклад.

Результат:

- опис помилок у OpenAPI;
- однакова схема помилки для всіх endpoints.

---

### Завдання 6. Пов’язати API з обраною зміною

Потрібно взяти зміну, яку бригада обрала в попередніх лабораторних роботах, і визначити, як вона впливає на API.

Потрібно відповісти:

- які endpoints можуть змінитися;
- які request-схеми можуть змінитися;
- які response-схеми можуть змінитися;
- чи потрібно додати новий endpoint;
- чи буде зміна сумісною;
- які клієнти або сервіси можуть бути зачеплені.

Результат:

- таблиця впливу зміни на API.

Приклад таблиці:

| Компонент API | Поточний стан | Зміна | Тип зміни | Ризик |
|---|---|---|---|---|
| `POST /orders` | створення замовлення без промокоду | додати `promoCode` | сумісна, якщо поле необов’язкове | середній |
| `OrderResponse` | повертає `totalAmount` | додати `discountAmount` | сумісна | низький |
| `PaymentRequest` | приймає суму до оплати | враховувати знижку | зміна бізнес-семантики | високий |

---

### Завдання 7. Показати приклад сумісної та несумісної зміни

Потрібно на основі власного API показати:

1. Одну сумісну зміну.
2. Одну несумісну зміну.

Для кожної зміни потрібно пояснити:

- що саме змінюється;
- чому це сумісна або несумісна зміна;
- які сервіси або клієнти можуть бути зачеплені;
- як зменшити ризик.

Результат:

- порівняння двох варіантів зміни API;
- висновок щодо сумісності.

---

### Завдання 8. Перевірити OpenAPI-файл у безкоштовному інструменті

Потрібно перевірити, чи коректно написаний `openapi.yaml`.

Можна використати один із безкоштовних інструментів:

- Swagger Editor;
- Swagger UI локально через Docker;
- Spectral CLI;
- плагіни OpenAPI для VS Code;
- інші безкоштовні або open-source інструменти.

Потрібно додати до звіту:

- скриншот або короткий опис перевірки;
- список знайдених помилок, якщо вони були;
- пояснення, як ці помилки виправлено.

---

## 19. Приклад повного фрагмента `openapi.yaml`

Нижче наведено навчальний фрагмент OpenAPI-опису, який можна використати як орієнтир.

```yaml
openapi: 3.1.0
info:
  title: Order Service API
  description: API for order lifecycle management in a microservice system.
  version: 1.0.0

servers:
  - url: http://localhost:8080
    description: Local development server

tags:
  - name: Orders
    description: Operations related to customer orders

paths:
  /orders:
    post:
      tags:
        - Orders
      summary: Create order
      description: Creates a new order for the selected products.
      operationId: createOrder
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateOrderRequest'
      responses:
        "201":
          description: Order created successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/OrderResponse'
        "400":
          description: Invalid request
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        "409":
          description: Business conflict
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'

    get:
      tags:
        - Orders
      summary: Get orders
      description: Returns a filtered list of orders.
      operationId: getOrders
      parameters:
        - name: userId
          in: query
          required: false
          schema:
            type: string
        - name: status
          in: query
          required: false
          schema:
            type: string
            enum:
              - CREATED
              - PAID
              - CANCELLED
              - COMPLETED
        - name: limit
          in: query
          required: false
          schema:
            type: integer
            default: 20
            minimum: 1
            maximum: 100
      responses:
        "200":
          description: Orders returned successfully
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/OrderResponse'

  /orders/{orderId}:
    get:
      tags:
        - Orders
      summary: Get order by id
      description: Returns order details by identifier.
      operationId: getOrderById
      parameters:
        - name: orderId
          in: path
          required: true
          schema:
            type: string
      responses:
        "200":
          description: Order returned successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/OrderResponse'
        "404":
          description: Order not found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'

components:
  schemas:
    CreateOrderRequest:
      type: object
      required:
        - userId
        - items
        - deliveryAddressId
      properties:
        userId:
          type: string
          example: "user-123"
        items:
          type: array
          minItems: 1
          items:
            $ref: '#/components/schemas/OrderItemRequest'
        deliveryAddressId:
          type: string
          example: "address-456"
        promoCode:
          type: string
          nullable: true
          description: Optional promo code. This field is added as a backward-compatible change.
          example: "SALE10"

    OrderItemRequest:
      type: object
      required:
        - productId
        - quantity
      properties:
        productId:
          type: string
          example: "product-1"
        quantity:
          type: integer
          minimum: 1
          example: 2

    OrderResponse:
      type: object
      properties:
        orderId:
          type: string
          example: "order-789"
        userId:
          type: string
          example: "user-123"
        status:
          type: string
          enum:
            - CREATED
            - PAID
            - CANCELLED
            - COMPLETED
          example: "CREATED"
        totalAmount:
          type: number
          format: double
          example: 1500.00
        discountAmount:
          type: number
          format: double
          example: 150.00
        currency:
          type: string
          example: "UAH"

    ErrorResponse:
      type: object
      required:
        - code
        - message
      properties:
        code:
          type: string
          example: "ORDER_NOT_FOUND"
        message:
          type: string
          example: "Order was not found"
        traceId:
          type: string
          example: "trace-123"
```

---

## 20. Типові помилки студентів

### 20.1. Опис API як звичайного тексту

Погано:

```text
Order service has endpoint for creating orders.
```

Добре:

```yaml
paths:
  /orders:
    post:
      summary: Create order
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateOrderRequest'
      responses:
        "201":
          description: Order created successfully
```

---

### 20.2. Відсутність опису помилок

Погано:

```yaml
responses:
  "200":
    description: Success
```

Проблема: незрозуміло, що буде при некоректному запиті, відсутньому ресурсі або бізнес-конфлікті.

Добре:

```yaml
responses:
  "200":
    description: Success
  "400":
    description: Invalid request
  "404":
    description: Resource not found
  "409":
    description: Business conflict
  "500":
    description: Internal server error
```

---

### 20.3. Відсутність `components/schemas`

Погано, коли однакові структури дублюються в різних endpoint.

Добре — винести спільні об’єкти в `components/schemas` і використовувати `$ref`.

---

### 20.4. Нечіткі назви endpoints

Погано:

```text
POST /doOrder
POST /makePayment
GET /getUserOrders
```

Краще:

```text
POST /orders
POST /payments
GET /users/{userId}/orders
```

---

### 20.5. Змішування відповідальності сервісів

Поганий приклад:

```text
order-service має endpoint /payments і напряму обробляє оплату
```

Краще:

```text
order-service створює замовлення, а payment-service відповідає за оплату
```

У такому разі OpenAPI має відображати межі відповідальності сервісів.

---

### 20.6. Ігнорування впливу зміни

Погано:

```text
Ми додали поле promoCode. Ризиків немає.
```

Краще:

```text
Поле promoCode є необов’язковим, тому зміна request body є сумісною для старих клієнтів. Однак зміна бізнес-логіки розрахунку вартості впливає на order-service і payment-service, тому потрібні додаткові тести для перевірки totalAmount, discountAmount і фінальної суми платежу.
```

---

## 21. Вимоги до результату роботи

Студенти повинні підготувати:

1. Файл `openapi.yaml` для одного основного сервісу.
2. За потреби — додаткові OpenAPI-файли для інших сервісів.
3. Опис вибраних endpoints у звіті.
4. Опис основних схем даних.
5. Опис помилок API.
6. Таблицю впливу обраної зміни на API.
7. Приклад сумісної зміни API.
8. Приклад несумісної зміни API.
9. Результат перевірки OpenAPI-файлу у безкоштовному інструменті.

---

## 22. Рекомендована структура звіту

Звіт може мати таку структуру:

```text
1. Назва системи та обрані сервіси
2. Роль API у взаємодії між сервісами
3. Опис основного сервісу
4. OpenAPI-опис endpoints
5. Опис схем даних
6. Опис помилок
7. Аналіз впливу обраної зміни на API
8. Сумісна та несумісна зміна API
9. Перевірка OpenAPI-файлу
10. Висновки
```

Рекомендований обсяг звіту:

- 4–7 сторінок без урахування повного тексту `openapi.yaml`;
- або Markdown-документ обсягом приблизно 1000–1800 слів.

Файл `openapi.yaml` можна додати окремо або вставити як додаток до звіту.

---

## 23. Критерії оцінювання

Максимальна оцінка — **10 балів**.

| Компонент | Бали |
|---|---:|
| Вибір релевантних сервісів для опису API | 1 |
| Коректна структура `openapi.yaml` | 1.5 |
| Опис мінімум 5 endpoints | 1.5 |
| Опис request/response схем у `components/schemas` | 1.5 |
| Опис помилок API | 1 |
| Аналіз впливу обраної зміни на API | 1.5 |
| Приклад сумісної та несумісної зміни | 1 |
| Перевірка OpenAPI-файлу в інструменті | 0.5 |
| Якість оформлення, логічність і узгодженість | 0.5 |

---

## 24. Контрольні питання

1. Що таке API-контракт?
2. Чим API-контракт відрізняється від реалізації сервісу?
3. Для чого в мікросервісній системі використовується OpenAPI?
4. Які основні розділи має OpenAPI-файл?
5. Для чого потрібен розділ `paths`?
6. Для чого використовується `components/schemas`?
7. Чим path parameter відрізняється від query parameter?
8. Коли потрібно використовувати `requestBody`?
9. Чому потрібно описувати не тільки успішні responses, а й помилки?
10. Які зміни API можна вважати сумісними?
11. Які зміни API є несумісними?
12. Чому додавання обов’язкового поля в request body може зламати клієнтів?
13. Як OpenAPI допомагає під час аналізу впливу змін?
14. Як API-опис може бути використаний у contract testing?
15. Чому в мікросервісній системі API потрібно розглядати як частину архітектури?

---

## 25. Самостійна робота

Для самостійної роботи студентам пропонується:

1. Розширити `openapi.yaml` ще двома endpoints.
2. Додати опис авторизації через Bearer token.
3. Додати стандартну схему пагінації.
4. Додати приклади responses для кожного endpoint.
5. Порівняти дві версії API та визначити, які зміни є breaking changes.
6. Сформувати короткий список правил API design для своєї бригади.

---

## 26. Приклад опису авторизації

Якщо API потребує авторизації, можна описати Bearer token так:

```yaml
components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

security:
  - bearerAuth: []
```

Якщо окремий endpoint є публічним, для нього можна явно перевизначити security:

```yaml
paths:
  /health:
    get:
      summary: Health check
      security: []
      responses:
        "200":
          description: Service is healthy
```

Для лабораторної роботи авторизація не є обов’язковою, але її можна додати як додаткове завдання.

---

## 27. Приклад опису пагінації

Для endpoints, які повертають список ресурсів, бажано описати пагінацію.

```yaml
paths:
  /orders:
    get:
      summary: Get orders
      parameters:
        - name: limit
          in: query
          required: false
          schema:
            type: integer
            default: 20
            minimum: 1
            maximum: 100
        - name: offset
          in: query
          required: false
          schema:
            type: integer
            default: 0
            minimum: 0
      responses:
        "200":
          description: Orders returned successfully
```

Або можна створити окремий response-об’єкт:

```yaml
components:
  schemas:
    OrderPageResponse:
      type: object
      properties:
        items:
          type: array
          items:
            $ref: '#/components/schemas/OrderResponse'
        limit:
          type: integer
        offset:
          type: integer
        total:
          type: integer
```

---

## 28. Зв’язок з наступними лабораторними роботами

OpenAPI-опис, створений у цій роботі, буде використано в наступних темах курсу:

- для побудови графа залежностей між сервісами;
- для визначення affected services;
- для аналізу впливу API-зміни;
- для планування contract testing;
- для оцінки ризику релізу;
- для підготовки плану rollback або roll-forward.

Тому `openapi.yaml` не потрібно сприймати як формальність. Це частина архітектурної baseline-моделі системи.

---

## 29. Рекомендовані безкоштовні інструменти

Для виконання роботи можна використати такі безкоштовні або open-source інструменти:

| Інструмент | Призначення |
|---|---|
| Swagger Editor | редагування та перевірка OpenAPI-опису |
| Swagger UI | візуалізація API-документації |
| Spectral CLI | перевірка OpenAPI-файлу за правилами |
| VS Code + OpenAPI/YAML plugins | локальне редагування файлів |
| Docker | локальний запуск Swagger UI або інших інструментів |
| GitHub/GitLab | зберігання версій API-контрактів |

Використання платних сервісів у межах лабораторної роботи не вимагається.

---

## 30. Приклад локального перегляду Swagger UI через Docker

Якщо у бригади є локальний файл `openapi.yaml`, його можна переглянути через Swagger UI.

Один із простих варіантів — покласти файл поруч із Docker Compose конфігурацією та підняти локальний контейнер.

Приклад `docker-compose.yml`:

```yaml
services:
  swagger-ui:
    image: swaggerapi/swagger-ui
    ports:
      - "8088:8080"
    environment:
      SWAGGER_JSON: /openapi/openapi.yaml
    volumes:
      - ./openapi.yaml:/openapi/openapi.yaml
```

Після запуску:

```bash
docker compose up
```

документація буде доступна локально в браузері:

```text
http://localhost:8088
```

Цей спосіб зручний тим, що не потрібно завантажувати API-опис у сторонні сервіси.

---

## 31. Підсумок

OpenAPI у межах дисципліни **«Інженерія змін у мікросервісних системах»** використовується як засіб формалізації API-контрактів.

Для студента важливо не просто написати YAML-файл, а зрозуміти:

- які сервіси залежать від цього API;
- які поля є частиною контракту;
- які зміни можуть зламати клієнтів;
- як підтримувати сумісність;
- як опис API допомагає аналізувати ризики змін.

Правильно оформлений OpenAPI-файл стає основою для подальшого аналізу архітектури, тестування контрактів, планування релізів і керованої еволюції мікросервісної системи.

---

## 32. Рекомендовані джерела

1. OpenAPI Specification — https://spec.openapis.org/oas/
2. OpenAPI Specification v3.1.1 — https://spec.openapis.org/oas/v3.1.1.html
3. OpenAPI Specification v3.2.1 — https://spec.openapis.org/oas/v3.2.1.html
4. OpenAPI Learn: Structure of an OpenAPI Description — https://learn.openapis.org/specification/structure.html
5. OpenAPI Learn: API Endpoints — https://learn.openapis.org/specification/paths.html
6. Swagger Editor — https://swagger.io/open-source/swagger-editor/
7. Swagger UI — https://swagger.io/open-source/swagger-ui/
8. Spectral — https://stoplight.io/open-source/spectral
