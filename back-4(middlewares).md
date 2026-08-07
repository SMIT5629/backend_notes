# ***MIDDLEWARE IN EXPRESS.JS — DETAILED & STRUCTURED***

---

# **1. What EXACTLY is Middleware?**
-Middleware is a function that runs between the request and the response.

### Express works on a **pipeline model**

Every request goes through a **chain of functions**.

```
Client Request
 ↓
Middleware 1
 ↓
Middleware 2
 ↓
Middleware 3
 ↓
Route Handler
 ↓
Response to Client
```

Each middleware decides:

* **Stop** the request
* **Modify** the request/response
* **Pass** it forward using `next()`

---

### Middleware is JUST a function

```js
(req, res, next) => {}
```

* `req` → incoming data
* `res` → outgoing response
* `next()` → moves to next middleware

 **If `next()` is not called and response not sent → request hangs**

---

# **2. Why Middleware is SO IMPORTANT ?**

Without middleware, Express would be very limited.

Middleware is used for:

* Parsing request body
* Authentication & Authorization
* Logging
* Error handling
* Security (Helmet, CORS)
* Session & cookies
* File upload
* Rate limiting

 **Almost everything in Express is middleware**

---

# **3. How Middleware EXECUTES ?**

### Example flow:

```js
app.use(mw1);
app.use(mw2);

app.get('/', handler);
```

Execution order:

```
mw1 → mw2 → handler
```

Order matters
Middleware runs **top to bottom**

---

# **4. Implementing Middleware (Step-by-Step)**

### Basic custom middleware

```js
const express = require('express');
const app = express();

const myMiddleware = (req, res, next) => {
 console.log("Middleware running");
 next();
};

app.use(myMiddleware);

app.get('/', (req, res) => {
 res.send("Hello World");
});
```

### What happens?

1. Request comes
2. `myMiddleware` runs
3. `next()` passes control
4. Route handler sends response

---

# **5. TYPES OF MIDDLEWARE**

---

# A. Built-in Middleware

Provided by Express itself.

---

### `express.json()`

 Used to read JSON data from request body.

Without it:

```js
req.body === undefined
```

With it:

```js
app.use(express.json());
```

#### Example:

```js
app.post('/data', (req, res) => {
 console.log(req.body);
 res.send("Data received");
});
```

Used in:

* REST APIs
* React / frontend → backend communication

---

### `express.urlencoded()`

 Used to parse **HTML form data**

```js
app.use(express.urlencoded({ extended: true }));
```

Used when:

```html
<form method="POST">
```

---

### `express.static()`

 Serves static files (CSS, JS, images)

```js
app.use(express.static('public'));
```

Folder:

```
public/
 ├─ style.css
 ├─ app.js
 └─ image.png
```

Access directly:

```
localhost:3000/style.css
```

 Mostly used in:

* EJS
* Monolithic apps

---

# B. Third-Party Middleware

Installed via npm.

---

### Morgan

**Meaning:**
Morgan is a middleware that **logs every HTTP request** in the console.

**Example:**

```js
const morgan = require('morgan');
app.use(morgan('dev'));
```

 Shows: `GET /login 200 4ms`

---

### Cookie-Parser

**Meaning:**
Cookie-parser is a middleware that **reads cookies** sent by the browser.

**Example:**

```js
const cookieParser = require('cookie-parser');
app.use(cookieParser());

app.get('/', (req, res) => {
 console.log(req.cookies);
 res.send('Cookies checked');
});
```

---

# C. Custom Middleware (MOST IMPORTANT)

You create logic based on your app needs.

---

### Example: Request Time Logger

```js
const timeLogger = (req, res, next) => {
 req.time = new Date();
 next();
};

app.use(timeLogger);

app.get('/', (req, res) => {
 res.send(req.time);
});
```

 Middleware can **add data to req**

---
# **6. Levels of Middleware in Express.js**
---
### There are **main 3 levels**:

1. **Application-Level Middleware**
2. **Router-Level Middleware**
3. **Error-Handling Middleware** (special level)

---

## Application-Level Middleware

### What is it?

Middleware that is applied to the **entire Express application**.

 It runs for **every request** (or for a specific path) **before routes**.

### Where is it defined?

Using:

```js
app.use()
app.get()
app.post()
```

---

### Example 1: Runs for **ALL requests**

