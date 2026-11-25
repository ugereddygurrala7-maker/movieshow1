# CineBook – Movie Ticket Booking System
### Full Stack PBL Project | Built with Google Antigravity

[![Node.js](https://img.shields.io/badge/Node.js-v24.x-green)](https://nodejs.org/)
[![React](https://img.shields.io/badge/React-18-blue)](https://react.dev/)
[![Vite](https://img.shields.io/badge/Vite-5.x-purple)](https://vitejs.dev/)
[![MySQL](https://img.shields.io/badge/MySQL-8.0-orange)](https://www.mysql.com/)
[![TailwindCSS](https://img.shields.io/badge/TailwindCSS-3.4-38B2AC)](https://tailwindcss.com/)
[![Socket.IO](https://img.shields.io/badge/Socket.IO-Real--Time-white)](https://socket.io/)

CineBook is a complete, production-grade online movie ticket booking platform inspired by BookMyShow and modern streaming platforms. It features interactive cinema seat selection, temporary 5-minute seat locking, real-time availability via Socket.IO, coupon discounts, test-mode payment gateway simulation, dynamic QR-code tickets, PDF ticket generation, and a comprehensive administrative dashboard.

---

## 1. Key Features

### 👤 Customer Experience
- **Hero & Featured Movie Premieres:** Watch official trailer embeds, view certifications, and book tickets instantly.
- **City & Theatre Selection:** Multi-city support (Mumbai, Delhi, Bengaluru, Hyderabad) with real theatres and screen formats (IMAX 3D, Dolby Atmos, 4DX).
- **Interactive Cinema Seating:**
  - Real cinema curved screen effect.
  - Categories: **Silver** (Rows A-B), **Gold** (Rows C-E), **Platinum** (Row F).
  - Status color coding: **Green** (Available), **Blue** (Selected), **Yellow** (Temporarily Locked), **Red** (Booked).
- **Temporary Seat Locking:** Backend locks selected seats for 5 minutes with a live countdown timer. Automatic expiration releases seats for other users.
- **Real-Time Sync (Socket.IO):** Instant updates broadcast across all connected browsers when seats are locked, unlocked, or confirmed.
- **Checkout & Test Payment Simulator:**
  - Ticket subtotal, per-seat convenience fee (₹35), and GST (18%).
  - Promo code discounts (`WELCOME50`, `CINE100`, `SUPER20`).
  - Credit/Debit Card sandbox, UPI simulation, and NetBanking tests.
  - Never stores raw card numbers or sensitive CVVs.
- **Digital QR Pass & PDF Ticket:**
  - Generates verifiable QR code tickets via `qrcode`.
  - One-click "Download PDF Ticket" via `jsPDF` and `html2canvas`.
- **Booking Management & Cancellation:**
  - View upcoming and past booking receipts.
  - 2-hour pre-show cancellation policy with full refund calculation and immediate seat release.
- **User Profile:** Manage personal info, change password, and upload profile avatar via Multer.

### 🛡️ Admin Dashboard
- **Analytics & KPIs:** Real-time metrics for total users, movies, theatres, active shows, bookings, and revenue.
- **Popular Movies & Trends:** Performance charts based on actual ticket sales and gross revenue.
- **Movie Management:** Full CRUD (Add, Edit, Delete, Poster/Banner upload, Trailer URLs, Cast & Director).
- **Theatre Management:** Add and manage venues, addresses, and screen configurations.
- **Show Scheduling:** Create shows with **automated schedule conflict prevention** (ensures shows on the same screen are spaced at least 2.5 hours apart).
- **Booking Oversight:** Search bookings by reference ID, inspect customer details, and cancel bookings administratively.
- **User Management:** Search registered users, view booking history and expenditure, and toggle account block/unblock states.

---

## 2. Technology Stack

- **Frontend:** React.js, Vite, Tailwind CSS, Lucide React icons, React Router DOM, Axios, Socket.IO Client, jsPDF, html2canvas, canvas-confetti.
- **Backend:** Node.js, Express.js, JWT (`jsonwebtoken`), Password Hashing (`bcryptjs`), Multer (file uploads), Nodemailer, Socket.IO, Express Validator.
- **Database:** MySQL with `mysql2/promise` connection pooling, parameterised queries, transactions, and unique constraints.

---

## 3. Project Structure

```
cinebook/
├── backend/
│   ├── config/
│   │   ├── db.js                 # MySQL pool & query executor
│   │   ├── mockStore.js          # In-memory store (for preview/dev)
│   │   └── mockQueryEngine.js    # Parameterised SQL compatibility engine
│   ├── controllers/              # REST controllers for all modules
│   ├── middleware/               # JWT auth, upload, and error handling
│   ├── routes/                   # Organized modular API routes
│   ├── services/                 # Socket.IO, Seat locking, Email services
│   ├── scripts/                  # Automated DB init & seed scripts
│   ├── tests/                    # 22 Automated test cases runner
│   ├── app.js                    # Express app configuration
│   ├── server.js                 # HTTP + Socket.IO server entry
│   └── .env.example              # Backend environment template
├── frontend/
│   ├── src/
│   │   ├── components/           # Navbar, Footer, AuthModal, TrailerModal, etc.
│   │   ├── context/              # AuthContext and CityContext
│   │   ├── pages/                # Home, Movies, Details, Seats, Checkout, Admin, etc.
│   │   ├── services/             # Axios API client and Socket.IO connection
│   │   ├── App.jsx               # Route mapping
│   │   └── main.jsx              # React DOM entry
│   └── tailwind.config.js        # Theme color tokens (#090909, #E50914)
├── database/
│   ├── schema.sql                # 12 MySQL tables DDL with constraints
│   └── seed.sql                  # Realistic sample data & bcrypt passwords
├── TESTING.md                    # 22 Test cases specification and QA report
├── API_DOCUMENTATION.md          # REST API endpoints documentation
└── package.json                  # Root development scripts
```

---

## 4. Default Demo Accounts

| Role | Email | Password | Privileges |
| :--- | :--- | :--- | :--- |
| **System Admin** | `admin@cinebook.com` | `Admin@123` | Full administrative dashboard, Movie/Theatre/Show CRUD, User blocking |
| **Customer** | `aarav@cinebook.com` | `User@123` | Ticket booking, seat locking, payments, cancellation, reviews |
| **Customer** | `diya@cinebook.com` | `User@123` | Ticket booking, profile settings, reviews |

*(Note: The login dialog has quick "Fill Demo Credentials" buttons for instant 1-click access during evaluations).*

---

## 5. Local Setup & Installation

### Prerequisites
- **Node.js:** v18.0.0 or higher
- **npm:** v9.0.0 or higher
- **MySQL:** v8.0 or higher (or cloud MySQL like Aiven / Railway)

### Step 1: Install Dependencies
Run the following from the project root:
```bash
# Install backend dependencies
cd backend
npm install

# Install frontend dependencies
cd ../frontend
npm install
```

### Step 2: Configure Environment Variables
Inside `backend/.env` (copy from `backend/.env.example`):
```env
PORT=5000
NODE_ENV=development
CLIENT_URL=http://localhost:5173

# MySQL Connection Details
DB_HOST=localhost
DB_PORT=3306
DB_USER=root
DB_PASSWORD=your_mysql_password
DB_NAME=cinebook_db

# Security & Secrets
JWT_SECRET=cinebook_super_secret_jwt_key_2026_production_safe
JWT_EXPIRES_IN=7d
SEAT_LOCK_DURATION_MINUTES=5
```

### Step 3: Initialize and Seed MySQL Database
Make sure your MySQL server is running, then execute:
```bash
cd backend
npm run db:init
npm run db:seed
```
*This creates the `cinebook_db` database, constructs all 12 relational tables with foreign keys and unique constraints, and populates movies, theatres, screens, seats, shows, coupons, and sample users.*

*(Note: If MySQL is not running on your machine, CineBook automatically operates in interactive developer mode so all UI workflows, seating layouts, bookings, and tests can still be evaluated seamlessly without crashing).*

### Step 4: Run the Application
In separate terminal windows:

**Terminal 1 (Backend API & Socket.IO Server):**
```bash
cd backend
npm run dev
# Server running at http://localhost:5000
```

**Terminal 2 (React Vite Frontend):**
```bash
cd frontend
npm run dev
# Frontend accessible at http://localhost:5173
```

---

## 6. Running Automated Tests

A comprehensive automated test runner validates all 22 test cases:
```bash
cd backend
node tests/runApiTests.js
```
The runner verifies:
- User registration and duplicate email handling
- Password verification and JWT token issuance
- Admin RBAC access boundaries
- Movie filtering, sorting, and CRUD
- Conflict-protected show scheduling
- Interactive seat categories (Silver, Gold, Platinum)
- 5-minute temporary seat locking
- Test payment sandbox (success & decline modes)
- QR-code generation and ticket booking
- Concurrency protection against double bookings
- Booking cancellation and automatic seat release

---

## 7. Cloud Deployment Guide

### A. Database (Cloud MySQL)
1. Create a free MySQL database on **Railway** or **Aiven** (https://aiven.io/).
2. Copy the connection host, port, username, password, and database name.
3. Import `database/schema.sql` and `database/seed.sql` via MySQL Workbench, TablePlus, or the cloud provider's web console.

### B. Backend (Render / Railway)
1. Deploy the `backend/` folder to **Render** as a Web Service (or on Railway).
2. Set Build Command: `npm install`
3. Set Start Command: `node server.js`
4. Add environment variables:
   - `PORT=5000`
   - `DB_HOST`, `DB_PORT`, `DB_USER`, `DB_PASSWORD`, `DB_NAME` (pointing to your Cloud MySQL instance)
   - `CLIENT_URL` (pointing to your deployed frontend domain)
   - `JWT_SECRET`

### C. Frontend (Vercel)
1. Deploy the `frontend/` folder to **Vercel**.
2. Framework Preset: **Vite**
3. Build Command: `npm run build`
4. Output Directory: `dist`
5. Set Environment Variable:
   - `VITE_API_URL=https://your-backend.onrender.com/api`
   - `VITE_SOCKET_URL=https://your-backend.onrender.com`
