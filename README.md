# Stark Group Vehicle Management System (VMS) - Assignment 2 Monorepo

An enterprise-grade, advanced fleet management solution built for Stark Group subsidiaries. This repository is organized as a unified full-stack monorepo featuring a **Flutter Web** administration portal (Material 3) connected via REST to a **Spring Boot 3.4** backend secured with **Spring Security JWT Authentication**, backed by **PostgreSQL 16**.

---

## 🌐 Live Deployments & Test Access

* **Frontend (Netlify):** https://vms-frontend-stark-a2.netlify.app
* **Backend API (Render):** https://vms-backend-a2.onrender.com
* **Test User Account (Assignment Evaluation):**
  * **Email:** `test@starkgroup.test`
  * **Password:** `Password123!`
  * *Fleet Data Behavior:* Logging in with this test account displays the full suite of vehicles, assigned drivers, documents, and maintenance ledgers. Any newly registered account starts with an isolated, clean fleet (`[]`) due to tenant-level data isolation.
  * > [!NOTE]
  * > **Security & Compliance Note:** These test credentials are provided solely for academic review and assignment evaluation purposes so reviewers can inspect the pre-seeded fleet, drivers, documents, and maintenance logs immediately. In a production environment, credentials and secrets would never be disclosed in public repository documentation; authentication would be strictly managed through secure identity providers, secrets managers, and environment variables following the principle of least privilege.

---

## 🚀 Workspace Architecture

This repository is organized as a unified full-stack monorepo:

```
stark_vms_a2/
├── backend/                   # Spring Boot 3.4 REST API & Service Layer
│   ├── src/main/java/         # Controllers, Services, Repositories, Domain models
│   ├── src/main/resources/    # Flyway migrations (V1-V6), application.properties
│   ├── src/test/java/         # Unit, integration, security, and benchmark tests
│   ├── Dockerfile             # Multi-stage production container build
│   ├── render.yaml            # Declarative Render infrastructure blueprint
│   └── build.gradle           # Gradle build and dependencies
├── lib/                       # Flutter Web Client Application
│   ├── core/                  # Theme, API configs, responsive helpers, error handling
│   ├── data/                  # API clients, repositories, session management
│   ├── models/                # Domain models (Vehicle, Driver, Document, Maintenance, Dashboard)
│   ├── screens/               # Screens (Dashboard, Vehicles, Drivers, Documents, Maintenance, Auth)
│   └── widgets/               # Material 3 design system components & shells
├── test/                      # Component, widget, and workflow integration tests
├── web/                       # Web artifacts, index.html, manifest.json
└── netlify.toml               # Netlify SPA configuration and security headers
```

```
┌────────────────────────────────┐            HTTP / JSON            ┌──────────────────────────────────┐
│   Flutter Web Frontend (SPA)   │ ────────────────────────────────> │   Spring Boot REST Backend       │
│   (Port 5173 / Netlify CDN)    │ <──────────────────────────────── │   (Port 8080 / Render Service)   │
│   - Material 3 Executive UI    │  - Bearer JWT Authorization       │   - Auth & User Management       │
│   - Fleet Dashboard & KPIs     │  - REST CRUD /api/vehicles        │   - Vehicle Fleet Service        │
│   - Document Expiry Badges     │  - REST CRUD /api/drivers         │   - Driver Assignment Service    │
│   - Maintenance History Ledger │  - REST CRUD /api/documents       │   - Document Expiry Engine       │
│   - Session Persistence        │  - REST CRUD /api/maintenance     │   - KeepAlive Proactive Service  │
└────────────────────────────────┘  - REST GET  /api/dashboard/stats └─────────────────┬────────────────┘
                                                                                       │ JPA / JDBC
                                                                                       ▼
                                                                     ┌──────────────────────────────────┐
                                                                     │   PostgreSQL 16 Engine           │
                                                                     │   (Flyway Migrations V1-V6)      │
                                                                     │   - User data isolation          │
                                                                     │   - Vehicles, Drivers, Docs, Maint│
                                                                     └──────────────────────────────────┘
```

---

## 🌟 10 Advanced Fleet Management Features (Assignment 2)

