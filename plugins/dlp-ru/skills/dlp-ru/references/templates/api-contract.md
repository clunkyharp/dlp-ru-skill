# Шаблон: контракт API (OpenAPI 3.1)

Скопируйте YAML ниже в файл `docs/api-contract.yml` вашего проекта. Ключи и структура OpenAPI оставлены без изменений (на английском), переведены только описания и заполнители.

Спецификация OpenAPI: https://spec.openapis.org/oas/v3.1.0

```yaml
# [Название сервиса/компонента] API
# Шаблон спецификации OpenAPI
# См.: https://spec.openapis.org/oas/v3.1.0

openapi: 3.1.0

info:
  title: "[Название сервиса] API"
  version: "1.0.0"
  description: |
    [Краткое описание назначения и границ этого API]

servers:
  - url: http://localhost:3000/api/v1
    description: Локальная разработка
  # - url: https://api.example.com/v1
  #   description: Production

paths:
  /resource:
    get:
      operationId: listResources
      summary: Получить список всех ресурсов
      description: "[Подробное описание операции]"
      tags:
        - Resources
      parameters:
        - name: limit
          in: query
          required: false
          schema:
            type: integer
            default: 20
            maximum: 100
          description: Максимальное число возвращаемых элементов
        - name: offset
          in: query
          required: false
          schema:
            type: integer
            default: 0
          description: Число пропускаемых элементов
      responses:
        "200":
          description: Успешный ответ
          content:
            application/json:
              schema:
                type: object
                properties:
                  data:
                    type: array
                    items:
                      $ref: "#/components/schemas/Resource"
                  total:
                    type: integer
        "401":
          $ref: "#/components/responses/Unauthorized"

    post:
      operationId: createResource
      summary: Создать новый ресурс
      description: "[Подробное описание]"
      tags:
        - Resources
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/ResourceCreate"
      responses:
        "201":
          description: Ресурс создан
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Resource"
        "400":
          $ref: "#/components/responses/BadRequest"
        "401":
          $ref: "#/components/responses/Unauthorized"
        "422":
          $ref: "#/components/responses/UnprocessableEntity"

  /resource/{id}:
    parameters:
      - name: id
        in: path
        required: true
        schema:
          type: string
          format: uuid
        description: Идентификатор ресурса

    get:
      operationId: getResource
      summary: Получить ресурс по ID
      tags:
        - Resources
      responses:
        "200":
          description: Успешный ответ
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Resource"
        "401":
          $ref: "#/components/responses/Unauthorized"
        "404":
          $ref: "#/components/responses/NotFound"

    put:
      operationId: updateResource
      summary: Обновить ресурс
      tags:
        - Resources
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/ResourceUpdate"
      responses:
        "200":
          description: Ресурс обновлён
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Resource"
        "400":
          $ref: "#/components/responses/BadRequest"
        "401":
          $ref: "#/components/responses/Unauthorized"
        "404":
          $ref: "#/components/responses/NotFound"

    delete:
      operationId: deleteResource
      summary: Удалить ресурс
      tags:
        - Resources
      responses:
        "204":
          description: Ресурс удалён
        "401":
          $ref: "#/components/responses/Unauthorized"
        "404":
          $ref: "#/components/responses/NotFound"

components:
  schemas:
    Resource:
      type: object
      required:
        - id
        - name
        - created_at
        - updated_at
      properties:
        id:
          type: string
          format: uuid
          description: Уникальный идентификатор
        name:
          type: string
          description: Название ресурса
        description:
          type:
            - string
            - "null"
          description: Необязательное описание
        created_at:
          type: string
          format: date-time
          description: Временная метка создания
        updated_at:
          type: string
          format: date-time
          description: Временная метка последнего обновления

    ResourceCreate:
      type: object
      required:
        - name
      properties:
        name:
          type: string
          minLength: 1
          maxLength: 255
        description:
          type:
            - string
            - "null"

    ResourceUpdate:
      type: object
      properties:
        name:
          type: string
          minLength: 1
          maxLength: 255
        description:
          type:
            - string
            - "null"

    Error:
      type: object
      required:
        - code
        - message
      properties:
        code:
          type: string
          description: Код ошибки
        message:
          type: string
          description: Сообщение об ошибке, понятное человеку
        field:
          type:
            - string
            - "null"
          description: Поле, вызвавшее ошибку (для ошибок валидации)

  responses:
    BadRequest:
      description: Некорректный запрос
      content:
        application/json:
          schema:
            $ref: "#/components/schemas/Error"

    Unauthorized:
      description: Требуется аутентификация
      content:
        application/json:
          schema:
            $ref: "#/components/schemas/Error"

    NotFound:
      description: Ресурс не найден
      content:
        application/json:
          schema:
            $ref: "#/components/schemas/Error"

    UnprocessableEntity:
      description: Запрос понятен, но не может быть обработан
      content:
        application/json:
          schema:
            $ref: "#/components/schemas/Error"

  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

security:
  - bearerAuth: []

tags:
  - name: Resources
    description: "[Описание ресурса]"
```
