# Library Management System

A full-stack MERN library management app with separate member and admin dashboards — members can browse books and track loans/reservations, while admins manage the book catalog, categories, members, and issue/return transactions.

## Features

**Members**
- Sign up / sign in (student via admission ID or staff via employee ID)
- Browse all books and filter by category
- View active and past transactions (issued/reserved books) on a personal dashboard

**Admins**
- Add, update, and remove books and book categories
- Add and manage library members
- Issue books and process returns (transactions)
- View all members and all transactions across the library

## Architecture

```
┌──────────────────┐   REST (axios)   ┌───────────────────┐   Mongoose ODM   ┌─────────────┐
│  React frontend    │ ───────────────▶ │  Express backend    │ ───────────────▶ │  MongoDB      │
│  (Member/Admin      │ ◀─────────────── │  (routes + models)   │ ◀─────────────── │  (Books,       │
│   dashboards)        │      JSON        │                       │                  │   Users,        │
└──────────────────┘                  └───────────────────┘                  │   Categories,   │
                                                                                │   Transactions) │
                                                                                └─────────────┘
```

Auth state on the frontend is handled with React Context + `useReducer` (`AuthContext`/`AuthReducer`) and persisted to `localStorage`.

## Tech stack

**Backend**
- Node.js + Express (ESM/`type: module`)
- MongoDB + Mongoose
- `bcrypt` for password hashing
- `multer` for file uploads (e.g. profile photos)
- `cors`, `dotenv`

**Frontend**
- React 17 + React Router v5
- Context API + `useReducer` for auth state
- Material UI, React-Bootstrap, Semantic UI React (component libraries)
- Axios for API calls
- `moment`, `react-datepicker` for date handling in transactions

## Data model

- **User** — member or admin, with admission/employee ID, profile info, and references to active and previous `BookTransaction`s
- **Book** — title, author, language, publisher, available count, status, linked `BookCategory` entries, and linked transactions
- **BookCategory** — named category with a list of books belonging to it
- **BookTransaction** — issue or reservation record linking a book and borrower, with from/to/return dates and status

## Project structure

```
Library-Management-System/
├── backend/
│   ├── server.js              # Express app entry, MongoDB connection, route mounting
│   ├── models/                # Mongoose schemas: Book, BookCategory, BookTransaction, User
│   └── routes/                # auth, users, books, categories, transactions
└── frontend/
    ├── src/
    │   ├── Components/         # Header, Footer, ImageSlider, PopularBooks, Stats, etc.
    │   ├── Context/             # AuthContext + AuthReducer (login state)
    │   └── Pages/
    │       ├── Home.js, Allbooks.js, Signin.js
    │       └── Dashboard/
    │           ├── AdminDashboard/    # AddBook, AddMember, AddTransaction, GetMember, Return
    │           └── MemberDashboard/
    └── public/
```

## Getting started

### Prerequisites
- Node.js
- A MongoDB instance (local or Atlas)

### Backend setup

```bash
cd backend
npm install
```

Create a `.env` file in `backend/`:
```
MONGO_URL=your_mongodb_connection_string
PORT=4000
```

```bash
npm start
```

### Frontend setup

```bash
cd frontend
npm install
npm start
```

The frontend runs on `http://localhost:3000` and expects the backend API at `http://localhost:4000`.

## API overview

| Route | Description |
|---|---|
| `POST /api/auth/register` | Register a new user |
| `POST /api/auth/signin` | Sign in with admission/employee ID + password |
| `GET /api/books/allbooks` | List all books |
| `GET /api/books?category=` | Get books by category |
| `POST /api/books/addbook` | Add a book (admin) |
| `PUT /api/books/updatebook/:id` | Update a book (admin) |
| `DELETE /api/books/removebook/:id` | Remove a book (admin) |
| `GET /api/categories/allcategories` | List categories |
| `POST /api/categories/addcategory` | Add a category (admin) |
| `POST /api/transactions/add-transaction` | Issue/reserve a book (admin) |
| `GET /api/transactions/all-transactions` | List all transactions |
| `GET /api/users/allmembers` | List all members (admin) |
| `PUT /api/users/updateuser/:id` | Update a user |
| `DELETE /api/users/deleteuser/:id` | Delete a user |

## Known limitations

- Admin-only routes currently trust an `isAdmin` flag passed in the request body rather than verifying it server-side against an authenticated session/JWT — this should be hardened with proper auth middleware before any real deployment.
- The password hash is included in the `/signin` response and the user object (including this field) is persisted to `localStorage` on the frontend; the password field should be stripped from the response and a token-based session should replace storing the full user object client-side.
- No automated tests yet.

## Author

[Vivek Gaddam](https://github.com/VivekGaddam)
