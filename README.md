# Node.js + Express + MongoDB Backend Assignment

A beginner-friendly REST API project using Node.js, Express, MongoDB Atlas and Mongoose. It includes user registration/login/logout and post CRUD operations, organised into models, controllers, routes and middleware.

## 1. Requirements

- Node.js (LTS version)
- npm (installed with Node.js)
- MongoDB Atlas account (free cluster is sufficient)
- Postman Desktop
- Git and a GitHub account

Check Node and npm:

```bash
node -v
npm -v
```

## 2. Install the project

Open this folder in VS Code and run:

```bash
npm install
```

## 3. Configure MongoDB Atlas

1. Sign in at https://www.mongodb.com/atlas and create a free cluster.
2. In **Database Access**, create a database username and password. These are not necessarily your MongoDB website login details.
3. In **Network Access**, add your current IP address. For a temporary classroom setup, Atlas also offers `0.0.0.0/0` (access from anywhere); remove this rule after testing if you use it.
4. Open **Database > Connect > Drivers**, select Node.js, and copy the connection string.
5. In this project folder, make a copy of `.env.example` and name the copy `.env`.
6. Replace the placeholder in `MONGODB_URI` with your Atlas connection string. Put your database name before the `?`, for example `backend_assignment`.
7. If your database password contains special characters, URL-encode them before putting the password in the URI.
8. Replace `JWT_SECRET` with a long random private string. Never publish `.env` or share its values.

Example `.env` format (use your own values):

```env
PORT=5000
MONGODB_URI=mongodb+srv://YOUR_DB_USER:YOUR_DB_PASSWORD@YOUR_CLUSTER.mongodb.net/backend_assignment?retryWrites=true&w=majority
JWT_SECRET=use_a_long_random_private_secret_here
JWT_EXPIRES_IN=1d
```

## 4. Run the backend

Development mode (automatically restarts after code changes):

```bash
npm run dev
```

Or normal mode:

```bash
npm start
```

When successful, the terminal should show that MongoDB connected and the server is running at `http://localhost:5000`. Open that address in a browser; it should return `{"message":"Backend API is running"}`.

## 5. API endpoints

Base URL: `http://localhost:5000`

| Method | Endpoint | Purpose | Login required? |
|---|---|---|---|
| POST | `/api/auth/register` | Register a user | No |
| POST | `/api/auth/login` | Log in and receive a JWT | No |
| POST | `/api/auth/logout` | Logout (client discards token) | Yes |
| POST | `/api/posts` | Create a post | Yes |
| GET | `/api/posts` | Get all posts | No |
| GET | `/api/posts/:id` | Get one post | No |
| PUT | `/api/posts/:id` | Update your post | Yes; owner only |
| DELETE | `/api/posts/:id` | Delete your post | Yes; owner only |

### Example request bodies

Register (`POST /api/auth/register`):

```json
{
  "name": "Demo User",
  "email": "demo@example.com",
  "password": "pass1234"
}
```

Login (`POST /api/auth/login`):

```json
{
  "email": "demo@example.com",
  "password": "pass1234"
}
```

Create post (`POST /api/posts`):

```json
{
  "title": "My first post",
  "content": "This post was created using my Express API."
}
```

Update post (`PUT /api/posts/POST_ID`):

```json
{
  "title": "Updated post title",
  "content": "The post content has been updated."
}
```

For create, update and delete post requests, add this header after logging in:

```text
Authorization: Bearer YOUR_JWT_TOKEN
```

The token is returned by register/login. Copy it from the response and use it in Postman's Authorization tab (type **Bearer Token**) or the Headers tab. Logout is stateless JWT logout: remove/discard the token in the client. The token expires according to `JWT_EXPIRES_IN`.

## 6. Test using Postman Desktop

A Postman collection is included at `postman/Backend-Assignment.postman_collection.json`.

1. Start the backend with `npm run dev`.
2. Open Postman Desktop and import the JSON collection from the `postman` folder.
3. Run **Register** once with a new email. The response contains a token.
4. Copy that token into the collection variable `token` (collection variables) or set the Bearer Token on protected requests.
5. Run **Create Post**. Copy the `_id` from the returned post and set the collection variable `postId`.
6. Test **Get All Posts**, **Get Post By ID**, **Update Post**, and **Delete Post**.
7. Also test missing fields, a wrong password, an invalid post ID, and a protected request without a token. These should return error responses rather than crash the server.

## 7. Push to a new GitHub repository

Create a **new public repository** on GitHub. Do not put this project inside your existing OneDayOneYear repository.

In the VS Code terminal, from this project folder:

```bash
git init
git add .
git commit -m "Build Express MongoDB backend with CRUD APIs"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/YOUR-REPOSITORY.git
git push -u origin main
```

Replace the remote URL with the URL of the repository you created. Check GitHub to confirm the files are visible. `.env` is ignored by Git; commit `.env.example` only.

## 8. Folder structure

```text
backend-assignment/
├── server.js
├── package.json
├── .env.example
├── .gitignore
├── README.md
├── postman/
│   └── Backend-Assignment.postman_collection.json
└── src/
    ├── app.js
    ├── config/
    │   └── db.js
    ├── controllers/
    │   ├── authController.js
    │   └── postController.js
    ├── middleware/
    │   └── authMiddleware.js
    ├── models/
    │   ├── Post.js
    │   └── User.js
    └── routes/
        ├── authRoutes.js
        └── postRoutes.js
```

## 9. Concepts for the evaluation

- **Node.js:** runs JavaScript outside the browser.
- **Express:** framework used to create the server and API routes.
- **MongoDB:** NoSQL database that stores data as documents.
- **Mongoose:** ODM used to define schemas and communicate with MongoDB.
- **Model:** defines the structure and validation of database documents.
- **Route:** maps an HTTP method and URL to a controller function.
- **Controller:** contains the logic for handling a request and sending a response.
- **Middleware:** function that runs between the request and controller; here it verifies JWT authentication.
- **CRUD:** Create (POST), Read (GET), Update (PUT), Delete (DELETE).
- **bcrypt:** hashes passwords before they are stored; plain-text passwords are never saved.
- **JWT:** signed token used to identify a logged-in user on protected requests.
- **Postman:** sends HTTP requests and displays API responses for testing.

## Notes

- This is a learning project, not a production-ready authentication service. JWT logout here means the client discards its token; it is not a server-side token revocation system.
- Do not commit database credentials, JWT secrets, or the `.env` file.
- The project uses a separate public-read / authenticated-write posts API. Only the owner of a post can update or delete it.
