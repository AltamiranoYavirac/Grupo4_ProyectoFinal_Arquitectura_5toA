# Interfaces y Contratos (APIs)

Esta sección especifica las interfaces expuestas por el Backend Serverless, encargado de sincronizar datos, gestionar backups y proveer acceso autenticado a los recursos de la aplicación.
Todas las APIs siguen un estilo REST, protegidas mediante token JWT / ID Token del proveedor de autenticación (Firebase Auth / AWS Cognito).

## Inventario de APIs (Resumen de Endpoints)

| Método     | Endpoint              | Descripción                                                                                             | Rol permitido |
| ---------- | --------------------- | ------------------------------------------------------------------------------------------------------- | ------------- |
| **POST**   | `/auth/login`         | Valida credenciales y devuelve un token de acceso.                                                      | Usuario       |
| **POST**   | `/sync/pull`          | Envía desde la nube hacia la app la última versión sincronizada de medicamentos, recordatorios y citas. | Usuario       |
| **POST**   | `/sync/push`          | Recibe desde la app los cambios generados offline y actualiza la copia en la nube.                      | Usuario       |
| **GET**    | `/medicamentos`       | Obtiene la lista de medicamentos sincronizados.                                                         | Usuario       |
| **POST**   | `/medicamentos`       | Crea o actualiza un medicamento del usuario.                                                            | Usuario       |
| **DELETE** | `/medicamentos/{id}`  | Elimina un medicamento.                                                                                 | Usuario       |
| **GET**    | `/recordatorios`      | Obtiene los recordatorios sincronizados.                                                                | Usuario       |
| **POST**   | `/recordatorios`      | Crea/edita un recordatorio sincronizado.                                                                | Usuario       |
| **DELETE** | `/recordatorios/{id}` | Elimina un recordatorio.                                                                                | Usuario       |
| **POST**   | `/backup/export`      | Genera un backup remoto completo del usuario.                                                           | Usuario       |
| **POST**   | `/backup/import`      | Restaura el backup remoto más reciente.                                                                 | Usuario       |


## Documentación de Contrato (OpenAPI/Swagger – Recurso Principal: /sync/push)

A continuación se presenta una especificación formal de uno de los recursos críticos del sistema:
la sincronización de datos desde la aplicación móvil hacia la nube.

Este es el recurso más importante porque:

- Permite el funcionamiento offline-first,

- Recibe actualizaciones desde SQLite y las integra en la base de datos cloud,

- Evita conflictos mediante control de versiones.

**OpenAPI 3.0 – Endpoint /sync/push**

API de Sincronización – App de Gestión de Medicinas

```
openapi: 3.0.1
info:
  title: API - Aplicación de Gestión de Medicinas
  description: >
    Documentación de la API utilizada por la aplicación móvil para
    sincronización, gestión de medicinas y recordatorios.
  version: "1.0.0"

servers:
  - url: https://api.medicapp.com
    description: Producción
  - url: https://staging.api.medicapp.com
    description: Staging

tags:
  - name: Auth
    description: Autenticación de usuario
  - name: Medicamentos
    description: CRUD de medicamentos
  - name: Recordatorios
    description: CRUD de recordatorios
  - name: Sincronización
    description: Endpoints para funcionamiento offline

paths:
  /auth/login:
    post:
      tags:
        - Auth
      summary: Inicia sesión y devuelve un token JWT.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                email:
                  type: string
                  example: usuario@ejemplo.com
                password:
                  type: string
                  example: "123456"
      responses:
        "200":
          description: Credenciales válidas.
          content:
            application/json:
              schema:
                type: object
                properties:
                  token:
                    type: string
                    description: Token de autenticación JWT.
                    example: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
        "401":
          description: Credenciales inválidas.

  /medicamentos:
    get:
      tags:
        - Medicamentos
      summary: Obtiene todos los medicamentos sincronizados del usuario.
      security:
        - bearerAuth: []
      responses:
        "200":
          description: Lista de medicamentos.
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: "#/components/schemas/Medicamento"

    post:
      tags:
        - Medicamentos
      summary: Crea o actualiza un medicamento.
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/Medicamento"
      responses:
        "201":
          description: Medicamento creado/actualizado.
        "400":
          description: Datos inválidos.

  /medicamentos/{id}:
    delete:
      tags:
        - Medicamentos
      summary: Elimina un medicamento.
      security:
        - bearerAuth: []
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
      responses:
        "204":
          description: Eliminado con éxito.
        "404":
          description: No encontrado.

  /recordatorios:
    get:
      tags:
        - Recordatorios
      summary: Obtiene los recordatorios sincronizados.
      security:
        - bearerAuth: []
      responses:
        "200":
          description: Lista de recordatorios.
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: "#/components/schemas/Recordatorio"

    post:
      tags:
        - Recordatorios
      summary: Crea o actualiza un recordatorio.
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/Recordatorio"
      responses:
        "201":
          description: Recordatorio creado/actualizado.

  /sync/push:
    post:
      tags:
        - Sincronización
      summary: Envía desde la app los cambios locales hacia la nube.
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                lastSync:
                  type: string
                  format: date-time
                medicamentos:
                  type: array
                  items:
                    $ref: "#/components/schemas/Medicamento"
                recordatorios:
                  type: array
                  items:
                    $ref: "#/components/schemas/Recordatorio"
      responses:
        "200":
          description: Sincronización exitosa.
          content:
            application/json:
              schema:
                type: object
                properties:
                  appliedChanges:
                    type: integer
                    example: 12
                  serverTime:
                    type: string
                    format: date-time

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

  schemas:

    Medicamento:
      type: object
      properties:
        id:
          type: string
          example: "A1B2C3"
        nombre:
          type: string
          example: Paracetamol
        dosis:
          type: string
          example: "500mg"
        frecuencia:
          type: string
          example: "Cada 8 horas"
        updatedAt:
          type: string
          format: date-time
      required:
        - id
        - nombre
        - updatedAt

    Recordatorio:
      type: object
      properties:
        id:
          type: string
          example: "R100"
        hora:
          type: string
          example: "08:00"
        mensaje:
          type: string
          example: "Tomar medicina del desayuno"
        updatedAt:
          type: string
          format: date-time
      required:
        - id
        - hora
        - updatedAt

```
### Segundo Ejemplo de Documentación Swagger (OpenAPI) — App de Gestión de Medicinas

