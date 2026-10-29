# 🍔 Online Food Ordering System

**A secure, RESTful backend for managing food catalogs, shopping carts, discounts, and orders.**

Built with Java 21 and Spring Boot, this project provides the backend foundation for an online food ordering platform. It combines JWT-based authentication, role-based authorization, and a layered architecture to separate API handling, business logic, and persistence.

---

## At a Glance

| Component | Technology |
|---|---|
| Language | Java 21 |
| Backend framework | Spring Boot 3.3.4 |
| Database | MySQL 8 |
| Persistence | Spring Data JPA, Hibernate |
| Security | Spring Security, JWT |
| Object mapping | MapStruct |
| Boilerplate reduction | Lombok |
| Build system | Gradle |

## What Can It Do?

### 🛡️ Authentication & Access Control
- Register new user accounts and authenticate existing users.
- Issue JWTs for authenticated requests.
- Enforce role-based access using `USER` and `ADMIN` roles.
- Separate public endpoints from protected resources.

### 🍕 Food Catalog
- Create, retrieve, update, and delete food items.
- Delete multiple food items in a single request.
- Browse food listings with pagination and sorting.

### 🛒 Cart Management
- Add food items and manage their quantities.
- Retrieve cart contents and calculate the cart total.
- Remove individual items, selected items, or the entire cart.

### 📦 Order Processing
- Place an order using the current shopping cart.

### 🏷️ Discount Management
- Create and update discounts.
- Retrieve individual discounts or browse paginated listings.
- Apply discounts to multiple food items.

---

## Architecture

The application follows a layered, ports-and-adapters-inspired structure to keep API concerns, business rules, and database operations separated.

```text
┌─────────────────────────────────────┐
│           REST API Layer            │
│  Controllers · DTOs · Exceptions    │
└──────────────────┬──────────────────┘
                   │
┌──────────────────▼──────────────────┐
│            Domain Layer              │
│ Services · Models · Mappers · Enums  │
└──────────────────┬──────────────────┘
                   │
┌──────────────────▼──────────────────┐
│          Persistence Layer           │
│ Adapters · Repository Ports · JPA    │
└──────────────────┬──────────────────┘
                   │
┌──────────────────▼──────────────────┐
│               MySQL                  │
└─────────────────────────────────────┘
```

**Layer responsibilities**

- **Application:** Handles HTTP requests, request/response DTOs, controllers, and exception handling.
- **Domain:** Contains business logic, domain models, enums, mapping components, and security-related services.
- **Persistence:** Connects domain operations to JPA entities and Spring Data repositories.
- **Configuration:** Wires Spring Security, JWT filters, and supporting beans.

---

## Getting Started

### Requirements

Make sure you have the following installed:

- JDK 21
- MySQL 8.x
- Git

Gradle does not need to be installed separately because the repository includes the Gradle Wrapper.

### 1. Get the source code

```bash
git clone https://github.com/<your-username>/online-food-order-be.git
cd online-food-order-be
```

Replace `<your-username>` with the GitHub account that hosts the repository.

### 2. Create the database

Open MySQL and execute:

```sql
CREATE DATABASE online_food_order;
```

### 3. Configure the application

Edit `src/main/resources/application.properties`:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/online_food_order
spring.datasource.username=${DB_USERNAME:root}
spring.datasource.password=${DB_PASSWORD:root}

spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.jpa.hibernate.ddl-auto=update
```

The username and password above use environment-variable overrides with local-development defaults. Adjust them to match your MySQL setup.

If your project currently has additional database or Hibernate properties, retain those as needed.

### 4. Start the application

**macOS / Linux**

```bash
./gradlew bootRun
```

**Windows**

```bat
gradlew.bat bootRun
```

The server uses port `8080` by default.

### 5. Verify the server

```bash
curl http://localhost:8080/actuator/health
```

Expected response when the application is healthy:

```json
{
  "status": "UP"
}
```

### Build the executable JAR

```bash
./gradlew clean build
```

Run the generated JAR:

```bash
java -jar build/libs/backend-0.0.1-SNAPSHOT.jar
```

---

## API Reference

Base URL:

```text
http://localhost:8080
```

Business APIs use the `/api/v1` prefix. Protected endpoints require a valid JWT in the request header:

```http
Authorization: Bearer <your-token>
```

### Identity & Authentication

Base path: `/api/v1/idm`

| Method | Endpoint | Purpose |
|---|---|---|
| `POST` | `/api/v1/idm/register` | Register an account |
| `POST` | `/api/v1/idm/login` | Authenticate and obtain a JWT |

### Food Items

Base path: `/api/v1/food`

| Method | Endpoint | Purpose |
|---|---|---|
| `POST` | `/api/v1/food` | Create a food item |
| `GET` | `/api/v1/food` | List food items |
| `GET` | `/api/v1/food/{id}` | Retrieve a food item |
| `PUT` | `/api/v1/food/{id}` | Update a food item |
| `DELETE` | `/api/v1/food/{id}` | Delete a food item |
| `DELETE` | `/api/v1/food` | Delete multiple food items |

Supported listing parameters include `page`, `size`, `sortBy`, and `sortDirection`.

Example:

```http
GET /api/v1/food?page=0&size=10&sortBy=createdAt&sortDirection=desc
```

### Shopping Cart

Base path: `/api/v1/cart`

| Method | Endpoint | Purpose |
|---|---|---|
| `PUT` | `/api/v1/cart` | Add a food item |
| `PUT` | `/api/v1/cart/update` | Update a cart item's quantity |
| `GET` | `/api/v1/cart/cart-items` | Retrieve cart items |
| `GET` | `/api/v1/cart/total` | Retrieve the cart total/count |
| `DELETE` | `/api/v1/cart/item/{foodCartId}` | Remove one item |
| `DELETE` | `/api/v1/cart/items` | Remove all items |
| `DELETE` | `/api/v1/cart` | Remove selected items |

### Orders

Base path: `/api/v1/order`

| Method | Endpoint | Purpose |
|---|---|---|
| `POST` | `/api/v1/order` | Place an order from the current cart |

### Discounts

Base path: `/api/v1/discount`

| Method | Endpoint | Purpose |
|---|---|---|
| `POST` | `/api/v1/discount` | Create a discount |
| `GET` | `/api/v1/discount` | List discounts |
| `GET` | `/api/v1/discount/{id}` | Retrieve a discount |
| `PUT` | `/api/v1/discount/{id}` | Update a discount |
| `PUT` | `/api/v1/discount/update-food-items-discounts` | Apply a discount to multiple food items |

### Users

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/api/v1/users/{id}` | Retrieve a user by ID |

### Application Monitoring

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/actuator/health` | Check application health |
| `GET` | `/actuator/info` | Retrieve application information |

Actual access permissions are governed by the application's Spring Security configuration.

---

## Repository Layout

```text
online-food-order-be/
├── src/
│   ├── main/
│   │   ├── java/com/foodorder/backend/
│   │   │   ├── application/
│   │   │   │   ├── controllers/
│   │   │   │   ├── dtos/
│   │   │   │   └── exception/
│   │   │   ├── domain/
│   │   │   │   ├── enums/
│   │   │   │   ├── handlers/
│   │   │   │   ├── mappers/
│   │   │   │   ├── model/
│   │   │   │   ├── providers/
│   │   │   │   └── services/
│   │   │   ├── persistence/
│   │   │   │   ├── adapter/
│   │   │   │   ├── entity/
│   │   │   │   ├── jpa/
│   │   │   │   └── repository/
│   │   │   └── configration/
│   │   └── resources/
│   │       ├── application.properties
│   │       └── application-sqllogs.properties
│   └── test/
├── gradle/
├── build.gradle
├── settings.gradle
├── gradlew
└── README.md
```

---

## Configuration & Security Notes

- Keep database credentials and JWT signing secrets out of version control.
- Use environment variables or a secrets manager for sensitive configuration.
- Use strong, unique JWT signing secrets outside local development.
- Review database schema-management settings before deploying to production.
- Keep dependencies updated and validate access rules for protected endpoints.

Example environment variables:

```bash
export DB_USERNAME="your_mysql_username"
export DB_PASSWORD="your_mysql_password"
```

Ensure your application's JWT configuration also reads its signing secret from a secure external configuration source before production deployment.

---

## License

This project is intended to be distributed under the MIT License, as described in the original project documentation. Ensure a valid `LICENSE` file containing the full license text exists in the repository before publishing it under that license.

---

**Built with Java, Spring Boot, and MySQL.**
