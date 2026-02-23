**Microservices-based Valet System** built with **Spring Boot** and **Spring Cloud**. It features a robust security layer via an API Gateway and a dedicated service for handling notification logistics.
# Bolt-Valet Microservices System

A distributed backend architecture for a valet service management system. This project utilizes a microservices approach to handle API routing, service discovery, security via JWT, and notification logging.

##  Architecture Overview

The system is composed of several key components:

1. **API Registry (Eureka Server):** Acts as the service discovery hub, allowing microservices to find and communicate with each other.
2. **API Gateway:** The single entry point for all clients. It handles routing and cross-cutting concerns like security.
3. **Notification Service:** Manages SMS alerts and keeps a persistent log of all communications sent to customers (e.g., vehicle requests and delivery confirmations).


##  Security & Authentication

The **API Gateway** implements a custom `JwtAuthenticationFilter` to secure internal services.

* **JWT Validation:** All secured requests must include a `Bearer` token in the Authorization header.
* **Open Endpoints:** Certain routes are bypassed by the security filter (defined in `RouteValidator`), including:
* `/auth/register`
* `/auth/token`
* `/eureka` (Discovery dashboard)

##  Notification Service

This service handles the lifecycle of customer notifications. It uses **Spring JDBC** for database interactions and provides a REST API for triggering SMS alerts.

### Key Endpoints:

| Method | Endpoint | Description |
| --- | --- | --- |
| `POST` | `/notification-service/notifications/save` | Logs a notification to the database. |
| `POST` | `/notification-service/notifications/send-sms` | Saves the notification and simulates sending an SMS to the user. |

### Data Model (`Notification`):

* `ticket_number`: Unique identifier for the valet request.
* `phone`: Recipient's phone number.
* `message`: Content of the notification.
* `sent_at`: Timestamp of the transaction.

##  Tech Stack

* **Framework:** Spring Boot
* **Microservices:** Spring Cloud Gateway, Netflix Eureka
* **Security:** JSON Web Token (jjwt)
* **Database:** Spring JDBC / JdbcTemplate
* **Language:** Java 17+

## Getting Started

### Prerequisites

* JDK 17 or higher
* Maven
* A running database (compatible with the `notification_Logs` table schema)

### Installation & Run

1. **Start the API Registry:** Navigate to `api-registry` and run `mvn spring-boot:run`.
2. **Start the Notification Service:** Run the service to begin listening for valet events.
3. **Start the API Gateway:** Run the gateway to enable secure routing.
