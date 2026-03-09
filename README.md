# Bolt Valet — Valet Parking Management System

A full-stack valet parking management system consisting of a React Native mobile app for attendants and a Spring Boot microservices backend. The system handles the complete vehicle lifecycle — check-in, parking, retrieval requests, and delivery — with real-time updates and customer notifications.

---

## System Architecture


<img width="1408" height="768" alt="Gemini_Generated_Image_a4qflra4qflra4qf" src="https://github.com/user-attachments/assets/e6bb5b29-64f8-43cf-8e40-1eab15460dc1" />



---

## Repositories

| Repo | Description |
|---|---|
| `ValetApp` | React Native mobile frontend (this repo) |
| `Valet-API` | Spring Boot microservices backend |

---

## Features

### Mobile App (Attendant)
- Login and manager registration
- Issue valet tickets — capture guest name, phone, vehicle info, pricing tier, and parking spot
- View all parked vehicles and edit or pull tickets
- Real-time car retrieval requests via WebSocket — accept incoming requests instantly
- Active cars view — track vehicles out for delivery and notify guests when ready
- Dark-themed, portrait-optimized UI

### Backend API
- JWT-authenticated API gateway — single entry point for all requests
- Full vehicle lifecycle management: `PARKED` → `REQUESTED` → `OUT`
- Real-time WebSocket broadcasting for attendant app updates
- Asynchronous event-driven notifications via RabbitMQ
- Customer SMS/push notifications on request and delivery
- Service discovery via Eureka — no hardcoded service addresses

---

## Tech Stack

### Frontend
| | |
|---|---|
| React Native 0.81 + Expo 54 | Mobile app framework |
| Expo Router | File-based navigation |
| TypeScript | Type support |
| Axios | REST API communication |
| SockJS + STOMP.js | Real-time WebSocket client |
| React Context API | Global state management |

### Backend
| | |
|---|---|
| Java 17 + Spring Boot 3.x | Service framework |
| Spring Cloud Gateway | API gateway with JWT filter |
| Spring Cloud Netflix Eureka | Service discovery |
| Spring WebSocket / STOMP | Real-time push to clients |
| Spring AMQP + RabbitMQ | Async event messaging |
| Spring Data JDBC + MySQL | Persistent storage |
| JJWT | Token-based authentication |

---

## Data Flow

### Issuing a Ticket
1. Attendant fills out ticket form in the app
2. App POSTs to `/auth-service/auth/register` via the API Gateway
3. Valet Service creates the customer and vehicle records
4. Ticket appears in the **Issued** tab

### Vehicle Request (Guest calls for car)
1. Guest request arrives; app's **Requested** tab receives a live push via WebSocket (`/topic/vehicle-request`)
2. Attendant taps **Accept** → app POSTs to `/valet-service/vehicles/updateStatus`
3. Vehicle status advances: `PARKED` → `REQUESTED` → `OUT`
4. RabbitMQ event triggers Notification Service to SMS the guest

### Vehicle Delivery
1. Attendant taps **Call** in the **Active** tab
2. App POSTs to `/valet-service/requests/notify-delivered`
3. RabbitMQ event triggers Notification Service: "Your vehicle is ready at the front."
4. WebSocket broadcasts to `/topic/vehicle-out`

---

## Backend API Reference

### Key Endpoints (via API Gateway at `:8083`)

| Method | Path | Description |
|---|---|---|
| `POST` | `/valet-service/vehicles/save` | Register a new vehicle |
| `POST` | `/valet-service/vehicles/updateStatus` | Advance vehicle status |
| `GET` | `/valet-service/vehicles/parked` | List all parked vehicles |
| `GET` | `/valet-service/vehicles/out` | List all out vehicles |
| `GET` | `/valet-service/requests/vehicle-request` | Trigger retrieval request |
| `POST` | `/valet-service/requests/notify-delivered` | Mark vehicle delivered |

### WebSocket Topics

| Topic | Event |
|---|---|
| `/topic/vehicle-request` | Guest requested their car |
| `/topic/vehicle-out` | Vehicle delivered to guest |

---

## License

Private project.


