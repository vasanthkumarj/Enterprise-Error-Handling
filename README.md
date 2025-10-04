# Project 01 – REST API with Enterprise Error Handling

## 🎯 Goal
Build a **User Service** with enterprise-grade REST standards.  
Focus on **DTO mapping, input validation, global error handling**, and **traceability**.  
Errors will follow the **RFC 7807 Problem Details** standard, with **correlation IDs** for observability.

---

## 📚 Concepts to Know
Before implementing, make sure you are clear on:
1. **REST Standards** → HTTP verbs, status codes, resource naming.
2. **DTOs** → Use request/response objects, don’t expose entities.
3. **Validation** → Jakarta Bean Validation (`@Valid`, `@NotBlank`, etc.).
4. **Global Error Handling** → Centralized via `@ControllerAdvice`.
5. **Problem Details (RFC 7807)** → Standard JSON format for error responses.
6. **Correlation ID** → Unique ID per request for logs & traceability.

---

## 🛠️ Steps for Implementation

### Step 1: Project Setup
- Create a new Spring Boot project (use **Spring Initializr**).
- Add dependencies:
  - `spring-boot-starter-web`
  - `spring-boot-starter-validation`
  - `lombok` (optional, for reducing boilerplate)
- Define base package `com.example.userservice`.

---

### Step 2: Define Project Structure
Organize code into clean packages:
com.example.userservice
├── controller → REST endpoints
├── dto → Request/response DTOs
├── exception → Custom exceptions + global handler
├── service → Business logic
├── model → Entities (if persistence is needed later)
└── config → Filters, interceptors, etc.


---

### Step 3: Create DTOs
- Define a `UserRequest` for incoming payload.
- Define a `UserResponse` for outgoing payload.
- Use **validation annotations** (`@NotBlank`, `@Email`) inside DTO.

---

### Step 4: Build the Controller
- Create `UserController` with endpoints:
  - `POST /users` → Create a new user.
  - `GET /users/{id}` → Fetch user by ID.
- Accept `UserRequest` with `@Valid`.
- Return `ResponseEntity<UserResponse>` with proper status codes (`201 Created`, `200 OK`).

---

### Step 5: Implement the Service
- Create `UserService` to handle business logic.
- For now, use an **in-memory store** (like `Map<Long, User>`).
- Implement methods:
  - `createUser(UserRequest)`
  - `getUser(Long id)`
- Throw a custom exception (`ResourceNotFoundException`) if user not found.

---

### Step 6: Define Custom Exceptions
- Create meaningful exceptions:
  - `ResourceNotFoundException` → for missing resources.
  - `BusinessException` → for domain-specific rule violations.

---

### Step 7: Implement Global Exception Handler
- Use `@ControllerAdvice` + `@ExceptionHandler`.
- Convert exceptions into **Problem Details JSON** (RFC 7807 format).
- Include:
  - `type` → URI identifying error category.
  - `title` → Human-readable title.
  - `status` → HTTP status.
  - `detail` → Error details.
  - `instance` → Request path.
  - `correlationId` → Unique request identifier.

---

### Step 8: Add Correlation ID
- Implement a **Filter or Interceptor**:
  - Generate a UUID for each request.
  - Store it in request attributes and logs (via `MDC`).
  - Add it into the error response.
- This ensures all logs for a request can be traced.

---

### Step 9: Test the APIs
1. **Valid Request**  
   - `POST /users` with valid JSON → returns `201 Created` + user details.
2. **Not Found**  
   - `GET /users/{invalidId}` → returns `404` with Problem Details JSON.
3. **Validation Error**  
   - `POST /users` with invalid payload → returns `400` with Problem Details JSON.

---

### Step 10: Document Best Practices
- Always include **correlationId** in logs & responses.
- Keep exception hierarchy meaningful.
- Never expose stack traces or DB details to clients.
- Stick to **consistent error format (RFC 7807)** across all APIs.
- Add Swagger/OpenAPI docs for request/response + error responses.

---

## ✅ Expected Outcomes
- A working `User` service with proper **validation** and **error handling**.
- All error responses follow **RFC 7807**.
- Each request is traceable using **correlationId** in logs + response.
- Codebase is clean, extensible, and ready for real-world use.

---

## 📌 Next Steps
- Add **unit tests** for validation & error handling.
- Extend to cover **update/delete APIs**.
- Introduce persistence (JPA + H2/Postgres).
- Integrate with **Swagger/OpenAPI** for API documentation.
