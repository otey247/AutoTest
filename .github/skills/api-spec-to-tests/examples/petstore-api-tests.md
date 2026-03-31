# Full Example: Petstore API Test Derivation

This example demonstrates deriving a complete test suite from a Petstore-style OpenAPI 3.0 specification. It walks through the spec fragment, the derived tests, and the reasoning behind each test.

---

## Source Spec (Condensed)

```yaml
openapi: 3.0.3
info:
  title: Petstore API
  version: 1.0.0
servers:
  - url: https://api.petstore.example.com/v1

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer

  schemas:
    Pet:
      type: object
      required: [id, name, status]
      properties:
        id:
          type: integer
          format: int64
          readOnly: true
        name:
          type: string
          minLength: 1
          maxLength: 100
        tag:
          type: string
          nullable: true
        status:
          type: string
          enum: [available, pending, sold]
      additionalProperties: false

    PetInput:
      type: object
      required: [name, status]
      properties:
        name:
          type: string
          minLength: 1
          maxLength: 100
        tag:
          type: string
          nullable: true
        status:
          type: string
          enum: [available, pending, sold]

    PetList:
      type: object
      properties:
        items:
          type: array
          items:
            $ref: '#/components/schemas/Pet'
        nextCursor:
          type: string
          nullable: true

    Error:
      type: object
      required: [code, message]
      properties:
        code:
          type: string
        message:
          type: string

security:
  - bearerAuth: []

paths:
  /pets:
    get:
      operationId: listPets
      summary: List all pets
      parameters:
        - name: limit
          in: query
          schema:
            type: integer
            minimum: 1
            maximum: 100
            default: 20
        - name: status
          in: query
          schema:
            type: string
            enum: [available, pending, sold]
        - name: cursor
          in: query
          schema:
            type: string
      responses:
        '200':
          description: A list of pets
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PetList'
        '400':
          description: Invalid query parameters
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
        '401':
          description: Unauthorized

    post:
      operationId: createPet
      summary: Create a pet
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/PetInput'
      responses:
        '201':
          description: Pet created
          headers:
            Location:
              schema:
                type: string
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Pet'
        '400':
          description: Invalid input
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
        '401':
          description: Unauthorized
        '409':
          description: Pet with same name already exists
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'

  /pets/{petId}:
    parameters:
      - name: petId
        in: path
        required: true
        schema:
          type: integer
          format: int64
    get:
      operationId: getPetById
      summary: Get a pet by ID
      responses:
        '200':
          description: A pet
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Pet'
        '401':
          description: Unauthorized
        '404':
          description: Pet not found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'

    put:
      operationId: updatePet
      summary: Update a pet
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/PetInput'
      responses:
        '200':
          description: Updated pet
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Pet'
        '400':
          description: Invalid input
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
        '401':
          description: Unauthorized
        '404':
          description: Pet not found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'

    delete:
      operationId: deletePet
      summary: Delete a pet
      responses:
        '204':
          description: Pet deleted
        '401':
          description: Unauthorized
        '404':
          description: Pet not found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
```

---

## Derived Test Suite

### Endpoint: GET /pets (listPets)

#### Test 1: List pets — 200 default parameters
- **Preconditions**: At least 1 pet exists; valid bearer token
- **Request**: `GET /pets` with `Authorization: Bearer <token>`
- **Expected**: Status 200
- **Assertions**:
  - Body matches PetList schema
  - `items` is an array where each element matches Pet schema
  - Default limit applied (at most 20 items returned)
  - `Content-Type` is `application/json`

#### Test 2: List pets — 200 with status filter
- **Preconditions**: Pets with various statuses exist; valid bearer token
- **Request**: `GET /pets?status=available`
- **Expected**: Status 200
- **Assertions**:
  - Every pet in `items` has `status: "available"`

#### Test 3: List pets — 200 with limit parameter
- **Preconditions**: More than 5 pets exist; valid bearer token
- **Request**: `GET /pets?limit=5`
- **Expected**: Status 200
- **Assertions**:
  - `items` array has at most 5 elements
  - `nextCursor` is non-null (more results available)

#### Test 4: List pets — 200 with cursor pagination
- **Preconditions**: More than 5 pets exist; valid bearer token
- **Request**: `GET /pets?limit=5` to get first page, then `GET /pets?limit=5&cursor=<nextCursor>`
- **Expected**: Status 200 on both requests
- **Assertions**:
  - Second page items are different from first page items
  - No duplicates across pages

