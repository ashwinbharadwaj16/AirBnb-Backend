# 🏨 AirBnB Backend

A **Spring Boot backend application** that implements core hotel booking functionality including hotel and room management, user authentication, inventory management, dynamic pricing, bookings, and Stripe payment integration.

The application follows a layered architecture using **Spring Boot, Spring Data JPA, PostgreSQL, Spring Security, JWT, and Stripe**.

---

## ✨ Features

### 🏨 Hotel & Room Management

* Create and manage hotels
* Add and manage rooms within hotels
* Activate/deactivate hotels
* Retrieve hotel and room information
* View hotel booking details
* Generate hotel reports

### 👤 User Management

* User registration and login
* JWT-based authentication
* Role-based authorization
* User profile management
* JWT refresh token support

Supported roles include:

* `HOTEL_MANAGER`
* `GUEST`

### 📅 Booking Management

* Search available hotels
* View hotel information
* Initialize bookings
* Add guests to bookings
* Process booking payments
* Cancel bookings
* Track booking status

### 📦 Inventory Management

* Maintain room availability
* Track room inventory
* Reserve inventory during booking
* Update room inventory
* Prevent conflicting bookings through inventory locking

### 💰 Dynamic Pricing

The application uses a strategy-based pricing architecture to calculate room prices dynamically.

Pricing components include:

* Base pricing
* Surge pricing
* Occupancy-based pricing
* Urgency-based pricing
* Holiday pricing

Multiple pricing strategies can be composed to calculate the final room price.

### 💳 Stripe Payments

* Stripe checkout integration
* Payment initialization
* Stripe webhook handling
* Payment status processing
* Inventory reservation during the booking/payment workflow

---

# 🏗️ System Architecture

The application follows a layered Spring Boot architecture.

```text
                    ┌─────────────────────┐
                    │      Client         │
                    │   Postman / Web     │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │    REST Controllers │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │    Service Layer    │
                    │                     │
                    │ Business Logic      │
                    │ Booking             │
                    │ Pricing             │
                    │ Payment             │
                    │ Authentication      │
                    └──────────┬──────────┘
                               │
                 ┌─────────────┼─────────────┐
                 ▼             ▼             ▼
          ┌────────────┐ ┌────────────┐ ┌────────────┐
          │ Repository │ │  Security  │ │  External  │
          │    Layer   │ │   / JWT    │ │   Stripe   │
          └─────┬──────┘ └────────────┘ └────────────┘
                │
                ▼
        ┌─────────────────┐
        │   PostgreSQL    │
        └─────────────────┘
```

---

# 📐 System Design

### Data Flow Diagram

![Data Flow Diagram](./DFD_airBnb.png)

### Database Design

![Database Design](./DFD_airBnb_DBeaverView.png)

### High-Level Architecture

![High-Level Architecture](./Design_airBnb.png)

---

# 🛠️ Technology Stack

| Category       | Technology                  |
| -------------- | --------------------------- |
| Language       | Java                        |
| Framework      | Spring Boot                 |
| ORM            | Spring Data JPA / Hibernate |
| Database       | PostgreSQL                  |
| Security       | Spring Security             |
| Authentication | JWT                         |
| Payments       | Stripe                      |
| Build Tool     | Maven                       |
| Utilities      | Lombok                      |
| API Testing    | Postman                     |

---

# 📦 Application Modules

The application is organized into separate layers and modules:

```text
src/main/java/
│
├── controller/
├── service/
├── repository/
├── dto/
├── entity/
├── exception/
├── security/
└── config/
```

### Controller Layer

Handles HTTP requests and exposes REST APIs.

### Service Layer

Contains the application's business logic, including:

* Hotel management
* Room management
* Booking processing
* Inventory management
* Pricing calculation
* Authentication
* Payment processing

### Repository Layer

Provides database access through Spring Data JPA.

### DTO Layer

Separates API request/response models from persistent entities.

### Security Layer

Responsible for:

* JWT generation
* JWT validation
* Authentication filters
* User authentication
* Role-based authorization
* Security configuration

### Exception Layer

Provides centralized API error handling through:

* `ResourceNotFoundException`
* `GlobalExceptionHandler`
* API error responses

---

# 🔐 Authentication & Authorization

The application uses **Spring Security with JWT-based authentication**.

### Authentication Flow

```text
User
 │
 │ Login
 ▼
Auth Controller
 │
 ▼
Auth Service
 │
 ▼
Validate Credentials
 │
 ▼
Generate JWT
 │
 ▼
Client
```

For protected requests:

```text
Client
 │
 │ Authorization: Bearer <JWT>
 ▼
JWT Authentication Filter
 │
 ▼
Validate Token
 │
 ▼
Security Context
 │
 ▼
Controller
```

### Supported Authentication APIs

```text
POST /api/v1/auth/signup
POST /api/v1/auth/login
POST /api/v1/auth/refresh
```

Protected APIs require a valid JWT bearer token.

---

# 💰 Dynamic Pricing Engine

The pricing engine calculates room prices using composable pricing strategies.