A continuación se muestra un ejemplo realista y completo del archivo openapi.yaml que podrías usar en Swagger UI o en tu backend serverless.

```
openapi: 3.0.1
info:
  title: API - Aplicación de Gestión de Medicinas
  description: >
    Documentación de la API utilizada por la aplicación móvil para
    sincronización, gestión de medicinas y recordatorios.
  version: "1.0.0"

servers:
  - url: https://api.medicapp.com
    description: Producción
  - url: https://staging.api.medicapp.com
    description: Staging

tags:
  - name: Auth
    description: Autenticación de usuario
  - name: Medicamentos
    description: CRUD de medicamentos
  - name: Recordatorios
    description: CRUD de recordatorios
  - name: Sincronización
    description: Endpoints para funcionamiento offline

paths:
  /auth/login:
    post:
      tags:
        - Auth
      summary: Inicia sesión y devuelve un token JWT.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                email:
                  type: string
                  example: usuario@ejemplo.com
                password:
                  type: string
                  example: "123456"
      responses:
        "200":
          description: Credenciales válidas.
          content:
            application/json:
              schema:
                type: object
                properties:
                  token:
                    type: string
                    description: Token de autenticación JWT.
                    example: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
        "401":
          description: Credenciales inválidas.

  /medicamentos:
    get:
      tags:
        - Medicamentos
      summary: Obtiene todos los medicamentos sincronizados del usuario.
      security:
        - bearerAuth: []
      responses:
        "200":
          description: Lista de medicamentos.
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: "#/components/schemas/Medicamento"

    post:
      tags:
        - Medicamentos
      summary: Crea o actualiza un medicamento.
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/Medicamento"
      responses:
        "201":
          description: Medicamento creado/actualizado.
        "400":
          description: Datos inválidos.

  /medicamentos/{id}:
    delete:
      tags:
        - Medicamentos
      summary: Elimina un medicamento.
      security:
        - bearerAuth: []
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
      responses:
        "204":
          description: Eliminado con éxito.
        "404":
          description: No encontrado.

  /recordatorios:
    get:
      tags:
        - Recordatorios
      summary: Obtiene los recordatorios sincronizados.
      security:
        - bearerAuth: []
      responses:
        "200":
          description: Lista de recordatorios.
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: "#/components/schemas/Recordatorio"

    post:
      tags:
        - Recordatorios
      summary: Crea o actualiza un recordatorio.
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/Recordatorio"
      responses:
        "201":
          description: Recordatorio creado/actualizado.

  /sync/push:
    post:
      tags:
        - Sincronización
      summary: Envía desde la app los cambios locales hacia la nube.
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                lastSync:
                  type: string
                  format: date-time
                medicamentos:
                  type: array
                  items:
                    $ref: "#/components/schemas/Medicamento"
                recordatorios:
                  type: array
                  items:
                    $ref: "#/components/schemas/Recordatorio"
      responses:
        "200":
          description: Sincronización exitosa.
          content:
            application/json:
              schema:
                type: object
                properties:
                  appliedChanges:
                    type: integer
                    example: 12
                  serverTime:
                    type: string
                    format: date-time

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

  schemas:

    Medicamento:
      type: object
      properties:
        id:
          type: string
          example: "A1B2C3"
        nombre:
          type: string
          example: Paracetamol
        dosis:
          type: string
          example: "500mg"
        frecuencia:
          type: string
          example: "Cada 8 horas"
        updatedAt:
          type: string
          format: date-time
      required:
        - id
        - nombre
        - updatedAt

    Recordatorio:
      type: object
      properties:
        id:
          type: string
          example: "R100"
        hora:
          type: string
          example: "08:00"
        mensaje:
          type: string
          example: "Tomar medicina del desayuno"
        updatedAt:
          type: string
          format: date-time
      required:
        - id
        - hora
        - updatedAt

```