#### Test 5: List pets — 200 empty result
- **Preconditions**: No pets with status "sold" exist; valid bearer token
- **Request**: `GET /pets?status=sold`
- **Expected**: Status 200
- **Assertions**:
  - `items` is an empty array
  - `nextCursor` is null

#### Test 6: List pets — 400 invalid limit (below minimum)
- **Preconditions**: Valid bearer token
- **Request**: `GET /pets?limit=0`
- **Expected**: Status 400
- **Assertions**:
  - Body matches Error schema
  - Error message references the `limit` parameter

#### Test 7: List pets — 400 invalid limit (above maximum)
- **Preconditions**: Valid bearer token
- **Request**: `GET /pets?limit=999`
- **Expected**: Status 400
- **Assertions**:
  - Body matches Error schema

#### Test 8: List pets — 400 invalid status enum
- **Preconditions**: Valid bearer token
- **Request**: `GET /pets?status=unknown`
- **Expected**: Status 400
- **Assertions**:
  - Body matches Error schema
  - Error references the invalid `status` value

#### Test 9: List pets — 401 no token
- **Request**: `GET /pets` with no Authorization header
- **Expected**: Status 401
- **Assertions**:
  - No pet data in response body

---

### Endpoint: POST /pets (createPet)

#### Test 10: Create pet — 201 with valid input
- **Preconditions**: Valid bearer token
- **Request**: `POST /pets` with body `{ "name": "Fido", "status": "available" }`
- **Expected**: Status 201
- **Assertions**:
  - Body matches Pet schema
  - `id` field is present (server-generated)
  - `name` equals "Fido"
  - `status` equals "available"
  - `Location` header contains URL to the new pet
  - Follow `Location` with GET; verify it returns the same pet

#### Test 11: Create pet — 201 with optional tag
- **Preconditions**: Valid bearer token
- **Request**: `POST /pets` with body `{ "name": "Luna", "status": "pending", "tag": "cat" }`
- **Expected**: Status 201
- **Assertions**:
  - `tag` equals "cat" in response

#### Test 12: Create pet — 201 with null tag
- **Preconditions**: Valid bearer token
- **Request**: `POST /pets` with body `{ "name": "Rex", "status": "available", "tag": null }`
- **Expected**: Status 201
- **Assertions**:
  - `tag` is null in response

#### Test 13: Create pet — 400 missing required field (name)
- **Preconditions**: Valid bearer token
- **Request**: `POST /pets` with body `{ "status": "available" }`
- **Expected**: Status 400
- **Assertions**:
  - Body matches Error schema
  - Error message references missing `name` field

#### Test 14: Create pet — 400 missing required field (status)
- **Preconditions**: Valid bearer token
- **Request**: `POST /pets` with body `{ "name": "Buddy" }`
- **Expected**: Status 400
- **Assertions**:
  - Error message references missing `status` field

#### Test 15: Create pet — 400 invalid status enum
- **Preconditions**: Valid bearer token
- **Request**: `POST /pets` with body `{ "name": "Max", "status": "lost" }`
- **Expected**: Status 400
- **Assertions**:
  - Error references invalid `status` value

#### Test 16: Create pet — 400 name too long
- **Preconditions**: Valid bearer token
- **Request**: `POST /pets` with body `{ "name": "<101 character string>", "status": "available" }`
- **Expected**: Status 400
- **Assertions**:
  - Error references `name` maxLength constraint

#### Test 17: Create pet — 400 empty name
- **Preconditions**: Valid bearer token
- **Request**: `POST /pets` with body `{ "name": "", "status": "available" }`
- **Expected**: Status 400
- **Assertions**:
  - Error references `name` minLength constraint

#### Test 18: Create pet — 400 extra unknown field (additionalProperties: false)
- **Preconditions**: Valid bearer token
- **Request**: `POST /pets` with body `{ "name": "Spot", "status": "available", "breed": "dalmatian" }`
- **Expected**: Status 400 (or field silently stripped — verify per API behavior)

#### Test 19: Create pet — 400 malformed JSON
- **Preconditions**: Valid bearer token
- **Request**: `POST /pets` with body `{ "name": "Buddy", "status": ` (truncated JSON)
- **Expected**: Status 400

#### Test 20: Create pet — 401 no token
- **Request**: `POST /pets` with valid body, no Authorization header
- **Expected**: Status 401

#### Test 21: Create pet — 409 duplicate name
- **Preconditions**: Pet named "Fido" already exists; valid bearer token
- **Request**: `POST /pets` with body `{ "name": "Fido", "status": "available" }`
- **Expected**: Status 409
- **Assertions**:
  - Body matches Error schema
  - Error indicates conflict reason

---