```text
                    Base Price
                        │
                        ▼
                 ┌──────────────┐
                 │ Pricing Rules │
                 └───────┬──────┘
                         │
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
       Surge        Occupancy       Urgency
       Pricing       Pricing         Pricing
          │              │              │
          └──────────────┼──────────────┘
                         ▼
                  Holiday Pricing
                         │
                         ▼
                   Final Price
```

### Pricing Strategies

#### Base Pricing

Provides the initial room price.

#### Surge Pricing

Adjusts prices based on increased demand.

#### Occupancy Pricing

Adjusts prices based on hotel/room occupancy.

#### Urgency Pricing

Adjusts prices when the booking date is approaching.

#### Holiday Pricing

Applies pricing adjustments for configured holidays.

This design allows additional pricing strategies to be introduced without significantly changing the existing pricing logic.

---

# ⏰ Scheduled Price Updates

The application periodically recalculates inventory pricing.

The scheduled process:

1. Retrieves applicable inventory.
2. Calculates the current price.
3. Applies configured pricing strategies.
4. Updates inventory pricing.
5. Maintains the minimum hotel price used during hotel search.

This helps keep search results aligned with current inventory pricing.

---

# 📅 Booking Flow

The booking workflow consists of multiple stages.

```text
Search Hotels
     │
     ▼
Select Hotel / Room
     │
     ▼
Initialize Booking
     │
     ▼
Add Guests
     │
     ▼
Reserve Inventory
     │
     ▼
Initialize Payment
     │
     ▼
Stripe Checkout
     │
     ▼
Payment Webhook
     │
     ▼
Confirm Booking
```

Inventory reservation is protected using locking mechanisms to reduce the possibility of multiple users reserving the same inventory concurrently.

---

# 💳 Payment Flow

Stripe is integrated for payment processing.

```text
Client
  │
  ▼
Booking Service
  │
  ▼
Create Payment Session
  │
  ▼
Stripe
  │
  ▼
Customer Payment
  │
  ▼
Stripe Webhook
  │
  ▼
Webhook Controller
  │
  ▼
Update Booking / Payment Status
```

Sensitive payment credentials and API keys should be supplied through environment variables or external configuration rather than committed to source control.

---

# 📦 Inventory Management

Inventory represents the availability of rooms for a particular period.

The system supports:

* Inventory lookup
* Inventory updates
* Availability validation
* Inventory reservation
* Concurrent booking protection
* Dynamic price updates

### Inventory APIs

```text
GET   /api/v1/admin/inventory/rooms/{roomId}
PATCH /api/v1/admin/inventory/rooms/{roomId}
```

---

# 🌐 REST API

## 🏨 Admin — Hotels

| Method | Endpoint                                  | Description        |
| ------ | ----------------------------------------- | ------------------ |
| POST   | `/api/v1/admin/hotels`                    | Create hotel       |
| GET    | `/api/v1/admin/hotels`                    | Get all hotels     |
| GET    | `/api/v1/admin/hotels/{hotelId}`          | Get hotel          |
| PUT    | `/api/v1/admin/hotels/{hotelId}`          | Update hotel       |
| DELETE | `/api/v1/admin/hotels/{hotelId}`          | Delete hotel       |
| PATCH  | `/api/v1/admin/hotels/{hotelId}/activate` | Activate hotel     |
| GET    | `/api/v1/admin/hotels/{hotelId}/bookings` | Get hotel bookings |
| GET    | `/api/v1/admin/hotels/reports`            | Generate report    |

---

## 🛏️ Admin — Rooms

| Method | Endpoint                                        | Description     |
| ------ | ----------------------------------------------- | --------------- |
| POST   | `/api/v1/admin/hotels/{hotelId}/rooms`          | Create room     |
| GET    | `/api/v1/admin/hotels/{hotelId}/rooms`          | Get hotel rooms |
| GET    | `/api/v1/admin/hotels/{hotelId}/rooms/{roomId}` | Get room        |
| PUT    | `/api/v1/admin/hotels/{hotelId}/rooms/{roomId}` | Update room     |

---

## 🔎 Hotel Search & Booking

| Method | Endpoint                                 | Description           |
| ------ | ---------------------------------------- | --------------------- |
| GET    | `/api/v1/hotels/search`                  | Search hotels         |
| GET    | `/api/v1/hotels/{hotelId}/info`          | Get hotel information |
| POST   | `/api/v1/bookings/init`                  | Initialize booking    |
| POST   | `/api/v1/bookings/{bookingId}/addGuests` | Add guests            |
| POST   | `/api/v1/bookings/{bookingId}/payments`  | Initialize payment    |
| POST   | `/api/v1/bookings/{bookingId}/cancel`    | Cancel booking        |

---

## 🔐 Authentication

| Method | Endpoint               | Description       |
| ------ | ---------------------- | ----------------- |
| POST   | `/api/v1/auth/signup`  | Register user     |
| POST   | `/api/v1/auth/login`   | Authenticate user |
| POST   | `/api/v1/auth/refresh` | Refresh JWT       |

---

## 📦 Inventory

