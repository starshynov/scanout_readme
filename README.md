# scanout_readme

### Author  
**Oleksandr Starshynov**

### Current Doc Version  
**v1.1 (Release 1, updated November 2025)**  

---

## 1. Introduction  

### Project name  
**QuickShop**

### Brief description  
**QuickShop** is an online art store (initially focused on paintings) that merges a traditional catalog experience with modern AI and real-time interaction capabilities.  
It combines a clean React-based frontend with a Node.js + Express backend and multiple databases,  
preparing the platform for vector search, real-time order updates, and intelligent recommendations.  

### Project goal  
To build a scalable, intelligent e-commerce platform for art buyers and collectors.  
The project aims to enhance the shopping experience through emotional, semantic, and color-based search — and to integrate reliable order handling, authentication, and notifications.  

### Product history  
**Release 1:**  
- Full online catalog of paintings with responsive browsing  
- User registration, authentication, and ordering  
- Email notifications and payment setup integration  
- Real-time socket layer for user interactions  

**Release 2 (planned):**  
- AI-driven visual and semantic search using Qdrant  
- Color-palette and mood-based discovery  
- Stripe + PostNL integration for payments and delivery tracking  

---

## 2. Overall Architecture  

### Architectural approach  
**Modular Monolithic Architecture** — separate frontend and backend connected via RESTful API and WebSocket (Socket.IO).  
Backend manages both relational and document data (PostgreSQL + MongoDB) and is ready for expansion with a vector search engine (Qdrant).  

### Core principles  
- Hybrid data layer (relational + document + vector)  
- Real-time data synchronization via WebSockets  
- Modular design (auth, catalog, orders, payments, notifications)  
- Future extensibility for AI-driven search  

### Components  

#### Frontend (React)
- Built with **React** and **CSS**  
- Responsive masonry-style grid for artworks  
- Category-based filtering (Renaissance, Impressionism, Contemporary, etc.)  
- Dynamic product pages with live updates  
- REST API integration for user and catalog data  
- Deployed via AWS S3 or EC2 static hosting  

#### * Backend (Node.js + Express)
- Core RESTful API serving JSON responses  
- User authentication with **JWT + Bcrypt**  
- Order handling with **Stripe** payment integration  
- Real-time updates (order status, inventory changes) via **Socket.IO**  
- Notification system via **Nodemailer**  
- Support for environment variables through **dotenv**  
- URL and slug generation via **slugify**  

**Main API routes:**  
- `POST /api/register` — create new user  
- `POST /api/login` — user authentication  
- `GET /api/products` — get product catalog  
- `GET /api/products/:id` — get product details  
- `POST /api/orders` — create and confirm order  
- `GET /api/orders/:id` — retrieve order details  
- `POST /api/payment` — Stripe session initialization  

#### Data Layer
- **MongoDB:** stores product catalog (paintings, categories, descriptions, stock)  
- **PostgreSQL:** stores user accounts, hashed passwords, and orders  
- **LokiJS:** lightweight in-memory database for session caching and local demos  
- **Qdrant (planned):** semantic embeddings for visual/mood-based search  

#### AI Integration (Release 2)
- Embeddings generated using **SentenceTransformers (all-MiniLM-L6-v2)**  
- Vector similarity search for “paintings that feel similar”  
- AI model hosted locally for data privacy and predictable results  

---

## 3. Technology Stack  

### Frontend  
- **React**, **CSS**, **react-masonry-css**  

### Backend  
- **Node.js**, **Express**, **REST API**, **Socket.IO**  
- **Authentication:** JWT + Bcrypt  
- **Notifications:** Nodemailer  
- **Payments:** Stripe  
- **Database clients:** Mongoose (MongoDB), `pg` (PostgreSQL)  
- **Utilities:** dotenv, slugify, uuid, cors  
- **Local caching:** LokiJS  

### Databases  
- **MongoDB:** products and catalog metadata  
- **PostgreSQL:** users, orders, authentication  
- **Qdrant (planned):** semantic embeddings  

