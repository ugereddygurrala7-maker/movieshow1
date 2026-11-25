# CineBook – Automated Test Cases & QA Documentation
**Full Stack PBL Project | Test Suite Report**

This document details the test scenarios, expected results, execution status, and verification procedures for the CineBook Movie Ticket Booking System.

---

## Automated Test Execution Summary
- **Total Test Cases Executed:** 22
- **Passed:** 22
- **Failed:** 0
- **Pass Rate:** 100%
- **Automated Runner File:** `backend/tests/runApiTests.js`
- **Execution Command:** `node tests/runApiTests.js`

---

## Test Cases Detailed Specification

| Test Case ID | Module | Scenario / Description | Method & Endpoint | Expected HTTP Status | Result |
| :--- | :--- | :--- | :--- | :---: | :---: |
| **TC-01** | System | Server Health Check & Service Status | `GET /api/health` | 200 OK | **PASS** |
| **TC-02** | Authentication | Register New User with valid data | `POST /api/auth/register` | 201 Created | **PASS** |
| **TC-03** | Authentication | Register with duplicate email | `POST /api/auth/register` | 400 Bad Request | **PASS** |
| **TC-04** | Authentication | Login attempt with invalid password | `POST /api/auth/login` | 401 Unauthorized | **PASS** |
| **TC-05** | Authentication | Login with valid user credentials & JWT issuance | `POST /api/auth/login` | 200 OK | **PASS** |
| **TC-06** | Authentication | Admin Login & privilege assignment | `POST /api/auth/login` | 200 OK | **PASS** |
| **TC-07** | Authorization | Fetch User Profile with Bearer JWT | `GET /api/auth/profile` | 200 OK | **PASS** |
| **TC-08** | RBAC Security | Reject regular user from accessing Admin Portal | `GET /api/admin/dashboard` | 403 Forbidden | **PASS** |
| **TC-09** | Admin Analytics | Fetch Admin Dashboard KPIs and monthly revenue trends | `GET /api/admin/dashboard` | 200 OK | **PASS** |
| **TC-10** | Movies | Filter Movies by Genre and Showing Status | `GET /api/movies?genre=Sci-Fi` | 200 OK | **PASS** |
| **TC-11** | Movies | Retrieve Single Movie Details with Audience Reviews | `GET /api/movies/:id` | 200 OK | **PASS** |
| **TC-12** | Movie CRUD | Admin creates new movie entry | `POST /api/movies` | 201 Created | **PASS** |
| **TC-13** | Theatres | Retrieve theatres filtered by City | `GET /api/theatres?city=Mumbai` | 200 OK | **PASS** |
| **TC-14** | Shows | Schedule Conflict Rejection (< 150 mins on same screen) | `POST /api/shows` | 400 Bad Request | **PASS** |
| **TC-15** | Shows | Non-conflicting Show Creation | `POST /api/shows` | 201 Created | **PASS** |
| **TC-16** | Seats | Retrieve Interactive Screen Layout & Categories | `GET /api/seats/:id/seats` | 200 OK | **PASS** |
| **TC-17** | Seat Locking | Temporary 5-Minute Seat Lock Reservation | `POST /api/seats/lock` | 200 OK | **PASS** |
| **TC-18** | Coupons | Validate Coupon Code (`WELCOME50`) & Compute Discount | `POST /api/coupons/validate` | 200 OK | **PASS** |
| **TC-19** | Payments | Test-Mode Payment Verification Sandbox (Valid Card) | `POST /api/payments/verify` | 200 OK | **PASS** |
| **TC-20** | Payments | Test-Mode Payment Failure Simulation (Declined Card) | `POST /api/payments/verify` | 400 Bad Request | **PASS** |
| **TC-21** | Bookings | Transactional Booking with QR Code Generation | `POST /api/bookings` | 201 Created | **PASS** |
| **TC-22** | Concurrency | Prevent Double-Booking Conflict on Held/Booked Seats | `POST /api/bookings` | 409 Conflict | **PASS** |

---

## Concurrency & Seat Locking Verification
1. **Seat Lock Expiration:**
   - Automatically expires after 5 minutes via database timestamp constraints.
   - Background interval releases expired locks every 30 seconds and broadcasts `seats_unlocked` via Socket.IO.
2. **Double Booking Prevention:**
   - MySQL unique constraint on `(show_id, seat_id)` in `booking_seats` ensures atomic enforcement at the database engine level.
   - In-flight database transactions with `FOR UPDATE` lock records before payment settlement.
