# Secure Library Management System API

A Spring Boot RESTful API for managing a library's book inventory with role-based access control using Spring Security.

## Features

- HTTP Basic Authentication
- Role-Based Access Control (RBAC)
- BCrypt password encoding
- In-memory H2 database
- CRUD operations for book management

## Technology Stack

- Spring Boot 4.0.0
- Spring Security
- Spring Data JPA
- H2 Database
- Lombok
- Java 17

## Security Configuration

### User Roles and Credentials

The application has two pre-configured users:

1. **Librarian (ADMIN)**
   - Username: `librarian`
   - Password: `admin123`
   - Role: `ADMIN`
   - Permissions: Full CRUD access to all book operations

2. **Patron (USER)**
   - Username: `patron`
   - Password: `user123`
   - Role: `USER`
   - Permissions: Read-only access to view books

## API Endpoints

### 1. Create a New Book
- **Endpoint:** `POST /api/books`
- **Required Role:** ADMIN
- **Request Body:**
```json
{
  "title": "The Great Gatsby",
  "author": "F. Scott Fitzgerald",
  "availableCopies": 5
}
```
- **Response:** 201 Created
```json
{
  "id": 1,
  "title": "The Great Gatsby",
  "author": "F. Scott Fitzgerald",
  "availableCopies": 5
}
```

### 2. Get All Books
- **Endpoint:** `GET /api/books`
- **Required Role:** ADMIN or USER
- **Response:** 200 OK
```json
[
  {
    "id": 1,
    "title": "The Great Gatsby",
    "author": "F. Scott Fitzgerald",
    "availableCopies": 5
  }
]
```

### 3. Get Book by ID
- **Endpoint:** `GET /api/books/{id}`
- **Required Role:** ADMIN or USER
- **Response:** 200 OK
```json
{
  "id": 1,
  "title": "The Great Gatsby",
  "author": "F. Scott Fitzgerald",
  "availableCopies": 5
}
```

### 4. Update Book
- **Endpoint:** `PUT /api/books/{id}`
- **Required Role:** ADMIN
- **Request Body:**
```json
{
  "title": "The Great Gatsby - Updated",
  "author": "F. Scott Fitzgerald",
  "availableCopies": 3
}
```
- **Response:** 200 OK

### 5. Delete Book
- **Endpoint:** `DELETE /api/books/{id}`
- **Required Role:** ADMIN
- **Response:** 204 No Content

## Running the Application

### Build the Application
```bash
./gradlew clean build
```

### Run the Application
```bash
./gradlew bootRun
```

The application will start on `http://localhost:8080`

## Testing the API

### Using cURL

#### 1. Create a Book (as Librarian)
```bash
curl -X POST http://localhost:8080/api/books \
  -u librarian:admin123 \
  -H "Content-Type: application/json" \
  -d '{
    "title": "1984",
    "author": "George Orwell",
    "availableCopies": 10
  }'
```

#### 2. Get All Books (as Patron)
```bash
curl -X GET http://localhost:8080/api/books \
  -u patron:user123
```

#### 3. Get Book by ID (as Patron)
```bash
curl -X GET http://localhost:8080/api/books/1 \
  -u patron:user123
```

#### 4. Update a Book (as Librarian)
```bash
curl -X PUT http://localhost:8080/api/books/1 \
  -u librarian:admin123 \
  -H "Content-Type: application/json" \
  -d '{
    "title": "1984 - Updated Edition",
    "author": "George Orwell",
    "availableCopies": 8
  }'
```

#### 5. Delete a Book (as Librarian)
```bash
curl -X DELETE http://localhost:8080/api/books/1 \
  -u librarian:admin123
```

#### 6. Try to Create a Book as Patron (Should Fail - 403 Forbidden)
```bash
curl -X POST http://localhost:8080/api/books \
  -u patron:user123 \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Unauthorized Book",
    "author": "Test Author",
    "availableCopies": 5
  }'
```

### Using Postman

1. Set the authorization type to "Basic Auth"
2. Enter the username and password
3. Set the appropriate HTTP method and endpoint
4. For POST/PUT requests, set Content-Type header to `application/json`
5. Add the request body in JSON format

## H2 Database Console

Access the H2 console at: `http://localhost:8080/h2-console`

**Connection Details:**
- JDBC URL: `jdbc:h2:mem:librarydb`
- Username: `sa`
- Password: (leave empty)

## Project Structure

```
src/main/java/org/ostad/assignment_15/
├── config/
│   └── SecurityConfig.java          # Spring Security configuration
├── controller/
│   └── BookController.java          # REST API endpoints
├── entity/
│   └── Book.java                    # Book entity/model
├── repository/
│   └── BookRepository.java          # JPA repository
├── service/
│   └── BookService.java             # Business logic
└── Assignment15Application.java     # Main application class
```

## Security Features

1. **HTTP Basic Authentication:** All endpoints require authentication
2. **Password Encoding:** Passwords are encrypted using BCryptPasswordEncoder
3. **Role-Based Access Control:**
   - ADMIN role has full access
   - USER role has read-only access
4. **Method-Level Security:** Uses @PreAuthorize annotations
5. **CSRF Protection:** Disabled for REST API (stateless)

## Expected Behavior

| Endpoint | ADMIN (librarian) | USER (patron) |
|----------|-------------------|---------------|
| POST /api/books | ✅ Allowed | ❌ Forbidden (403) |
| GET /api/books | ✅ Allowed | ✅ Allowed |
| GET /api/books/{id} | ✅ Allowed | ✅ Allowed |
| PUT /api/books/{id} | ✅ Allowed | ❌ Forbidden (403) |
| DELETE /api/books/{id} | ✅ Allowed | ❌ Forbidden (403) |

## Notes

- The H2 database is in-memory, so all data is lost when the application stops
- Passwords are securely encoded using BCrypt
- CSRF is disabled as this is a stateless REST API
- The application uses method-level security with @PreAuthorize annotations