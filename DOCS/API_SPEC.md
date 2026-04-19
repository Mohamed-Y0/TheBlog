# API Specification - TheBlog v1

Base URL: `/api/v1`

## 📝 Articles

### 1. Get All Articles
- **Endpoint:** `GET /articles`
- **Description:** Retrieves a list of all articles.
- **Response:** `200 OK`
  ```json
  {
    "status": "success",
    "results": 1,
    "data": [
      {
        "title": "Article Title",
        "slug": "article-title",
        "content": "...",
        "category": "Tech",
        "readTime": 5,
        "viewCount": 0,
        "status": "published",
        "createdAt": "...",
        "updatedAt": "..."
      }
    ]
  }
  ```

### 2. Create Article
- **Endpoint:** `POST /articles`
- **Description:** Creates a new article.
- **Request Body:**
  - `title` (String, required)
  - `content` (String, required)
  - `category` (String, required)
  - `tags` (Array of Strings)
  - `status` (String: 'draft' or 'published')
  - `coverImage` (String, URL)
- **Response:** `201 Created`

### 3. Get Article by ID
- **Endpoint:** `GET /articles/:id`
- **Description:** Retrieves a single article by its ID.
- **Response:** `200 OK`

### 4. Update Article
- **Endpoint:** `PATCH /articles/:id`
- **Description:** Updates an existing article.
- **Request Body:** (Any article field)
- **Response:** `200 OK`

### 5. Delete Article
- **Endpoint:** `DELETE /articles/:id`
- **Description:** Deletes an article by its ID.
- **Response:** `204 No Content`

---

## 👤 Users & Authentication

### 1. Sign Up
- **Endpoint:** `POST /users`
- **Description:** Registers a new user.
- **Request Body:**
  - `name` (String, required)
  - `email` (String, required)
  - `password` (String, required)
  - `passwordConfirm` (String, required)
- **Response:** `201 Created`

### 2. Login
- **Endpoint:** `POST /users/login`
- **Description:** Authenticates a user and returns a JWT token in a cookie.
- **Request Body:**
  - `email` (String, required)
  - `password` (String, required)
- **Response:** `200 OK`
  ```json
  {
    "status": "success",
    "token": "...",
    "data": {
      "user": {
        "name": "...",
        "email": "...",
        "role": "..."
      }
    }
  }
  ```

### 3. Get All Users
- **Endpoint:** `GET /users`
- **Description:** Retrieves a list of all active users.
- **Response:** `200 OK`

### 4. Get User by ID
- **Endpoint:** `GET /users/:id`
- **Description:** Retrieves a single user by ID.
- **Response:** `200 OK`

### 5. Update User
- **Endpoint:** `PATCH /users/:id`
- **Description:** Updates user profile information.
- **Response:** `200 OK`

### 6. Delete User
- **Endpoint:** `DELETE /users/:id`
- **Description:** Performs a soft delete by setting `active: false` (managed in model middleware).
- **Response:** `204 No Content`
