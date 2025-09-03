# 📝 Base Backend

A Node.js + Express backend for the **Thought Gallery** app.
It manages **users, authentication, and user-generated content (posts)** with a PostgreSQL database hosted on **Render**.

---

## 🚀 Features

* **User Authentication**

  * JWT-based login and signup
  * Password hashing with `bcrypt`
* **User Management**

  * Create, Read, Update, Delete (CRUD) users
  * Authenticated routes protected with middleware
* **Posts Management**

  * Each post belongs to a user (`user_id` foreign key)
  * **`ON DELETE CASCADE`** ensures user’s posts are deleted automatically when the user is removed
* **Authorization**

  * Users can only update/delete their own data
  * Protected API endpoints using `checkAuth` middleware

---

## 🗄️ Database Schema

### `users` table

```sql
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email TEXT UNIQUE NOT NULL,
  password TEXT NOT NULL,
  created_at TIMESTAMP DEFAULT NOW()
);
```

### `posts` table

```sql
CREATE TABLE posts (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  content TEXT NOT NULL,
  created_at TIMESTAMP DEFAULT NOW()
);
```

### 🔎 Why `ON DELETE CASCADE`?

* If a user is deleted → all their posts are deleted automatically.
* Prevents orphaned rows in the `posts` table.
* Keeps the database clean and consistent.

---

## ⚙️ Tech Stack

* **Backend**: Node.js + Express
* **Database**: PostgreSQL (Render)
* **Auth**: JWT (`jsonwebtoken`)
* **Security**: `bcrypt` for password hashing
* **Environment Variables**: `dotenv`

---

## 📂 Project Structure

```
src/
 ├── controllers/       # Business logic for users/posts
 ├── data/              # DB connection & migrations
 ├── middlewares/       # Validation & authentication middleware
 ├── routes/            # Express routes
 ├── util/              # Utility functions (auth helpers)
 ├── index.js           # Entry point
```

---

## 🔑 API Endpoints

### Users

| Method | Endpoint    | Protected | Description             |
| ------ | ----------- | --------- | ----------------------- |
| GET    | `/user`     | ✅         | Get all users           |
| GET    | `/user/:id` | ✅         | Get user by ID          |
| PUT    | `/user/:id` | ✅         | Update user (self only) |
| DELETE | `/user/:id` | ✅         | Delete user (self only) |

### Posts

| Method | Endpoint     | Protected | Description                      |
| ------ | ------------ | --------- | -------------------------------- |
| GET    | `/posts`     | ✅         | Get all posts                    |
| POST   | `/posts`     | ✅         | Create new post (logged-in user) |
| DELETE | `/posts/:id` | ✅         | Delete post (owner only)         |

---

## 🔧 Setup Instructions

1. Clone repository:

   ```bash
   git clone https://github.com/your-repo/thought-gallery-backend.git
   cd thought-gallery-backend
   ```

2. Install dependencies:

   ```bash
   npm install
   ```

3. Create a `.env` file:

   ```ini
   KEY=supersecret
   DB_USER=thoughts_gallery
   DB_HOST=dpg-xxxx.oregon-postgres.render.com
   DB_NAME=thoughts_gallery_db
   DB_PASSWORD=your_password
   DB_PORT=5432
   ```

4. Run server:

   ```bash
   npm start
   ```

   Server runs at: [http://localhost:3000](http://localhost:3000)

---

## ✅ Example Workflow

1. **Sign up** → `/signup`
2. **Login** → get JWT
3. Use JWT in headers:

   ```
   Authorization: Bearer <token>
   ```
4. **Create posts**
5. **Delete user** → all posts by that user are also removed (because of `ON DELETE CASCADE`).

---

## 📌 Notes

* Ensure PostgreSQL connection string is correct in `.env`.
* Use `pg` library for DB queries.
* Protected routes require a valid **JWT**.

---

Would you like me to also add **sample `curl` requests** (for signup, login, creating posts, etc.) in the README so you or your teammates can test APIs quickly?