| Method | Endpoint                                 | Description        |
| ------ | ---------------------------------------- | ------------------ |
| GET    | `/api/v1/admin/inventory/rooms/{roomId}` | Get room inventory |
| PATCH  | `/api/v1/admin/inventory/rooms/{roomId}` | Update inventory   |

---

## 👤 User

| Method | Endpoint                | Description         |
| ------ | ----------------------- | ------------------- |
| GET    | `/api/v1/users/profile` | Get user profile    |
| PATCH  | `/api/v1/users/profile` | Update user profile |

---

# 🧪 API Testing

A Postman collection is included in the repository:

```text
AirBnb.postman_collection.json
```

### Import Collection

1. Open Postman.
2. Select **Import**.
3. Choose `AirBnb.postman_collection.json`.
4. Configure the required environment variables.
5. Authenticate using the login endpoint.
6. Use the returned JWT as a Bearer token for protected APIs.

---

# ⚙️ Getting Started

## Prerequisites

Make sure the following are installed:

* Java 17+
* Maven
* PostgreSQL
* Postman
* Stripe CLI (required for local webhook testing)

---

## 1. Clone the Repository

```bash
git clone https://github.com/ashwinbharadwaj16/AirBnb-Backend.git

cd AirBnb-Backend
```

---

## 2. Create PostgreSQL Database

Create a database named:

```text
airbnb
```

Example:

```sql
CREATE DATABASE airbnb;
```

---

## 3. Configure Application

Update:

```text
src/main/resources/application.properties
```

Example configuration:

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/airbnb
spring.datasource.username=postgres
spring.datasource.password=yourpassword

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

For Stripe configuration, provide the required credentials through your local environment/configuration.

**Do not commit secret keys to GitHub.**

---

## 4. Run the Application

Using Maven:

```bash
./mvnw spring-boot:run
```

On Windows:

```bash
mvnw.cmd spring-boot:run
```

The application will start on the configured Spring Boot port.

---

# 💻 Development Workflow

The project was developed incrementally around the core booking domain.

### Phase 1 — Application Foundation

* Create Spring Boot project
* Configure Maven dependencies
* Configure PostgreSQL
* Establish layered package structure

### Phase 2 — Domain Model

Implement core entities:

```text
User
Hotel
Room
Booking
Inventory
Guest
Payment
```

Configure entity relationships using JPA/Hibernate.

### Phase 3 — Hotel & Room Management

Implement:

* Hotel CRUD
* Room management
* Hotel activation
* Hotel information APIs

### Phase 4 — Inventory

Implement:

* Room inventory
* Availability checks
* Inventory updates
* Reservation logic
* Concurrency protection

### Phase 5 — Booking

Implement:

* Hotel search
* Booking initialization
* Guest management
* Booking cancellation
* Booking status management

### Phase 6 — Dynamic Pricing

Implement composable pricing strategies for:

* Base price
* Surge
* Occupancy
* Urgency
* Holiday pricing

### Phase 7 — Security

Implement:

* Spring Security
* JWT authentication
* JWT filter
* UserDetails
* Role-based authorization
* Refresh tokens
* Security exception handling

### Phase 8 — Payments

Integrate Stripe for:

* Payment session creation
* Checkout
* Webhook processing
* Booking/payment state updates

---

# 🧠 Design Patterns

### Strategy / Decorator-Based Pricing

The pricing engine separates individual pricing rules from the core booking logic.

This makes it possible to introduce additional pricing rules without modifying the existing pricing calculation flow.

### Layered Architecture

The application separates:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
Database
```

This improves maintainability and keeps business logic independent from HTTP and persistence concerns.

---

# 🔒 Concurrency & Data Consistency

Booking systems must handle concurrent requests where multiple users attempt to reserve the same room.

The application uses locking around inventory reservation and booking operations to help prevent conflicting reservations.

Conceptually:

```text
User A ───────┐
              │
              ▼
        Inventory Lock
              │
              ▼
        Check Availability
              │
              ▼
          Reserve
              │
              ▼
           Unlock
              
User B ─────────────► Waits / Rechecks
```

This ensures inventory availability is checked as part of a controlled reservation workflow.

---

# 📚 Key Learnings

This project provided practical experience with:

* Spring Boot application architecture
* Spring Data JPA and Hibernate
* PostgreSQL database design
* REST API development
* JWT authentication
* Spring Security
* Role-based authorization
* Transaction management
* Concurrent inventory reservation
* Dynamic pricing strategies
* Scheduled jobs
* Stripe payment integration
* Webhook processing
* Exception handling
* API testing with Postman

---

# 🚧 Future Improvements

Potential enhancements include:

* Redis caching for hotel search
* Improved search and filtering
* Distributed locking
* Email notifications
* Payment retry handling
* Better observability and logging
* Docker containerization
* CI/CD pipeline
* Cloud deployment
* Automated integration testing
* Rate limiting
* API documentation with OpenAPI/Swagger

---

# 👨‍💻 Author

**Ashwin Bharadwaj**

Java Backend Developer
Spring Boot • Java • PostgreSQL • Spring Security • REST APIs

[GitHub](https://github.com/ashwinbharadwaj16)