| # | Feature | Backend Implementation | Frontend UI / UX |
| :--- | :--- | :--- | :--- |
| **1** | **Vehicle Document Storage** | `VehicleDocumentController` & Service supporting Registration, Tax Token, Insurance, and Fitness certificates. | Tabbed document viewer with file attachment support and metadata tagging. |
| **2** | **Document Expiry Tracking** | Automated date evaluation calculating days until expiry; returns computed expiry statuses. | Real-time visual badges (`EXPIRED`, `EXPIRING_SOON`, `VALID`) with urgency sorting. |
| **3** | **Maintenance Logging** | `MaintenanceController` logging service date, maintenance type, vendor/workshop, and cost. | Form with validated monetary amounts, date pickers, and maintenance categorization. |
| **4** | **Maintenance Ledger** | Relational `MaintenanceRecord` repository calculating aggregated fleet & per-vehicle spend. | Chronological timeline ledger showing total expenditures and breakdown per vehicle. |
| **5** | **Lifecycle Status Management** | `PATCH /api/vehicles/{id}/status` supporting immediate atomic transitions. | Status badges (`ACTIVE`, `INACTIVE`, `MAINTENANCE`) with responsive inline toggle controls. |
| **6** | **Multi-Field Vehicle Search** | JPA Specifications querying across Vehicle Number, Brand, Model, Company, and Driver. | Real-time debounced search bar with instant client table filtering. |
| **7** | **Pagination & Sorting** | Standard Spring Page JSON (`content`, `number`, `size`, `totalElements`, `totalPages`, `last`). | Responsive pagination controls with page size selector and column sorting. |
| **8** | **Driver Directory** | Dedicated Driver entity with unique license enforcement and license expiry audit. | Driver cards and table with quick status indicators (`AVAILABLE`, `ASSIGNED`, `SUSPENDED`). |
| **9** | **Driver–Vehicle Assignment** | `POST /assign-driver/{driverId}` updating bidirectional driver-vehicle relations atomically. | One-click driver assignment modal filtering strictly for available, active drivers. |
| **10** | **Fleet Analytics & KPIs** | `GET /api/dashboard/stats` aggregating real-time counts, costs, and renewal warnings. | Executive dashboard cards with breakdown charts, renewal notices, and quick actions. |

---

## ⚡ Keep-Alive Architecture

To eliminate Render's free-tier container sleep (15-minute inactivity shutdown):
* **`KeepAliveService`**: Automatically dispatches an HTTPS GET request to `https://vms-backend-a2.onrender.com/actuator/health` through Render's public edge router every 7 minutes. Render registers this inbound request and keeps the instance active 24/7.
* **Connection Pool Warmup**: Validates the HikariCP connection pool on every scheduled run (`isValid(3)`), ensuring fast sub-200ms response times on user login.

---

## 🛠️ Getting Started & Running Locally

### 1. Prerequisites
* **Java**: JDK 21+
* **Flutter SDK**: 3.47+ with Dart 3.x
* **PostgreSQL**: 16+

### 2. Run Backend Locally
```bash
cd backend
DB_URL="jdbc:postgresql://localhost:5432/stark_vms_a2_db" \
DB_USERNAME="vms_user" \
DB_PASSWORD="" \
JWT_SECRET="your-32-byte-or-longer-secret-key-here!" \
./gradlew bootRun
```

### 3. Run Backend Test Suite
```bash
cd backend
./gradlew test
```

### 4. Run Frontend Locally
```bash
flutter run -d chrome --dart-define=API_BASE_URL=http://localhost:8080
```

### 5. Run Frontend Test Suite
```bash
flutter test
```

---

## 🔒 Security & Git Hygiene

* **Externalized Secrets**: All database passwords, JWT secrets, and API credentials are kept out of source code and injected via environment variables.
* **Stateless Bearer JWT**: Spring Security 6 OAuth2 Resource Server validates tokens via HMAC-SHA256 with strict issuer and audience verification.
* **CORS Protection**: Whitelisted origin boundaries permitting trusted domains only.
* **Data Isolation**: Strict user-level data isolation preventing cross-account access or modifications.