```js
const express = require('express');
const app = express();

app.use((req, res, next) => {
 console.log('Application-level middleware executed');
 next();
});

app.get('/', (req, res) => {
 res.send('Home Page');
});

app.get('/about', (req, res) => {
 res.send('About Page');
});
```

### Flow:

```
Request → Middleware → Route → Response
```

 This middleware runs for:

* `/`
* `/about`
* **every route**

---

### Example 2: Middleware for a **specific path**

```js
app.use('/admin', (req, res, next) => {
 console.log('Admin middleware');
 next();
});

app.get('/admin/dashboard', (req, res) => {
 res.send('Admin Dashboard');
});
```

### Runs ONLY when URL starts with:

```
/admin
```

---

### Common Uses of Application-Level Middleware

 Logging
 Body parsing (`express.json()`)
 Authentication checks
 CORS
 Helmet security
 Session handling

Example:

```js
app.use(express.json());
app.use(cors());
```

---

## Router-Level Middleware

### What is it?

Middleware that applies **only to a specific router**, not the whole app.

 Used when your app is **modular** (users, products, admin, etc.)

---

### Why use Router-Level Middleware?

Because:

* App becomes **clean**
* Logic is **separated**
* Easy to **scale**

---

### Folder structure (real-world)

```
project/
 ├── app.js
 ├── routes/
 │ ├── userRoutes.js
 │ └── adminRoutes.js
```

---

### Example: Router-Level Middleware

#### `routes/userRoutes.js`

```js
const express = require('express');
const router = express.Router();

// Router-level middleware
router.use((req, res, next) => {
 console.log('User router middleware');
 next();
});

router.get('/profile', (req, res) => {
 res.send('User Profile');
});

module.exports = router;
```

---

#### `app.js`

```js
const express = require('express');
const app = express();
const userRoutes = require('./routes/userRoutes');

app.use('/user', userRoutes);

app.listen(3000);
```

---

### Request Flow:

```
Request → App Middleware → Router Middleware → Route Handler
```

 This middleware runs ONLY for:

```
/user/profile
```

 Not for:

```
/admin
```

---

### Route-Specific Router Middleware

```js
const isLoggedIn = (req, res, next) => {
 if (req.session.user) {
 next();
 } else {
 res.send('Login required');
 }
};

router.get('/dashboard', isLoggedIn, (req, res) => {
 res.send('Dashboard');
});
```

---

## Error-Handling Middleware (Special Level)

### What makes it special?

It has **4 parameters** instead of 3:

```js
(err, req, res, next)
```

 Express knows this is an **error middleware** automatically.

---

### Example: Global Error Middleware

```js
app.use((err, req, res, next) => {
 console.error(err.message);
 res.status(500).send('Something went wrong');
});
```

---

### Throwing an error from middleware or route

```js
app.get('/error', (req, res) => {
 throw new Error('This is an error');
});
```

or

```js
next(new Error('Custom error'));
```

 Control goes **directly** to error-handling middleware.

---

## Complete Middleware Execution Order

```
1. Application-level middleware
2. Router-level middleware
3. Route handler
4. Error-handling middleware (only if error occurs)
```

---

## Real-World Example (Authentication)

```js
const authMiddleware = (req, res, next) => {
 if (req.headers.token === '123') {
 next();
 } else {
 res.status(401).send('Unauthorized');
 }
};

app.use('/dashboard', authMiddleware);

app.get('/dashboard', (req, res) => {
 res.send('Welcome to dashboard');
});
```

---

# **7. SECURITY MIDDLEWARE**

---

# Helmet

Adds secure HTTP headers.

```bash
npm install helmet
```

```js
const helmet = require('helmet');
app.use(helmet());
```

Protects against:

* XSS
* Clickjacking
* MIME attacks

---

# CORS

Controls cross-origin access.

```bash
npm install cors
```

```js
const cors = require('cors');

app.use(cors({
 origin: 'http://localhost:3000',
 credentials: true
}));
```

Used when:

* React frontend
* Express backend
* Different ports/domains

---

## **9. REAL PROJECT FLOW (Important)**

```
Request
 ↓
CORS
 ↓
Helmet
 ↓
Body Parser
 ↓
Auth Middleware
 ↓
Route Handler
 ↓
Error Middleware
```

---

## FINAL MEMORY RULE

> **Middleware = Function that controls request flow**

If you understand:

* `req`
* `res`
* `next()`
* execution order

 You understand Express deeply.

---
