# CineBook REST API Documentation
**Version:** 1.0.0  
**Base URL:** `http://localhost:5000/api`  
**Authentication:** Bearer Token via Header `Authorization: Bearer <jwt_token>`

---

## 1. Authentication Endpoints (`/api/auth`)

### 1.1 Register User
- **Method:** `POST /api/auth/register`
- **Access:** Public
- **Request Body:**
  ```json
  {
    "full_name": "Aarav Sharma",
    "email": "aarav@cinebook.com",
    "phone": "+91 9820012345",
    "password": "User@123",
    "confirm_password": "User@123"
  }
  ```
- **Response (201 Created):**
  ```json
  {
    "success": true,
    "message": "Registration successful. Welcome to CineBook!",
    "token": "eyJhbGciOi...",
    "user": { "id": 2, "full_name": "Aarav Sharma", "email": "aarav@cinebook.com", "role": "user" }
  }
  ```

### 1.2 User Login
- **Method:** `POST /api/auth/login`
- **Access:** Public
- **Request Body:**
  ```json
  {
    "email": "admin@cinebook.com",
    "password": "Admin@123"
  }
  ```
- **Response (200 OK):**
  ```json
  {
    "success": true,
    "token": "eyJhbGciOi...",
    "user": { "id": 1, "full_name": "System Administrator", "email": "admin@cinebook.com", "role": "admin" }
  }
  ```

### 1.3 Get Current Profile
- **Method:** `GET /api/auth/profile`
- **Access:** Authenticated (Bearer Token)
- **Response (200 OK):** Current user object.

### 1.4 Update Profile
- **Method:** `PUT /api/auth/profile`
- **Access:** Authenticated (Bearer Token)
- **Content-Type:** `multipart/form-data` or `application/json`
- **Fields:** `full_name`, `phone`, optional `avatar` file upload.

### 1.5 Change Password
- **Method:** `PUT /api/auth/change-password`
- **Access:** Authenticated (Bearer Token)
- **Request Body:**
  ```json
  {
    "current_password": "User@123",
    "new_password": "NewSecretPassword@123",
    "confirm_new_password": "NewSecretPassword@123"
  }
  ```

---

## 2. Movies Endpoints (`/api/movies`)

### 2.1 Get All Movies
- **Method:** `GET /api/movies`
- **Access:** Public
- **Query Parameters:** `search`, `genre`, `language`, `status` (`now_showing` | `coming_soon`), `minRating`, `sortBy`, `order`, `page`, `limit`
- **Response (200 OK):** Paginated movies array with total count.

### 2.2 Get Movie by ID
- **Method:** `GET /api/movies/:id`
- **Access:** Public
- **Response (200 OK):** Detailed movie object including verified reviews and audience ratings.

### 2.3 Create Movie (Admin)
- **Method:** `POST /api/movies`
- **Access:** Admin (Bearer Token)
- **Body:** `title`, `description`, `genre`, `language`, `duration`, `release_date`, `rating`, `certification`, `poster_url`, `banner_url`, `trailer_url`, `cast_members`, `director`, `status`

### 2.4 Update Movie (Admin)
- **Method:** `PUT /api/movies/:id`
- **Access:** Admin (Bearer Token)

### 2.5 Delete Movie (Admin)
- **Method:** `DELETE /api/movies/:id`
- **Access:** Admin (Bearer Token)

---

## 3. Theatres Endpoints (`/api/theatres`)

### 3.1 Get Theatres by City
- **Method:** `GET /api/theatres?city=Mumbai`
- **Access:** Public
- **Response (200 OK):** Array of theatres in the specified city.

### 3.2 Get Theatre by ID
- **Method:** `GET /api/theatres/:id`
- **Access:** Public
- **Response (200 OK):** Theatre info with screens list.

### 3.3 Create Theatre (Admin)
- **Method:** `POST /api/theatres`
- **Access:** Admin (Bearer Token)
- **Body:** `name`, `city`, `address`, `total_screens`, `phone`

---

## 4. Shows Endpoints (`/api/shows`)

### 4.1 Get Shows with Filters
- **Method:** `GET /api/shows`
- **Query Parameters:** `movieId`, `theatreId`, `city`, `date`
- **Response (200 OK):** Shows with formatted timestamps, screen types, and category prices.

### 4.2 Get Show by ID
- **Method:** `GET /api/shows/:id`
- **Access:** Public

### 4.3 Schedule Show (Admin)
- **Method:** `POST /api/shows`
- **Access:** Admin (Bearer Token)
- **Conflict Rule:** Rejects schedule if another show exists on the same screen within 150 minutes on that date.

---

## 5. Seats & Real-Time Locking (`/api/seats`)

### 5.1 Get Seat Layout for Show
- **Method:** `GET /api/seats/:id/seats`
- **Access:** Public (Optional Auth)
- **Response (200 OK):** Seat rows (A-F), seat numbers, categories (`Silver`, `Gold`, `Platinum`), status (`available`, `selected`, `locked`, `booked`).

### 5.2 Temporarily Lock Seats (5-minute hold)
- **Method:** `POST /api/seats/lock`
- **Access:** Authenticated (Bearer Token)
- **Request Body:**
  ```json
  {
    "show_id": 1,
    "seat_ids": [15, 16]
  }
  ```
- **Response (200 OK):** Lock expiry timestamp and confirmed seat IDs. Emits `seats_locked` via Socket.IO.

### 5.3 Release Seat Locks
- **Method:** `POST /api/seats/unlock`
- **Access:** Authenticated (Bearer Token)

---

## 6. Bookings & Payments (`/api/bookings`, `/api/payments`)

### 6.1 Create Booking
- **Method:** `POST /api/bookings`
- **Access:** Authenticated (Bearer Token)
- **Request Body:**
  ```json
  {
    "show_id": 1,
    "seat_ids": [15, 16],
    "payment_method": "CARD",
    "coupon_code": "WELCOME50",
    "transaction_id": "TXN_123456"
  }
  ```
- **Response (201 Created):** Full booking receipt, unique booking reference, breakdown, and Base64 QR code image.

### 6.2 Get User Bookings
- **Method:** `GET /api/bookings/my-bookings`
- **Access:** Authenticated (Bearer Token)

### 6.3 Cancel Booking
- **Method:** `PUT /api/bookings/:id/cancel`
- **Access:** Authenticated (Bearer Token)
- **Rule:** Permitted up to 2 hours before showtime. Releases seats and sets refund status.

### 6.4 Test Payment Verification
- **Method:** `POST /api/payments/verify`
- **Access:** Authenticated (Bearer Token)

---

## 7. Admin Endpoints (`/api/admin`)

- `GET /api/admin/dashboard` - Platform KPIs, stats, popular movies, monthly trends.
- `GET /api/admin/bookings` - All bookings across the platform with pagination and status filters.
- `GET /api/admin/users` - Registered users list with total bookings and expenditure.
- `PUT /api/admin/users/:id/block` - Toggle user account block/unblock state.