### Endpoint: GET /pets/{petId} (getPetById)

#### Test 22: Get pet — 200 valid ID
- **Preconditions**: Pet with known ID exists; valid bearer token
- **Request**: `GET /pets/{petId}`
- **Expected**: Status 200
- **Assertions**:
  - Body matches Pet schema exactly (id, name, status present and correctly typed)
  - No extra fields beyond schema (additionalProperties: false)

#### Test 23: Get pet — 401 no token
- **Request**: `GET /pets/1` with no Authorization header
- **Expected**: Status 401

#### Test 24: Get pet — 404 non-existent ID
- **Preconditions**: Valid bearer token
- **Request**: `GET /pets/999999999`
- **Expected**: Status 404
- **Assertions**:
  - Body matches Error schema
  - No stack traces or internal details

#### Test 25: Get pet — 400 invalid ID type
- **Preconditions**: Valid bearer token
- **Request**: `GET /pets/not-a-number`
- **Expected**: Status 400 (invalid path parameter type)

---

### Endpoint: PUT /pets/{petId} (updatePet)

#### Test 26: Update pet — 200 valid update
- **Preconditions**: Pet with known ID exists; valid bearer token
- **Request**: `PUT /pets/{petId}` with body `{ "name": "Fido Updated", "status": "sold" }`
- **Expected**: Status 200
- **Assertions**:
  - Body matches Pet schema
  - `name` and `status` reflect the update
  - `id` remains the same

#### Test 27: Update pet — 200 idempotent (repeat same update)
- **Preconditions**: Pet exists; valid bearer token
- **Request**: Send the same PUT request twice
- **Expected**: Status 200 both times
- **Assertions**:
  - Response is identical both times
  - No side effects from repeated call

#### Test 28: Update pet — 400 invalid input
- **Preconditions**: Pet exists; valid bearer token
- **Request**: `PUT /pets/{petId}` with body `{ "name": "", "status": "available" }`
- **Expected**: Status 400
- **Assertions**:
  - Error references `name` minLength

#### Test 29: Update pet — 401 no token
- **Request**: `PUT /pets/{petId}` with valid body, no Authorization header
- **Expected**: Status 401

#### Test 30: Update pet — 404 non-existent ID
- **Preconditions**: Valid bearer token
- **Request**: `PUT /pets/999999999` with valid body
- **Expected**: Status 404

---

### Endpoint: DELETE /pets/{petId} (deletePet)

#### Test 31: Delete pet — 204 success
- **Preconditions**: Pet with known ID exists; valid bearer token
- **Request**: `DELETE /pets/{petId}`
- **Expected**: Status 204
- **Assertions**:
  - Response body is empty
  - Subsequent GET /pets/{petId} returns 404

#### Test 32: Delete pet — 204 idempotent (repeat delete)
- **Preconditions**: Pet was already deleted; valid bearer token
- **Request**: `DELETE /pets/{petId}` again
- **Expected**: Status 404 (or 204 if idempotent — verify per spec)
- **Reasoning**: Some APIs return 404 on re-delete; others return 204 for idempotency. Document which behavior is expected.

#### Test 33: Delete pet — 401 no token
- **Request**: `DELETE /pets/1` with no Authorization header
- **Expected**: Status 401

#### Test 34: Delete pet — 404 non-existent ID
- **Preconditions**: Valid bearer token
- **Request**: `DELETE /pets/999999999`
- **Expected**: Status 404

---

## Coverage Summary

| Endpoint | Happy Path | Validation | Auth | Error | Pagination | Idempotency | Total |
|----------|-----------|------------|------|-------|------------|-------------|-------|
| GET /pets | 5 | 3 | 1 | — | 2 | — | 9 |
| POST /pets | 3 | 5 | 1 | 1 | — | — | 10 |
| GET /pets/{petId} | 1 | 1 | 1 | 1 | — | — | 4 |
| PUT /pets/{petId} | 1 | 1 | 1 | 1 | — | 1 | 5 |
| DELETE /pets/{petId} | 1 | — | 1 | 1 | — | 1 | 4 |
| **Total** | **11** | **10** | **5** | **4** | **2** | **2** | **34** |

### Test Category Ratios

- Happy path: 32% — validates core functionality
- Validation: 29% — catches input handling issues
- Auth: 15% — verifies security enforcement
- Error handling: 12% — confirms error behavior
- Pagination: 6% — validates list navigation
- Idempotency: 6% — confirms safe retry behavior

This ratio is typical for a well-covered CRUD API. APIs with more complex business logic will have a higher proportion of validation and error tests.
