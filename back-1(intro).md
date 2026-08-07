# Node.js Fundamentals -- Detailed Notes

---

# 1. Starting with Node.js -- The Beginning

## What is Node.js?

Node.js is a JavaScript runtime that allows JavaScript to run outside
the browser.
It uses the V8 engine internally and provides extra features like:

- File system access
- Creating servers
- Networking
- Event loop
- Asynchronous architecture

---

## Tools Required

### Node.js LTS

Provides stable runtime + NPM.

### VS Code

Best editor for Node development (terminal + debugging + extensions).

### Postman / Thunder Client

Used to test APIs without needing a frontend UI.

---

## Running Your First Script ("Namaste Duniya")

1. Create a file:

    ```js
    console.log("Namaste Duniya");
    ```

2. Run in terminal:

 node index.js

### What happens?

Node → passes code to V8 → executes and prints output.

---

## NPM Basics

NPM = Node Package Manager used to install/manage dependencies.

### Initialize a project

 npm init -y

Creates `package.json` containing:
- project details
- dependencies
- scripts
- metadata

### Install a package

 npm install express

### Install globally

 npm install -g nodemon

---

## package.json

Stores the entire project structure:
- Dependencies
- Dev dependencies
- Scripts
- Versioning
- Project metadata

---

# 2. Creating Server -- Writing Our First Server

## What is a Server?

A server is a system that:
- Accepts requests
- Processes them
- Sends responses back

Node.js is ideal for servers because of its:
- Event-driven architecture
- Non-blocking I/O
- Single-threaded but highly scalable model

---

## Creating First Node Server (http module)

```js
const http = require("http");

const server = http.createServer((req, res) => {
 res.write("Hello from Node.js Server");
 res.end();
});

server.listen(3000, () => {
 console.log("Server running at port 3000");
});
```

Run:

 node server.js

Visit:

 http://localhost:3000

---

## Understanding Request & Response

- `res.write()` → send data
- `res.end()` → end the response
<<<<<<< HEAD
 Without `res.end()`, the request never closes.
=======
  Without `res.end()`, the request never closes.
>>>>>>> 87a599d527363e60466a13eb25e5729c9a923422

---

## Routing in Node.js HTTP Server

```js
const http = require("http");

const server = http.createServer((req, res) => {
 if (req.url === "/") {
 res.end("Home Page");
 } else if (req.url === "/about") {
 res.end("About Page");
 } else {
 res.end("404 Page Not Found");
 }
});

server.listen(3000);
```

---

# 3. HTTP Status Codes

## 1XX -- Informational

Indicates request is received and processing continues.
Example: 100 Continue

## 2XX -- Success

- 200 OK -- Successful
- 201 Created -- Resource created

Example:

```js
res.writeHead(200, { "Content-Type": "text/plain" });
res.end("Success");
```

---

## 3XX -- Redirection

- 301 Moved Permanently
- 302 Found

Example:

```js
res.writeHead(302, { Location: "/login" });
res.end();
```

---

## 4XX -- Client Errors

- 400 Bad Request
- 401 Unauthorized
- 403 Forbidden
- 404 Not Found
- 422 Unprocessable Entity

Example:

```js
res.writeHead(404);
res.end("Page Not Found");
```

---

## 5XX -- Server Errors

- 500 Internal Server Error
- 503 Service Unavailable

Example:

```js
res.writeHead(500);
res.end("Server Error");
```

---

# 4. Nodemon -- Automatic Server Restart

## Install nodemon (global)

 npm install -g nodemon

## Run with nodemon

 nodemon server.js

## Add script in package.json

```json
"scripts": {
 "start": "node server.js",
 "dev": "nodemon server.js"
}
```

Run:

 npm run dev

### Benefit:

Nodemon automatically restarts the server whenever you save your file
-- no need to restart manually.

---

# 5. Backend Architectures -- MVC, SOA & REST APIs

*A clean explanation with examples*

---

## 1. Different Architectures in Backend

Backend systems can be organized using different architectures depending on size, complexity, and scalability needs.

### 1.1 MVC (Model–View–Controller)

A structured architecture used to keep backend code clean and organized.

### 1.2 SOA (Service-Oriented Architecture)

A large-scale architecture that divides the backend into multiple small services (sub-servers), each handling a specific job.

---

## 2. MVC Architecture

MVC divides a backend into three main components:

### 2.1 Model

- Represents data and database logic.
- Defines schemas and interacts with the database.

**Example (Node.js + Mongoose):**

```js
const mongoose = require("mongoose");

const taskSchema = new mongoose.Schema({
 text: String,
 completed: Boolean
});

module.exports = mongoose.model("Task", taskSchema);
```

### 2.2 View

- Represents UI or output.
- In REST APIs, the View is usually not used because the frontend (React, Angular, etc.) handles UI.
- In EJS/Laravel/Django, View renders HTML templates.

### 2.3 Controller

- Contains business logic.
- Receives request → processes → returns response.

**Example:**

```js
const Task = require("../models/taskModel");

exports.getTasks = async (req, res) => {
 const tasks = await Task.find();
 res.json(tasks);
};
```

### 2.4 MVC Folder Structure Example

```
/controllers
 taskController.js
/models
 taskModel.js
/routes
 taskRoutes.js
server.js
```

---

## 3. MVC in the Context of REST APIs

REST APIs follow:

- Client & Server are separate
- Communication via HTTP methods
- Responses in JSON

MVC fits REST APIs perfectly:

<<<<<<< HEAD
| REST API Component | MVC Part |
| -------------------- | ----------- |
| Endpoint `/tasks` | Route |
| Business Logic | Controller |
| Database Operations | Model |
| No HTML UI | View unused |
=======
| REST API Component  | MVC Part    |
| -------------------- | ----------- |
| Endpoint `/tasks`    | Route       |
| Business Logic        | Controller  |
| Database Operations   | Model       |
| No HTML UI             | View unused |
>>>>>>> 87a599d527363e60466a13eb25e5729c9a923422

**Route Example**

```js
const express = require("express");
const router = express.Router();
const { getTasks } = require("../controllers/taskController");

router.get("/tasks", getTasks);
module.exports = router;
```

---

## 4. SOA (Service-Oriented Architecture)

SOA is an architecture where the backend is split into multiple small services.

Each service has:
- Its own server
- Its own database
- Its own MVC structure

### Example: Food Delivery App

```
User Service (login, signup)
Order Service (create order)
Restaurant Service (menu)
Payment Service (payments)
```

Each is a separate mini-backend, communicating via REST APIs.

Example:

```
User Service: GET /users/profile
Order Service: POST /orders/new
Payment Service: POST /pay
```

---

## 5. MVC vs SOA (Simple Comparison)

<<<<<<< HEAD
| Feature | MVC | SOA |
| ------------ | ------------------- | ------------------------- |
| Structure | Inside one backend | Many backends (services) |
| Use Case | Small/Medium apps | Large systems |
| Servers | Single | Multiple |
| Relationship | MVC is part of SOA | SOA = many MVC apps |
=======
| Feature      | MVC                | SOA                      |
| ------------ | ------------------- | ------------------------- |
| Structure    | Inside one backend  | Many backends (services)  |
| Use Case     | Small/Medium apps   | Large systems              |
| Servers      | Single               | Multiple                   |
| Relationship | MVC is part of SOA  | SOA = many MVC apps        |
>>>>>>> 87a599d527363e60466a13eb25e5729c9a923422

---

## Final Summary

- MVC keeps backend code clean using Model, View, Controller.
- REST APIs commonly use MVC.
- SOA is a big architecture where backend is divided into many services.
- Each SOA service internally uses its own MVC.
