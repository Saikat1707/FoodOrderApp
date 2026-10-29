# 🍔 Online Food Ordering System — Backend

A RESTful backend service for an **Online Food Ordering System**, built with **Java** and **Spring Boot**. The system enables administrators to manage a catalog of food items and discounts, while users can browse available food, manage a shopping cart, and place orders.

The application is structured around a clean, layered architecture (controllers → services → persistence adapters → JPA repositories) and secured with **JWT-based authentication** and **role-based authorization**.

---

## 📖 Table of Contents

- [Features](#-features)
- [Tech Stack](#-tech-stack)
- [Prerequisites](#-prerequisites)
- [Getting Started](#-getting-started)
- [Database Setup](#-database-setup)
- [API Endpoints Overview](#-api-endpoints-overview)
- [Project Structure](#-project-structure)
- [License](#-license)

---

## ✨ Features

### 👨‍💼 Admin Features
- **Manage food items** — create, read, update, and delete food items (single or in bulk).
- **Manage discounts** — create and update discounts, and apply a discount to multiple food items at once.
- **Browse catalog** — paginated and sortable listing of all food items.

### 🙋 User Features
- **Register & log in** — account registration and JWT-based authentication.
- **Browse food items** — view available food items with pagination and sorting.
- **Shopping cart** — add items to the cart, update quantities, view cart contents, see the cart total, and remove one, many, or all items.
- **Place orders** — convert the current cart into an order.

### 🔐 Security
- Stateless authentication using **JSON Web Tokens (JWT)**.
- **Role-based access control** with `USER` and `ADMIN` roles.
- Public endpoints (registration, login, health checks) and protected endpoints separated via configurable security paths.

---

## 🛠 Tech Stack

| Category            | Technology                                   |
| ------------------- | -------------------------------------------- |
| Language            | Java 21                                      |
| Framework           | Spring Boot 3.3.4                             |
| Spring Modules      | Spring Web, Spring Data JPA, Spring Security, Spring Actuator |
| Authentication      | JWT (jjwt 0.12.6)                            |
| Database            | MySQL 8 (SQL)                                |
| ORM / Persistence   | Hibernate / JPA                              |
| Object Mapping      | MapStruct 1.5.5                              |
| Boilerplate         | Lombok                                       |
| Build Tool          | Gradle                                       |

---

## 📋 Prerequisites

Before running the project, make sure you have the following installed:

- **Java Development Kit (JDK) 21** or higher
- **MySQL 8.x** server (running locally or remotely)
- **Git**
- *(Optional)* Gradle — the project ships with the **Gradle Wrapper** (`gradlew`), so a local Gradle installation is not required.

---

## 🚀 Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/<your-username>/online-food-order-be.git
cd online-food-order-be
```

### 2. Configure the database

Create the MySQL database and update the credentials in
`src/main/resources/application.properties` (see [Database Setup](#-database-setup) below).

### 3. Build and run

Using the Gradle Wrapper:

```bash
# On macOS / Linux
./gradlew bootRun

# On Windows
gradlew.bat bootRun
```

To build an executable JAR instead:

```bash
./gradlew clean build
java -jar build/libs/backend-0.0.1-SNAPSHOT.jar
```

The application starts on **`http://localhost:8080`** by default.

### 4. Verify it's running

```bash
curl http://localhost:8080/actuator/health
# {"status":"UP"}
```

---

## 🗄 Database Setup

The application connects to a MySQL database named **`online_food_order`**. Hibernate is configured with `ddl-auto=update`, so the schema (tables) is created and updated automatically on startup.

### 1. Create the database

```sql
CREATE DATABASE online_food_order;
```

### 2. Configure the connection

The default configuration lives in `src/main/resources/application.properties`:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/online_food_order
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect
```

Update `spring.datasource.username` and `spring.datasource.password` to match your local MySQL credentials.

> **⚠️ Security note:** The repository ships with a sample JWT secret and database credentials for local development only. **Never commit real secrets.** For production, externalize sensitive values using environment variables or a secrets manager.

---

## 🔌 API Endpoints Overview

All endpoints are prefixed with **`/api/v1`**. Protected endpoints require an `Authorization: Bearer <token>` header.

### 🔐 Authentication (`/api/v1/idm`)

| Method | Endpoint                | Description                          | Auth   |
| ------ | ----------------------- | ------------------------------------ | ------ |
| `POST` | `/api/v1/idm/register`  | Register a new user account          | Public |
| `POST` | `/api/v1/idm/login`     | Authenticate and receive a JWT token | Public |

### 🍕 Food Items (`/api/v1/food`)

| Method   | Endpoint              | Description                                              |
| -------- | --------------------- | ------------------------------------------------------- |
| `POST`   | `/api/v1/food`        | Create a new food item                                  |
| `GET`    | `/api/v1/food/{id}`   | Get a single food item by ID                            |
| `GET`    | `/api/v1/food`        | List all food items (paginated & sortable)              |
| `PUT`    | `/api/v1/food/{id}`   | Update an existing food item                            |
| `DELETE` | `/api/v1/food/{id}`   | Delete a single food item                               |
| `DELETE` | `/api/v1/food`        | Delete multiple food items (list of IDs in the body)    |

> Listing supports the query parameters `page`, `size`, `sortBy`, and `sortDirection` (e.g. `?page=0&size=10&sortBy=createdAt&sortDirection=desc`).

### 🛒 Cart (`/api/v1/cart`)

| Method   | Endpoint                         | Description                                            |
| -------- | -------------------------------- | ------------------------------------------------------ |
| `PUT`    | `/api/v1/cart`                   | Add a food item to the cart (`foodItemId`, `quantity`) |
| `PUT`    | `/api/v1/cart/update`            | Update the quantity of a cart item                     |
| `GET`    | `/api/v1/cart/cart-items`        | Get all items currently in the cart                    |
| `GET`    | `/api/v1/cart/total`             | Get the number of items in the cart                    |
| `DELETE` | `/api/v1/cart/item/{foodCartId}` | Remove a single item from the cart                     |
| `DELETE` | `/api/v1/cart/items`             | Remove all items from the cart                         |
| `DELETE` | `/api/v1/cart`                   | Remove multiple cart items (list of IDs in the body)   |

### 📦 Orders (`/api/v1/order`)

| Method | Endpoint        | Description                          |
| ------ | --------------- | ------------------------------------ |
| `POST` | `/api/v1/order` | Place an order from the current cart |

### 🏷 Discounts (`/api/v1/discount`)

| Method | Endpoint                                       | Description                               |
| ------ | ---------------------------------------------- | ----------------------------------------- |
| `POST` | `/api/v1/discount`                             | Create a new discount                     |
| `PUT`  | `/api/v1/discount/{id}`                        | Update an existing discount               |
| `GET`  | `/api/v1/discount/{id}`                        | Get a single discount by ID               |
| `GET`  | `/api/v1/discount`                             | List all discounts (paginated & sortable) |
| `PUT`  | `/api/v1/discount/update-food-items-discounts` | Apply a discount to multiple food items   |

### 👤 Users (`/api/v1/users`)

| Method | Endpoint             | Description      |
| ------ | -------------------- | ---------------- |
| `GET`  | `/api/v1/users/{id}` | Get a user by ID |

### 📈 Actuator

| Method | Endpoint           | Description               | Auth   |
| ------ | ------------------ | ------------------------- | ------ |
| `GET`  | `/actuator/health` | Application health check  | Public |
| `GET`  | `/actuator/info`   | Application info          | Public |

---

## 🏗 Project Structure

The codebase follows a layered, ports-and-adapters style architecture:

```
online-food-order-be/
├── build.gradle                  # Gradle build configuration & dependencies
├── settings.gradle
├── gradlew / gradlew.bat         # Gradle wrapper scripts
└── src/
    ├── main/
    │   ├── java/com/foodorder/backend/
    │   │   ├── OnlinefoodorderApplication.java   # Application entry point
    │   │   ├── application/                       # Web / API layer
    │   │   │   ├── controllers/                   # REST controllers (Food, Cart, Order, Discount, Idm, SysUser)
    │   │   │   ├── dtos/                           # Request/response DTOs
    │   │   │   └── exception/                      # Custom exceptions & global handler
    │   │   ├── configration/
    │   │   │   └── security/                       # Spring Security & JWT filter config
    │   │   ├── domain/                             # Business / domain layer
    │   │   │   ├── enums/                          # System enums (e.g. roles)
    │   │   │   ├── handlers/                       # Auth entry point & access-denied handlers
    │   │   │   ├── mappers/                        # MapStruct mappers
    │   │   │   ├── model/                          # Domain models
    │   │   │   ├── providers/                      # Identity provider
    │   │   │   └── services/                       # Business logic & security services
    │   │   └── persistence/                        # Persistence layer
    │   │       ├── adapter/                        # Repository adapters
    │   │       ├── entity/                         # JPA entities
    │   │       ├── jpa/                            # Spring Data JPA repositories
    │   │       └── repository/                     # Repository ports (interfaces)
    │   └── resources/
    │       ├── application.properties             # Main configuration
    │       └── application-sqllogs.properties     # SQL logging profile
    └── test/
        └── java/com/foodorder/backend/            # Tests
```

**Layer responsibilities:**

- **`application`** — exposes REST endpoints, validates input, and maps between DTOs and domain models.
- **`domain`** — holds business logic, domain models, mappers, and security services.
- **`persistence`** — translates domain models to JPA entities and persists them via Spring Data repositories.
- **`configration`** — wires up Spring Security, the JWT authentication filter, and related beans.

---

## 📄 License

This project is licensed under the **MIT License**. See the [`LICENSE`](LICENSE) file for details.

> If you intend to publish this repository publicly, add a `LICENSE` file at the project root containing the full MIT license text (or your license of choice).
