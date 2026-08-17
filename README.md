# 🎬 Movies Ticket Booking System
### A Full-Stack Movie Ticket Booking Platform — React.js + Node.js + Express.js + MongoDB

Welcome! This README is your **complete guide** to understanding, setting up, and running the Theater Booking System — a full-stack web application that lets users browse movies, pick a showtime, select seats on a live seat map, and complete a booking, while admins manage movies, theatres, screens, showtimes, and users from a dedicated dashboard.

Every section explains **why** something is structured a certain way before showing **how** to use it, so the reasoning behind the architecture is clear, not just the commands.

---

## 📚 Table of Contents

1. [Project Introduction](#1-project-introduction)
2. [Prerequisites](#2-prerequisites)
3. [Project Setup](#3-project-setup)
4. [Folder Structure](#4-folder-structure)
5. [Environment Variables](#5-environment-variables)
6. [Backend Overview](#6-backend-overview)
7. [Frontend Overview](#7-frontend-overview)
8. [Core User Flow](#8-core-user-flow)
9. [Admin Panel](#9-admin-panel)
10. [API Reference](#10-api-reference)
11. [Running the Project](#11-running-the-project)
12. [Final Project Flow](#12-final-project-flow)
13. [Future Improvements](#13-future-improvements)
14. [Build It Yourself — Step by Step](#14-build-it-yourself--step-by-step)

---

## 1. Project Introduction

### 1.1 Project Overview

**Theater Booking System** is a MERN-stack (MongoDB, Express, React, Node.js) application that recreates the core experience of a movie ticket booking platform:

```
React (Client) ⇄ Express/Node (Server) ⇄ MongoDB (Database)
```

Users can register, log in, browse now-showing movies, view movie details, pick a theatre and showtime, select seats on an interactive seat map, and complete a booking. Admins have a separate, protected dashboard to manage movies, theatres, screens, showtimes, users, and bookings.

### 1.2 Technologies Used

| Layer | Technology | Purpose |
|---|---|---|
| Frontend | React 19 | Builds the user interface using reusable components |
| Frontend build tool | Vite | Fast dev server and production bundler |
| Frontend routing | React Router v7 | Handles navigation between user and admin pages |
| Frontend HTTP client | Axios | Sends requests from React to the backend API |
| UI feedback | React Toastify, SweetAlert2 | Toast notifications and confirmation dialogs |
| Icons | React Icons | UI iconography |
| Backend | Node.js | JavaScript runtime that runs the server |
| Backend framework | Express 5 | REST API framework |
| Real-time layer | Socket.io | Pushes live booking, payment, seat, and user events from server to the admin dashboard |
| Database | MongoDB | Stores movies, theatres, screens, seats, showtimes, bookings, payments, users, and admins |
| ODM | Mongoose | Schema definitions and MongoDB access |
| Auth | JSON Web Token (JWT) | Issues and verifies login tokens for users and admins |
| Password security | bcrypt | Hashes passwords before storing them |
| File uploads | Multer | Handles poster/banner/image uploads |
| Config | dotenv | Loads environment variables from `.env` |
| Dev tooling | Nodemon | Restarts the server automatically on file changes |

### 1.3 Key Features

- **Movie catalog** — browse now-showing movies with posters, genre, language, duration, certificate, and rating
- **Live search** — a header search bar with a real-time suggestions dropdown (poster, title, genre, language, rating) that matches on title, genre, language, description, and theatre/location; search terms sync to the URL (`?search=...`) so results are shareable and survive a page refresh, and typing on the Home page auto-scrolls to the movies section
- **Movie details page** — full synopsis, trailer, and available theatres/showtimes for a movie
- **Interactive seat booking** — live seat map per screen with seat categories (Regular / Premium / VIP), wheelchair-accessible seats, and real-time booked/available status
- **Authentication** — separate registration & login flows for users and admins, secured with JWT and bcrypt-hashed passwords
- **Booking flow** — showtime selection → seat selection → payment page → confirmation screen → view in "My Bookings", now backed by real `Booking` and `Payment` records created together in a single API call, with a local-storage fallback if the backend call fails
- **Live admin dashboard** — bookings, payments, revenue stats, and new user sign-ups update in real time across the Dashboard, Bookings, Payments, Users pages and the Topbar notification bell via Socket.io, with no manual refresh needed
- **Payments** — card, UPI (scan-to-pay QR code generated on the fly for the exact order amount), and other methods on the Payment page
- **Digital ticket sharing** — from the Confirmation page, email the ticket via a pre-filled `mailto:` link (auto-filled with the logged-in user's email) or send it straight to WhatsApp by entering a mobile number
- **User account** — profile and settings pages, booking history
- **Admin dashboard** — manage movies (add/edit/delete), theatres, screens, showtimes, view registered users, bookings, and payments
- **Protected routes** — user- and admin-only pages are gated behind authentication checks on the frontend
- **Contact form** — validated submission with toast/SweetAlert2 confirmation feedback

---

## 2. Prerequisites

Install the following tools before setting up the project.

### 2.1 Node.js and npm

Node.js runs the backend server; npm installs project dependencies.

**Install:** Download the **LTS version** from [nodejs.org](https://nodejs.org).

**Verify:**
```bash
node -v
npm -v
```

### 2.2 MongoDB

The database that stores movies, theatres, seats, bookings, and users.

**Install:** Local install from [mongodb.com/try/download/community](https://www.mongodb.com/try/download/community), **or** use a free [MongoDB Atlas](https://www.mongodb.com/atlas) cloud cluster and grab its connection string.

**Verify (local install):**
```bash
mongod --version
```

### 2.3 MongoDB Compass (optional but recommended)

A visual tool to inspect collections (`movies`, `theatres`, `screens`, `seats`, `showtimes`, `users`, `admins`) while developing.

**Install:** [mongodb.com/try/download/compass](https://www.mongodb.com/try/download/compass)

### 2.4 Code Editor

Any editor works; VS Code is recommended with the ESLint and Prettier extensions.

### 2.5 Quick Checklist

```bash
node -v
npm -v
mongod --version
```

---

## 3. Project Setup

The project is split into two independent folders inside `TheaterBookingSystem/`:

```bash
cd TheaterBookingSystem
```

### 3.1 Backend Setup

```bash
cd server
npm install
```

**Why:** `npm install` reads `server/package.json` and installs `express`, `mongoose`, `cors`, `dotenv`, `bcrypt`, `jsonwebtoken`, and `multer` (plus `nodemon` as a dev dependency).

### 3.2 Frontend Setup

```bash
cd ../client
npm install
```

**Why:** This installs React 19, React Router, Axios, React Toastify, SweetAlert2, React Icons, and the Vite tooling defined in `client/package.json`.

---

## 4. Folder Structure

```
TheaterBookingSystem/
├── server/                              # Express + MongoDB backend
│   ├── .env                              # Environment variables (not committed)
│   ├── package.json
│   ├── server.js                         # App entry point
│   ├── seedUsers.js                      # Script to seed sample users
│   │
│   ├── config/
│   │   └── db.js                         # MongoDB connection logic
│   │
│   ├── models/                           # Mongoose schemas
│   │   ├── Admin.js
│   │   ├── Booking.js                    # Confirmed booking record
│   │   ├── Movie.js
│   │   ├── Payment.js                    # Transaction record linked to a Booking
│   │   ├── Screen.js
│   │   ├── Seat.js
│   │   ├── ShowTime.js
│   │   ├── Theatre.js
│   │   └── User.js
│   │
│   ├── controllers/                      # Business logic per resource
│   │   ├── adminController.js
│   │   ├── bookingController.js          # Creates Booking + Payment together, emits socket events
│   │   ├── movieController.js
│   │   ├── paymentController.js          # Lists payments, aggregates revenue/success-rate stats
│   │   ├── screenController.js
│   │   ├── seatController.js
│   │   ├── showTimeController.js
│   │   ├── theatreController.js
│   │   └── userController.js
│   │
│   ├── routes/                           # Express route definitions
│   │   ├── adminRoutes.js
│   │   ├── bookingRoutes.js
│   │   ├── movieRoutes.js
│   │   ├── paymentRoutes.js
│   │   ├── screenRoutes.js
│   │   ├── seatRoutes.js
│   │   ├── showTimeRoutes.js
│   │   ├── theatreRoutes.js
│   │   └── userRoutes.js
│   │
│   ├── middleware/                       # Auth / request middleware
│   │
│   └── uploads/                          # Uploaded images
│       ├── posters/                       # Movie poster uploads
│       └── banners/                       # Movie banner uploads
│
└── client/                               # React + Vite frontend
    ├── index.html
    ├── package.json
    ├── vite.config.js
    ├── eslint.config.js
    │
    ├── public/
    │   └── favicon.svg
    │
    └── src/
        ├── main.jsx                       # React entry point
        ├── App.jsx                        # Route definitions
        ├── App.css
        ├── index.css
        │
        ├── Userpages/                     # Public-facing pages
        │   ├── Home/
        │   │   ├── Home.jsx                 # Landing page
        │   │   ├── Hero.jsx                 # Hero/banner section
        │   │   ├── MovieCard.jsx            # Reusable movie card
        │   │   ├── Navbar.jsx               # Public navbar
        │   │   └── Home.css
        │   ├── Movies/
        │   │   ├── Movies.jsx               # Movie listing + search/filter
        │   │   └── Movies.css
        │   ├── moviedetails/
        │   │   ├── moviedetails.jsx         # Movie details + showtimes
        │   │   └── moviedetails.css
        │   ├── SeatBooking/
        │   │   ├── SeatBooking.jsx          # Interactive seat selection
        │   │   └── SeatBooking.css
        │   ├── Payment/
        │   │   ├── Payment.jsx              # Card / UPI (QR) / other payment
        │   │   └── Payment.css
        │   ├── confirmation/
        │   │   ├── confirmation.jsx         # Booking confirmation + share (email/WhatsApp)
        │   │   └── confirmation.css
        │   ├── Bookings/
        │   │   ├── MyBookings.jsx           # Booking history
        │   │   └── MyBookings.css
        │   ├── Login/
        │   │   ├── Login.jsx
        │   │   ├── Login.css
        │   │   └── Register/                # Nested register variant
        │   │       ├── Register.jsx
        │   │       └── Register.css
        │   ├── Register/
        │   │   ├── Register.jsx
        │   │   └── Register.css
        │   ├── Profile/
        │   │   ├── Profile.jsx
        │   │   └── Profile.css
        │   ├── Settings/
        │   │   ├── Settings.jsx
        │   │   └── Settings.css
        │   ├── About/
        │   │   ├── About.jsx
        │   │   └── About.css
        │   ├── Contact/
        │   │   ├── Contact.jsx              # Validated contact form
        │   │   └── Contact.css
        │   └── Offers/
        │       ├── Offers.jsx
        │       └── Offers.css
        │
        ├── admin/                         # Admin-only area
        │   ├── components/
        │   │   ├── AdminLayout.jsx          # Sidebar + Topbar shell
        │   │   ├── Sidebar.jsx
        │   │   ├── Topbar.jsx
        │   │   ├── DashboardCard.jsx
        │   │   └── MovieForm.jsx
        │   ├── pages/
        │   │   ├── AdminLogin.jsx
        │   │   ├── Dashboard.jsx
        │   │   ├── Movies.jsx
        │   │   ├── AddMovie.jsx
        │   │   ├── EditMovie.jsx
        │   │   ├── Theatres.jsx
        │   │   ├── AddTheatre.jsx
        │   │   ├── EditTheatre.jsx
        │   │   ├── Screens.jsx
        │   │   ├── Addscreen.jsx
        │   │   ├── ShowTimes.jsx
        │   │   ├── AddShowTime.jsx
        │   │   ├── Users.jsx
        │   │   ├── Bookings.jsx
        │   │   └── Payments.jsx
        │   └── css/
        │       ├── admin.css
        │       ├── adminLogin.css
        │       ├── dashboard.css
        │       ├── movies.css
        │       ├── addMovie.css
        │       ├── theatre.css
        │       ├── screen.css
        │       └── showtime.css
        │
        ├── components/                    # Shared UI
        │   ├── Header/
        │   │   ├── Header.jsx                # Navbar + live search dropdown
        │   │   └── Header.css
        │   ├── Footer/
        │   │   ├── Footer.jsx
        │   │   └── Footer.css
        │   ├── Logo/
        │   │   ├── Logo.jsx                  # Brand mark (theme: auto/light/dark)
        │   │   └── Logo.css
        │   ├── UserMenu/
        │   │   ├── UserMenu.jsx
        │   │   └── UserMenu.css
        │   └── ProtectedRoute.jsx            # UserProtectedRoute / AdminProtectedRoute
        │
        ├── services/                       # Axios wrappers per API resource
        │   ├── adminService.js
        │   ├── bookingService.js             # Create/list bookings, update booking status
        │   ├── movieService.js
        │   ├── paymentService.js             # List payments, fetch aggregated stats
        │   ├── screenService.js
        │   ├── seatService.js
        │   ├── showTimeService.js
        │   ├── socketService.js              # Socket.io client + subscribeToEvent helper
        │   └── userService.js
        │
        ├── utils/                         # Helpers
        │   ├── formatDuration.js
        │   └── swal.js                      # SweetAlert2 / toast helpers
        │
        └── assets/
            └── images/                     # Static image assets
```

**Why this layout:** the backend is organized by **resource** (movie, theatre, screen, seat, showtime, user, admin), each with its own model, controller, and route file — a standard REST API pattern. The frontend separates **public user pages**, the **admin area**, and **shared components/services**, so the two experiences can evolve independently while reusing the same Axios service layer. Shared pieces like the live-search `Header`, the theme-aware `Logo`, and the `swal.js` toast helper live in `components/`/`utils/` precisely because multiple pages (Home, Movies, Contact, Confirmation) depend on them.

---

## 5. Environment Variables

Create a `.env` file inside `server/` (this file is not committed to version control):

```env
PORT=5000
MONGO_URI=mongodb://localhost:27017/theaterBookingSystem
JWT_SECRET=your_own_secret_key_here
```

| Variable | Purpose |
|---|---|
| `PORT` | Port the Express server listens on (defaults to `5000` if omitted) |
| `MONGO_URI` | MongoDB connection string (local or Atlas) |
| `JWT_SECRET` | Secret used to sign/verify JWTs for user and admin login. A fallback default exists in code for local testing, but a real secret should always be set |

The client talks to the API at `http://localhost:5000/api/...` (see `client/src/services/*.js`), so keep the backend on port `5000` unless those base URLs are updated to match a different port. The client's `socketService.js` also connects to `http://localhost:5000` directly for the WebSocket connection — keep this in sync with `PORT` as well.

---

## 6. Backend Overview

### 6.1 Entry Point (`server.js`)

On startup, the server:
1. Loads environment variables with `dotenv`
2. Connects to MongoDB via `connectDB()`
3. Seeds an initial admin account if none exists (`seedInitialAdmins`)
4. Wraps the Express `app` in a raw `http` server and attaches a **Socket.io** instance to it, storing `io` on the app (`app.set("io", io)`) so controllers can broadcast events
5. Registers middleware: `cors()`, and JSON/urlencoded body parsing (with a 50 MB limit to accommodate base64 poster/image uploads)
6. Mounts one router per resource under `/api/...`, including `/api/bookings` and `/api/payments`
7. Starts listening via `server.listen()` (not `app.listen()`) so both HTTP and WebSocket traffic share the same port

### 6.2 Data Models

| Model | Key fields | Notes |
|---|---|---|
| `Movie` | title, genre, language, duration, certificate, rating, releaseDate, poster, banner, trailer, ticketPrice, bookingOpen, theatres[] | Core catalog entity |
| `Theatre` | name, location, city, screens, totalSeats, image | A physical venue |
| `Screen` | theatreId, screenName, screenType, totalSeats, regularSeats, premiumSeats, vipSeats | A screen inside a theatre |
| `Seat` | screenId, seatNumber, category, price, isWheelchair, booked | Individual bookable seat |
| `ShowTime` | movieId, theatreId, screenId, date, time, ticketPrice, status | Links a movie to a screen at a specific date/time |
| `Booking` | bookingRef (unique), userId, customerName, customerEmail, showId, movieTitle, theatreName, screenName, showDate, showTime, seats[], totalAmount, status (Confirmed/Pending/Cancelled), paymentMethod | Created together with a `Payment` record at checkout |
| `Payment` | txnId (unique), bookingId, bookingRef, customerName, customerEmail, method, amount, status (Success/Pending/Refunded) | One transaction per booking; feeds the admin revenue/success-rate stats |
| `User` | (registration/login fields, hashed password) | End customer account |
| `Admin` | name, email, password (hashed), role | Dashboard administrator, seeded on first boot |

### 6.3 Auth & Security

- Passwords for both `User` and `Admin` are hashed with **bcrypt** before saving (`pre("save")` hook)
- Login issues a **JWT**, signed with `JWT_SECRET`, used to authenticate subsequent requests and protect admin-only frontend routes
- `matchPassword` methods on the models compare a plaintext password against the stored hash at login time

### 6.4 Real-Time Updates (Socket.io)

The server wraps Express in a raw `http` server and attaches a **Socket.io** instance to it, stored on `app` (`app.set("io", io)`) so any controller can broadcast events. `bookingController` and `adminController` emit events after a database write; the admin frontend listens for them via `services/socketService.js`.

| Event | Emitted when | Consumed by |
|---|---|---|
| `booking:created` | A new booking + payment is created at checkout | Dashboard, Bookings page, Topbar notification bell |
| `booking:updated` | An admin changes a booking's status | Bookings page |
| `payment:created` | A payment record is created alongside a booking | Dashboard, Payments page, Topbar |
| `seats:updated` | Seats are marked booked after checkout | Seat map views |
| `user:registered` | A new user signs up | Users page, Topbar |
| `user:deleted` / `users:updated` | An admin removes/edits a user | Users page |
| `operations:updated` | Any booking is added or updated | Dashboard (triggers a stats refetch) |

**Why:** without this, the admin dashboard would need manual polling or a page refresh to see new bookings, payments, or sign-ups. Socket.io pushes the update the moment it happens on the server.

---

## 7. Frontend Overview

### 7.1 Routing

`App.jsx` defines all routes using **React Router v7**, split into:
- **Public/user routes** — Home, Movies, Movie Details, Seat Booking, Payment, Confirmation, Login, Register, About, Contact, Offers
- **Authenticated user routes** — Profile, Settings, My Bookings (guarded by `ProtectedRoute`)
- **Admin routes** — Admin Login, Dashboard, Movies, Theatres, Screens, ShowTimes, Users, Bookings, Payments (nested under `AdminLayout` with a `Sidebar` and `Topbar`)

### 7.2 Services Layer

Each backend resource has a matching file in `client/src/services/` (e.g. `movieService.js`, `seatService.js`, `theatreService.js`) that wraps Axios calls to the corresponding `/api/...` endpoint. Pages call these service functions rather than calling Axios directly, keeping API URLs and request logic in one place.

### 7.3 Shared Components

- `Header` / `Footer` — layout wrappers used across user-facing pages. `Header` owns the live search experience (query state, click-outside-to-close dropdown via a `ref`, and suggestion filtering) and accepts an optional `moviesList` prop so pages that already have movies loaded skip a duplicate fetch
- `Navbar` / `UserMenu` — top navigation and account dropdown
- `Logo` — reusable brand mark; supports a `theme` prop (`"auto" | "light" | "dark"`) so it renders correctly on both light and dark backgrounds (e.g. dark theme in the `Footer`)
- `ProtectedRoute` — redirects unauthenticated users away from account/booking pages

---

## 8. Core User Flow

```
Home → Movies → Movie Details → Select Showtime
   → Seat Booking (pick seats on the live seat map)
   → Payment → Confirmation → My Bookings
```

1. **Browse / Search** — `Home.jsx` and `Movies.jsx` fetch and display movies via `movieService`. The shared `Header` component drives search: it holds the query, shows a live filtered dropdown of matching movies, and updates the URL's `?search=` param via `useSearchParams` so `Movies.jsx`/`Home.jsx` and the header stay in sync
2. **Movie details** — `moviedetails.jsx` fetches a single movie and its showtimes (`showTimeService`), grouped by theatre
3. **Seat selection** — `SeatBooking.jsx` fetches seats for the chosen screen (`seatService.getSeats`), renders them by category (Regular/Premium/VIP) with wheelchair seats flagged, and lets the user pick seats before submitting a booking (`seatService.bookSeats`)
4. **Payment** — `Payment.jsx` collects payment details for the selected seats and showtime. For UPI, a QR code (via the `api.qrserver.com` QR generator) is rendered on the fly encoding a `upi://pay` deep link pre-filled with the order amount, so the user scans and pays with any UPI app instead of typing a UPI ID. On confirming payment, it calls `createBookingApi` (`bookingService`) which hits `POST /api/bookings` — this creates the `Booking` **and** its linked `Payment` record together, marks the chosen seats as booked, and broadcasts the change to the admin dashboard over Socket.io in real time. If the API call fails, the flow falls back to storing the booking locally so the user experience isn't blocked
5. **Confirmation** — `confirmation.jsx` shows the booking summary and lets the user share the digital ticket: **Email** opens a pre-filled `mailto:` link (default email pulled from the logged-in user's local session) with the full ticket details in the body, and **WhatsApp** prompts for a mobile number and opens `wa.me`/`api.whatsapp.com` with the formatted ticket pre-filled
6. **History** — `MyBookings.jsx` lists the logged-in user's past bookings

---

## 9. Admin Panel

Reached via a separate `AdminLogin.jsx`, protected independently from the user login. Once authenticated, the admin can:

- **Dashboard** — live stats via `DashboardCard` components (revenue, bookings, payments), refreshed instantly over Socket.io whenever a new booking or payment comes in
- **Movies** — add (`AddMovie.jsx`), edit (`EditMovie.jsx`), list, and delete movies, including poster upload via `MovieForm`
- **Theatres** — add (`AddTheatre.jsx`), edit (`EditTheatre.jsx`), and list theatres
- **Screens** — add screens to a theatre and configure seat category counts (`Addscreen.jsx`)
- **ShowTimes** — schedule a movie on a specific screen/date/time (`AddShowTime.jsx`)
- **Users** — view registered users, updated live as new accounts register
- **Bookings** — view all bookings and update a booking's status (Confirmed/Pending/Cancelled), with new bookings appearing in real time
- **Payments** — view transaction history and aggregated stats (total revenue, success rate, UPI usage %), updated live
- **Topbar notifications** — a live notification bell that surfaces new bookings, payments, and user sign-ups as they happen, powered by the same Socket.io events as the Dashboard

---

## 10. API Reference

Base URL: `http://localhost:5000/api`

### 10.1 Movies — `/movies`
| Method | Endpoint | Description |
|---|---|---|
| GET | `/movies` | List all movies |
| GET | `/movies/:id` | Get one movie |
| POST | `/movies` | Add a movie |
| PUT | `/movies/:id` | Update a movie |
| DELETE | `/movies/:id` | Delete a movie |

### 10.2 Theatres — `/theatres`
| Method | Endpoint | Description |
|---|---|---|
| GET | `/theatres` | List all theatres |
| GET | `/theatres/:id` | Get one theatre |
| POST | `/theatres` | Add a theatre |
| PUT | `/theatres/:id` | Update a theatre |
| DELETE | `/theatres/:id` | Delete a theatre |

### 10.3 Screens — `/screens`
| Method | Endpoint | Description |
|---|---|---|
| GET | `/screens` | List all screens |
| POST | `/screens` | Add a screen |
| DELETE | `/screens/:id` | Delete a screen |

### 10.4 Seats — `/seats`
| Method | Endpoint | Description |
|---|---|---|
| GET | `/seats/:screenId` | Get all seats for a screen |
| POST | `/seats/generate` | Auto-generate seats for a screen |
| POST | `/seats/book` | Book one or more seats |

### 10.5 Showtimes — `/showtimes`
| Method | Endpoint | Description |
|---|---|---|
| GET | `/showtimes` | List all showtimes |
| GET | `/showtimes/movie/:movieId` | List showtimes for a specific movie |
| GET | `/showtimes/:id` | Get one showtime |
| POST | `/showtimes` | Add a showtime |
| DELETE | `/showtimes/:id` | Delete a showtime |

### 10.6 Users — `/users`
| Method | Endpoint | Description |
|---|---|---|
| POST | `/users/register` | Register a new user |
| POST | `/users/login` | Log in, returns a JWT |
| GET | `/users` | List all users (admin use) |
| DELETE | `/users/:id` | Delete a user |

### 10.7 Admins — `/admin`
| Method | Endpoint | Description |
|---|---|---|
| POST | `/admin/login` | Admin login, returns a JWT |
| POST | `/admin/register` | Register a new admin |
| GET | `/admin` | List all admins |
| DELETE | `/admin/:id` | Delete an admin |

### 10.8 Bookings — `/bookings`
| Method | Endpoint | Description |
|---|---|---|
| POST | `/bookings` | Create a booking (also creates the linked `Payment`, marks seats booked, and emits `booking:created`/`payment:created`/`seats:updated` socket events) |
| GET | `/bookings` | List all bookings, newest first (admin use) |
| PUT | `/bookings/:id` | Update a booking's status (Confirmed/Pending/Cancelled), emits `booking:updated` |

### 10.9 Payments — `/payments`
| Method | Endpoint | Description |
|---|---|---|
| GET | `/payments` | List all payment transactions, newest first |
| GET | `/payments/stats` | Aggregated stats: total revenue, transaction count, success rate, UPI usage percentage |

### 10.10 Real-time events (Socket.io, not REST)
| Event | Description |
|---|---|
| `booking:created` / `booking:updated` | A booking was created or its status changed |
| `payment:created` | A payment transaction was recorded |
| `seats:updated` | Seats for a show were marked booked |
| `user:registered` / `user:deleted` / `users:updated` | A user account changed |
| `operations:updated` | Generic signal to refetch dashboard stats |

---

## 11. Running the Project

### Step 1 — Start MongoDB
```bash
mongod
```
(Skip if MongoDB is already running as a background service, or if using an Atlas cloud connection string.)

### Step 2 — Start the backend
```bash
cd server
npm run dev
```
Expected output:
```
=== MongoDB Connected Successfully ===
=== Server running at http://localhost:5000 ===
```

### Step 3 — Start the frontend
```bash
cd client
npm run dev
```
Vite will print a local URL (typically `http://localhost:5173`) — open it in the browser.

### Step 4 — Use the application
- Register/log in as a user, browse movies, book seats, and view "My Bookings"
- Visit the admin login route and sign in with the seeded/admin credentials to manage movies, theatres, screens, and showtimes

---

## 12. Final Project Flow

```
React UI
   │  (user browses movies, selects seats, submits a booking)
   ▼
Axios (services/*.js)
   │  (sends GET / POST / PUT / DELETE to /api/...)
   ▼
Express Route
   │  (matches method + URL to a route file)
   ▼
Controller
   │  (runs business logic — e.g. create Booking + Payment, mark seats booked, hash password, sign JWT)
   ▼
MongoDB (via Mongoose)
   │  (reads/writes movies, theatres, screens, seats, showtimes, bookings, payments, users, admins)
   ▼
Controller Response
   │  (JSON response with status code)
   ▼
React UI Update
   │  (state updates, toast/alert shown, page navigates forward)
   ▼
User sees the updated result — a booked seat, a new movie, a confirmed ticket
```

### 12.1 Real-time admin path (Socket.io)

```
Booking/Payment/User controller finishes a write
   ▼
io.emit("booking:created" / "payment:created" / "user:registered" / etc.)
   │  (event broadcast to every connected client)
   ▼
Client socketService.js (socket.io-client, connected on app load)
   │  (subscribeToEvent(eventName, callback) fires the callback)
   ▼
Admin Dashboard / Bookings / Payments / Users page / Topbar bell
   │  (component state updates immediately)
   ▼
Admin sees the new booking, payment, or sign-up instantly — no refresh needed
```

---

## 13. Future Improvements

- Integrate a real payment gateway (Razorpay/Stripe) in place of the current `Payment` step
- Add role-based route protection with middleware on the backend (currently primarily enforced on the frontend)
- Move image uploads to cloud storage (e.g. Cloudinary/S3) instead of the local `server/uploads/` folder
- Add seat-locking during checkout to prevent double-booking under concurrent requests
- Deploy backend (Render/Railway) and frontend (Vercel/Netlify), switching `MONGO_URI` to MongoDB Atlas

---

---

## 14. Build It Yourself — Step by Step

This section walks through **recreating the core of this project from scratch**, copy-block by copy-block. It uses the **Movie** resource as the full CRUD example (backend) and the **Seat Booking** page as the advanced example (frontend), since those two together cover every pattern used elsewhere in the app (Theatre, Screen, ShowTime, User, Admin all follow the same shape).

### 14.1 Initialize the project folders

```bash
mkdir TheaterBookingSystem
cd TheaterBookingSystem
mkdir server client
```

### 14.2 Backend — initialize and install dependencies

```bash
cd server
npm init -y
npm install express mongoose cors dotenv bcrypt jsonwebtoken multer
npm install --save-dev nodemon
```

Open the generated `package.json` and set:

```json
{
  "type": "module",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js"
  }
}
```

**Why `"type": "module"`:** it lets the backend use modern `import`/`export` syntax instead of `require`, matching the style used throughout this project's controllers and routes.

### 14.3 Backend — connect to MongoDB

Create `server/config/db.js`:

```javascript
import mongoose from "mongoose";

const connectDB = async () => {
  try {
    await mongoose.connect(process.env.MONGO_URI);
    console.log("=== MongoDB Connected Successfully ===");
  } catch (error) {
    console.error(" MongoDB Connection Failed");
    console.error(error.message);
    process.exit(1);
  }
};

export default connectDB;
```

Create `server/.env`:

```env
PORT=5000
MONGO_URI=mongodb://localhost:27017/theaterBookingSystem
JWT_SECRET=your_own_secret_key_here
```

### 14.4 Backend — create a model (Movie)

Create `server/models/Movie.js`:

```javascript
import mongoose from "mongoose";

const movieSchema = new mongoose.Schema(
  {
    title: { type: String, required: true, trim: true },
    description: { type: String, default: "" },
    genre: { type: String, required: true },
    language: { type: String, required: true },
    duration: { type: String, required: true },
    certificate: { type: String, default: "U/A" },
    rating: { type: Number, default: 0 },
    releaseDate: { type: Date, default: Date.now },
    poster: { type: String, required: true },
    banner: { type: String, default: "" },
    trailer: { type: String, default: "" },
    ticketPrice: { type: Number, default: 250 },
    bookingOpen: { type: Boolean, default: true },
    theatres: [
      {
        theatreName: String,
        location: String,
        showTimes: [String],
      },
    ],
  },
  { timestamps: true }
);

export default mongoose.model("Movie", movieSchema);
```

**Why:** every other resource (`Theatre`, `Screen`, `Seat`, `ShowTime`, `User`, `Admin`) follows this same pattern — a `mongoose.Schema` with `required`/`default` field rules and `{ timestamps: true }` so Mongoose auto-tracks `createdAt`/`updatedAt`.

### 14.5 Backend — create the controller (business logic)

Create `server/controllers/movieController.js`:

```javascript
import Movie from "../models/Movie.js";

// GET All Movies
export const getMovies = async (req, res) => {
  try {
    const movies = await Movie.find();
    res.json(movies);
  } catch (err) {
    res.status(500).json({ message: err.message });
  }
};

// GET Single Movie
export const getMovie = async (req, res) => {
  try {
    const movie = await Movie.findById(req.params.id);
    if (!movie) {
      return res.status(404).json({ message: "Movie not found" });
    }
    res.json(movie);
  } catch (err) {
    res.status(500).json({ message: err.message });
  }
};

// ADD Movie
export const addMovie = async (req, res) => {
  try {
    const movie = await Movie.create(req.body);
    res.status(201).json(movie);
  } catch (err) {
    res.status(400).json({ message: err.message });
  }
};

// UPDATE Movie
export const updateMovie = async (req, res) => {
  try {
    const movie = await Movie.findByIdAndUpdate(req.params.id, req.body, { new: true });
    res.json(movie);
  } catch (err) {
    res.status(400).json({ message: err.message });
  }
};

// DELETE Movie
export const deleteMovie = async (req, res) => {
  try {
    await Movie.findByIdAndDelete(req.params.id);
    res.json({ message: "Movie deleted successfully" });
  } catch (err) {
    res.status(500).json({ message: err.message });
  }
};
```

**Why this shape:** each function is a thin wrapper around a single Mongoose call, wrapped in `try/catch` so a database error becomes a clean JSON error response instead of crashing the server.

### 14.6 Backend — create the route

Create `server/routes/movieRoutes.js`:

```javascript
import express from "express";
import {
  getMovies,
  getMovie,
  addMovie,
  updateMovie,
  deleteMovie,
} from "../controllers/movieController.js";

const router = express.Router();

router.get("/", getMovies);
router.get("/:id", getMovie);
router.post("/", addMovie);
router.put("/:id", updateMovie);
router.delete("/:id", deleteMovie);

export default router;
```

**Repeat 14.4–14.6** for every other resource (`Theatre`, `Screen`, `Seat`, `ShowTime`, `User`, `Admin`) — same three files, same pattern, different field names.

### 14.7 Backend — wire everything up in `server.js`

```javascript
import express from "express";
import cors from "cors";
import dotenv from "dotenv";
import connectDB from "./config/db.js";
import movieRoutes from "./routes/movieRoutes.js";
import theatreRoutes from "./routes/theatreRoutes.js";
import screenRoutes from "./routes/screenRoutes.js";
import seatRoutes from "./routes/seatRoutes.js";
import showTimeRoutes from "./routes/showTimeRoutes.js";
import userRoutes from "./routes/userRoutes.js";
import adminRoutes from "./routes/adminRoutes.js";
import { seedInitialAdmins } from "./controllers/adminController.js";

dotenv.config();

connectDB().then(() => {
  seedInitialAdmins();
});

const app = express();

app.use(cors());
app.use(express.json({ limit: "50mb" }));
app.use(express.urlencoded({ limit: "50mb", extended: true }));

app.use("/api/movies", movieRoutes);
app.use("/api/theatres", theatreRoutes);
app.use("/api/screens", screenRoutes);
app.use("/api/seats", seatRoutes);
app.use("/api/showtimes", showTimeRoutes);
app.use("/api/users", userRoutes);
app.use("/api/admin", adminRoutes);

app.get("/", (req, res) => {
  res.send("# Theater Booking Backend is Running...");
});

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => {
  console.log(`=== Server running at http://localhost:${PORT} ===`);
});
```

Run it:

```bash
npm run dev
```

You should see `=== MongoDB Connected Successfully ===` and `=== Server running at http://localhost:5000 ===`.

### 14.8 Frontend — scaffold with Vite

```bash
cd ../client
npm create vite@latest . -- --template react
npm install axios react-router-dom react-icons react-toastify sweetalert2
```

### 14.9 Frontend — entry point

Create/update `client/src/main.jsx`:

```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import { BrowserRouter } from "react-router-dom";
import App from "./App";
import "./index.css";

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </React.StrictMode>
);
```

**Why `BrowserRouter` wraps `App`:** it enables client-side routing so `react-router-dom`'s `<Routes>`/`<Route>` inside `App.jsx` can switch pages without a full browser reload.

### 14.10 Frontend — a service file (Axios wrapper)

Create `client/src/services/movieService.js`:

```javascript
import axios from "axios";

const API = "http://localhost:5000/api/movies";

export const getMovies = () => axios.get(API);
export const getMovie = (id) => axios.get(`${API}/${id}`);
export const addMovie = (movie) => axios.post(API, movie);
export const updateMovie = (id, movie) => axios.put(`${API}/${id}`, movie);
export const deleteMovie = (id) => axios.delete(`${API}/${id}`);
```

**Why a services layer:** pages never call `axios` directly — they import from `services/*.js`. This keeps every API base URL in one place, so switching from `localhost:5000` to a deployed backend URL later means editing one line per file, not hunting through every component.

### 14.11 Frontend — routing skeleton

Create/update `client/src/App.jsx`, adding one `<Route>` per page as you build it:

```jsx
import { Routes, Route } from "react-router-dom";
import Home from "./Userpages/Home/Home";
import Movies from "./Userpages/Movies/Movies";
import MovieDetails from "./Userpages/moviedetails/moviedetails";
import SeatBooking from "./Userpages/SeatBooking/SeatBooking";

function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/movies" element={<Movies />} />
      <Route path="/movie/:id" element={<MovieDetails />} />
      <Route path="/seat-booking/:showId" element={<SeatBooking />} />
      {/* add more routes as you build Login, Register, Payment, Confirmation,
          Profile, MyBookings, Admin pages, etc. */}
    </Routes>
  );
}

export default App;
```

### 14.12 Frontend — a page that calls the service (Movies list)

```jsx
import { useEffect, useState } from "react";
import { getMovies } from "../../services/movieService";

function Movies() {
  const [movies, setMovies] = useState([]);

  useEffect(() => {
    fetchMovies();
  }, []);

  const fetchMovies = async () => {
    const res = await getMovies();
    setMovies(res.data);
  };

  return (
    <div className="movies-grid">
      {movies.map((movie) => (
        <div key={movie._id} className="movie-card">
          <img src={movie.poster} alt={movie.title} />
          <h3>{movie.title}</h3>
          <p>{movie.genre} · {movie.language}</p>
        </div>
      ))}
    </div>
  );
}

export default Movies;
```

**Request flow:** component mounts → `useEffect` runs once → `getMovies()` calls `GET /api/movies` → Express route → `movieController.getMovies` → `Movie.find()` in MongoDB → JSON array returned → `setMovies` updates state → React re-renders the grid.

### 14.13 Frontend — seat booking service (the advanced example)

```javascript
import axios from "axios";

const API = "http://localhost:5000/api/seats";

export const getSeats = (screenId) => axios.get(`${API}/${screenId}`);
export const generateSeats = (payload) => axios.post(`${API}/generate`, payload);
export const bookSeats = (payload) => axios.post(`${API}/book`, payload);
```

A minimal seat-picker page built on top of it:

```jsx
import { useEffect, useState } from "react";
import { useParams } from "react-router-dom";
import { getSeats, bookSeats } from "../../services/seatService";

function SeatBooking() {
  const { showId } = useParams(); // in a real page, resolve screenId from the showtime
  const [seats, setSeats] = useState([]);
  const [selected, setSelected] = useState([]);

  useEffect(() => {
    fetchSeats();
  }, []);

  const fetchSeats = async () => {
    const res = await getSeats(showId);
    setSeats(res.data);
  };

  const toggleSeat = (seat) => {
    if (seat.booked) return;
    setSelected((prev) =>
      prev.includes(seat._id) ? prev.filter((id) => id !== seat._id) : [...prev, seat._id]
    );
  };

  const confirmBooking = async () => {
    await bookSeats({ seatIds: selected });
    fetchSeats();
    setSelected([]);
  };

  return (
    <div className="seat-map">
      {seats.map((seat) => (
        <button
          key={seat._id}
          disabled={seat.booked}
          className={selected.includes(seat._id) ? "seat selected" : "seat"}
          onClick={() => toggleSeat(seat)}
        >
          {seat.seatNumber}
        </button>
      ))}
      <button onClick={confirmBooking} disabled={selected.length === 0}>
        Confirm Booking
      </button>
    </div>
  );
}

export default SeatBooking;
```

**Why this is the "advanced" example:** it shows local UI state (`selected`) layered on top of server state (`seats`), plus a write operation (`bookSeats`) that mutates the database and then re-fetches to reflect the new booked/available status — the same pattern every write action in this app follows (add movie, add theatre, add showtime, register user, etc.).

### 14.14 Run both halves together

```bash
# Terminal 1
cd server
npm run dev

# Terminal 2
cd client
npm run dev
```

Open the Vite dev URL in the browser — you now have a working slice of the Theater Booking System, ready to extend with the remaining pages and resources following the exact same model → controller → route → service → page pattern.

---

## 🎉 Repository

**Repository name:** `TheaterBookingSystem-using-React`
