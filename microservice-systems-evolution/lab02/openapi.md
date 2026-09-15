# Опис API мікросервісів за допомогою OpenAPI. Приклад. Опис створення замовлення

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

## 32. Рекомендовані джерела

1. OpenAPI Specification — https://spec.openapis.org/oas/
2. OpenAPI Specification v3.1.1 — https://spec.openapis.org/oas/v3.1.1.html
3. OpenAPI Specification v3.2.1 — https://spec.openapis.org/oas/v3.2.1.html
4. OpenAPI Learn: Structure of an OpenAPI Description — https://learn.openapis.org/specification/structure.html
5. OpenAPI Learn: API Endpoints — https://learn.openapis.org/specification/paths.html
6. Swagger Editor — https://swagger.io/open-source/swagger-editor/
7. Swagger UI — https://swagger.io/open-source/swagger-ui/
8. Spectral — https://stoplight.io/open-source/spectral