### Deployment  
- **Hosting:** AWS EC2 + Nginx  
- **Process manager:** PM2  
- **Monitoring:** CloudWatch  

---

## 4. Users and Roles  

### Roles  
- **Customer:** browse, register, log in, order, and track deliveries  
- **Admin:** manage inventory, verify payments, oversee logistics  

### Permissions  
- Customers can view products and manage their own orders.  
- Admins can create/edit products, approve orders, and update shipment status.  

---

## 5. Integration and Communication  

### Data flow  
1. Frontend sends requests to backend REST API.  
2. Backend retrieves or stores data in MongoDB or PostgreSQL.  
3. WebSockets (Socket.IO) push order updates and notifications in real time.  
4. In future releases, Qdrant enhances search relevance and personalization.  

### Versioning  
- **API:** `/api/v1`  
- **Model:** `models/quickshop-embeddings-v1` (planned for AI module)  

---

## 6. Data and Storage  

### Product schema (MongoDB)
```
{
  "title": "The Starry Night",
  "artist": "Vincent van Gogh",
  "category": "Post-Impressionism",
  "price": 4800,
  "imageUrl": "https://...",
  "description": "Oil on canvas, 1889",
  "dimensions": "73.7 × 92.1 cm",
  "stock": 1
}
```
User schema (PostgreSQL)

| Field | Type | Description |
|-----------|-----------|-----------|
| id   | UUID   | Primary key   |
| email   | String   | Unique, required   |
| password   | String   | Hashed with Bcrypt   |
| role   | Enum (customer / admin)   | Defines user access level   |
| created_at  | Timestamp   | Record creation time   |

Order schema (PostgreSQL)

| Field | Type | Description |
|-----------|-----------|-----------|
| id   | UUID   | Primary key   |
| user_id   | UUID   | Foreign key → users table   |
| product_id   | ObjectId   | Foreign key → MongoDB products   |
| status   | String   | Order status (Pending, Paid, Delivered)   |
| payment_intent   | String   | Stripe payment intent ID   |
| created_at   | Timestamp   | Record creation time   |

Vector schema (Qdrant)

| Field | Type | Description |
|-----------|-----------|-----------|
| id   | UUID   | Linked to product ID   |
| vector   | Float[]   | 512-dimensional embedding vector   |
| metadata   | JSON   | Color palette, mood, and art style tags   |

## 7. Security  

- Passwords hashed with **Bcrypt**  
- Token-based authentication via **JWT**  
- HTTPS enforced through **Nginx** reverse proxy  
- Environment variables secured using **dotenv**  
- CSRF-safe endpoints and strict input validation  
- Planned integration of **Stripe webhooks** for fraud detection  

---

## 8. Logging and Monitoring  

- **Winston** used for application-level logging  
- **Morgan** middleware for HTTP request tracking  
- **PM2** for production log management  
- **AWS CloudWatch** for metrics and alerting  

---

## 9. DevOps and CI/CD  

### Local Setup  

```
git clone https://github.com/aleksandrstarshynov/quickshop.git
cd QuickShop
```

**Frontend:**

```
cd my-app
npm install
npm start
```

**Backend:**
```
cd server
npm install
npm start
```

- Frontend runs on port 3000
- Backend runs on port 4000
- Deployed on AWS EC2 behind Nginx, managed via PM2

## 10. Testing and Quality  

- **Unit testing:** Jest + Supertest  
- **Integration testing:** Mocha / Chai *(planned)*  
- **Manual testing:** product browsing, checkout, and payment flow  
- **Future:** test coverage for AI search and Socket.IO events  

---

## 11. Monitoring and Incident Response  

- Tracks API latency, uptime, and transaction success rate  
- **AWS CloudWatch** alerts for downtime  
- Graceful fallback for failed API responses (Stripe, Qdrant)  
- Reconnect logic implemented for **Socket.IO**  

---

## 12. Roadmap  

### Release 2  

- AI-driven semantic search (**Qdrant + SentenceTransformers**)  
- Full payment integration with **Stripe**  
- Image CDN via **Cloudinary**  
- Delivery tracking through **PostNL**  
