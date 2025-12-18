
# **1️⃣What is a REST API?**
---

## 1.1 What does REST mean?

REST = **Representational State Transfer**.
Think of it like a **set of rules** that tells your backend and frontend **how to talk using HTTP**.

Key points:

* **Client** and **server** are separated.
* Communication is **stateless**.
* Server sends **representations of resources** (usually JSON).

---

## 1.2 What is an API?

API = **Application Programming Interface**.

Example in web dev:

* Frontend → React / Angular / Mobile App
* Backend → Node.js + Express
* API → Endpoints your backend exposes

Think: API is the **menu in a restaurant**. You (client) don’t care how the kitchen (server) cooks your food, you just call the dish you want (endpoint) and get it delivered (JSON response).

---

## 1.3 REST API in simple terms

A REST API:

* Uses **HTTP** (`GET`, `POST`, `PUT`, `DELETE`)
* Uses **URLs to identify resources** (`/users`, `/products`)
* Sends/receives **JSON**
* Is **stateless** → every request is independent

---

## 1.4 REST API Example (Express)

```js
// Simple Express server
const express = require('express');
const app = express();
app.use(express.json());

const users = [
  { id: 1, name: "Smit" },
  { id: 2, name: "Aashu" }
];

// GET all users
app.get('/users', (req, res) => {
  res.status(200).json(users);
});

// GET single user by ID
app.get('/users/:id', (req, res) => {
  const user = users.find(u => u.id === parseInt(req.params.id));
  if (!user) return res.status(404).json({ error: "User not found" });
  res.json(user);
});

// POST new user
app.post('/users', (req, res) => {
  const newUser = { id: users.length + 1, name: req.body.name };
  users.push(newUser);
  res.status(201).json(newUser);
});

app.listen(3000, () => console.log("Server running on port 3000"));
```

**Breakdown:**

| Part     | Meaning                      |
| -------- | ---------------------------- |
| `/users` | Resource                     |
| `GET`    | Fetch data                   |
| `POST`   | Add new data                 |
| `:id`    | Path parameter               |
| `JSON`   | Data format                  |
| `status` | HTTP status code for clarity |

✅ You can actually **run this server** and test it in Postman.

---

## 1.5 REST principles

1. **Client–Server separation** → Frontend + backend are independent
2. **Statelessness** → No memory of past requests
3. **Uniform Interface** → Standardized ways to interact (`GET`, `POST`, `PUT`, `DELETE`)
4. **Resource-based URLs** → `/users`, `/orders`
5. **Use HTTP status codes** → 200, 404, 500 etc


---

# **2️⃣ REST API Versioning (`/v1/`)**
---

## 2.1. Why API versioning is important

APIs **evolve over time**:

* New features added → `GET /users` now returns `age` and `email`
* Some fields removed → `username` removed from response
* Response format changes → `camelCase` → `snake_case`

If clients (frontend/mobile apps) keep calling **old endpoints**:

* Their code may **break**
* They may **expect a field that no longer exists**
* There’s **no way to support old apps safely**

**Versioning solves this problem** by letting multiple versions coexist:

```http
GET /api/v1/users   → old version, safe for existing apps
GET /api/v2/users   → new version with extra fields
```

> Official REST guidelines (Fielding, 2000 – the REST thesis) recommend **URI versioning or header versioning** to maintain **backward compatibility**.

---

## 2.2. Types of Versioning

1. **URI Versioning** (most common)

```http
GET /api/v1/users
GET /api/v2/users
```

Pros:

* Easy to read & debug
* Cache-friendly
* Clear in logs

Cons:

* Version info is part of URL → less flexible if you want content negotiation

---

2. **Header Versioning**

```http
GET /users
Accept: application/vnd.example.v1+json
```

Pros:

* URL stays clean
* Can support multiple versions in headers

Cons:

* Harder to test in browsers
* Less visible

---

3. **Query Parameter Versioning**

```http
GET /users?version=1
```

Less common in production; mostly for **temporary testing**.

---

## 2.3. When to increase version

You **increment API version** when **breaking changes occur**:

* Field removed or renamed
* Response structure changes
* Critical business logic changes that affect clients

> Minor additions (like adding a new optional field) **don’t always require a new version**, but sometimes it’s safer to version anyway.

---

## 2.4. Real-world Express Example

### Step 1 – Create folder structure

```
project/
├─ routes/
│  ├─ v1/
│  │  └─ users.js
│  └─ v2/
│     └─ users.js
└─ index.js
```

---

### Step 2 – v1/users.js

```js
const express = require('express');
const router = express.Router();

// GET all users (v1)
router.get('/', (req, res) => {
  res.json([
    { id: 1, name: "Smit" },
    { id: 2, name: "Aashu" }
  ]);
});

module.exports = router;
```

---

### Step 3 – v2/users.js

```js
const express = require('express');
const router = express.Router();

// GET all users (v2) – added email field
router.get('/', (req, res) => {
  res.json([
    { id: 1, name: "Smit", email: "smit@gmail.com" },
    { id: 2, name: "Aashu", email: "aashu@gmail.com" }
  ]);
});

module.exports = router;
```

---

### Step 4 – index.js

```js
const express = require('express');
const app = express();

// Use JSON middleware
app.use(express.json());

// Mount API versions
app.use('/api/v1/users', require('./routes/v1/users'));
app.use('/api/v2/users', require('./routes/v2/users'));

app.listen(3000, () => console.log("Server running on port 3000"));
```

✅ Now you can test:

* `GET http://localhost:3000/api/v1/users` → returns **old format**
* `GET http://localhost:3000/api/v2/users` → returns **new format with emails**

---

## 2.5. Why `/v1/` is preferred (Official Reasoning)

* **Easy to read** → developers instantly know version
* **Cache-friendly** → URLs can be cached separately
* **Clear backward compatibility** → old apps keep working
* **Widely adopted** → used by GitHub, Stripe, AWS, Twilio

Official docs references:

* [GitHub API versioning](https://docs.github.com/en/rest/overview/api-versions)
* [Stripe API versioning](https://stripe.com/docs/api/versioning)
* [Microsoft REST API Guidelines](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#81-versioning)

---

### 2.6. Quick Tips

* Always **start with `/v1/`** even if first version
* Keep **old versions alive** until all clients migrate
* **Document differences clearly** in API docs
* For **minor non-breaking changes**, you can stay on the same version

---
Perfect, Smit 💙! Let’s go **deep into Postman** like a real backend developer, covering **sending requests, saving collections, and writing tests**, with **real examples you can run with your Express APIs**. I’ll also explain **why and how each feature is used in development**.

---

# **3️⃣ Using Postman for API Testing & Development** 

---

## 3.1 What is Postman?

Postman is an **API client** that lets you:

* Send HTTP requests to your backend
* Inspect responses (body, headers, status codes)
* Save requests as collections for re-use
* Write **automated tests** to check responses
* Share APIs with your team

Think of Postman as **your “API playground”** where you can **experiment safely** before frontend integration.

---

## 3.2 Sending Requests

### 3.2.1 HTTP Methods

* **GET** → Fetch data
* **POST** → Create data
* **PUT / PATCH** → Update data
* **DELETE** → Remove data

### 3.2.2 Example: Testing Your Express API

Suppose you have this route in Express:

```js
// index.js
app.post('/api/v1/register', (req, res) => {
  const { email, password, username } = req.body;
  if (!email || !password || !username) {
    return res.status(400).json({ error: "Missing fields" });
  }
  res.status(201).json({ message: "User registered", user: { email, username } });
});
```

#### Step in Postman:

1. Open Postman → Click **New Request**
2. Set method → **POST**
3. URL → `http://localhost:3000/api/v1/register`
4. Go to **Body → raw → JSON** and send:

```json
{
  "email": "smit@gmail.com",
  "password": "123456",
  "username": "SmitP"
}
```

5. Click **Send** → You’ll see response:

```json
{
  "message": "User registered",
  "user": { "email": "smit@gmail.com", "username": "SmitP" }
}
```

✅ This shows **request headers, body, status code, response time**, and allows you to debug quickly.

---

### 3.2.3 GET request example

```js
// Express GET route
app.get('/api/v1/users', (req, res) => {
  res.json([
    { id: 1, name: "Smit" },
    { id: 2, name: "Aashu" }
  ]);
});
```

Postman GET request:

* Method → GET
* URL → `http://localhost:3000/api/v1/users`
* Click **Send** → See JSON array response

---

## 3.3. Saving Collections

A **collection** in Postman is a **folder of related requests**, e.g., all Auth endpoints:

```
Auth APIs
 ├── Register
 ├── Login
 └── Logout
```

### Why Collections are useful:

* Organize requests by resource
* Share with your team
* Use in **automated testing / CI/CD**
* Easily rerun API requests

#### How to save:

1. Create request in Postman
2. Click **Save → Save to Collection → Name it**
3. Add description (optional, recommended for clarity)

Example: Save `/register`, `/login`, `/logout` under `Auth APIs` collection.

---

## 3.4 Writing Tests in Postman

Postman lets you write **JavaScript-based tests** that run **after a request**:

### 3.4.1 Check HTTP Status

```js
pm.test("Status is 201", function () {
    pm.response.to.have.status(201);
});
```

### 3.4.2 Check JSON Response

```js
pm.test("Response has username", function () {
    const jsonData = pm.response.json();
    pm.expect(jsonData.user).to.have.property("username");
});
```

### 3.4.3 Check Response Time

```js
pm.test("Response time < 200ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(200);
});
```

✅ This is how you **ensure API behaves as expected**, even before frontend integration.

---

## 3.5 Environment & Variables (Optional but Pro Tip)

* Postman supports **environments** → store base URLs, tokens
* Example:

```
Environment: Local
BASE_URL = http://localhost:3000/api/v1
```

Then in request:

```
{{BASE_URL}}/register
```

* This makes **switching from local → staging → production** effortless.

---

## 3.6 Using Postman with Express – Real Workflow

1. Build your **Express endpoints**
2. Test endpoints in **Postman**
3. Save them as **collections**
4. Write **tests** to check response, status, and schema
5. Run **collection runner** for multiple requests → useful in **CI/CD**

---

### Mental Model 🧠

```
Express API
   ↓ HTTP
Postman Client
   ↓ Send Request
   ↓ Inspect Response
   ↓ Run Tests / Save Collections
Frontend (React / Mobile) consumes the API
```

---
# **4️⃣Understanding and Working With Status code , 2xx (Success) , 4xx (Client Errors) , 5xx (Server Errors).** 

---

## 4.1 What is an HTTP Status Code? 

When a **client (browser / frontend / Postman)** sends a request to a **server**, the server always replies with:

* **Response body** (data)
* **Status code** (what happened)

Think of the **status code as the server’s short explanation**.

📌 Official definition (from HTTP spec / MDN idea):

> HTTP status codes indicate whether a specific HTTP request has been successfully completed.

---

## 4.2 Why Status Codes Matter 

They help:

* Browser decide what to do
* Frontend show correct message
* APIs behave predictably
* Debug errors faster

⚠️ Returning wrong status codes = bad API design

---

## 4.3 Status Code Groups (Overview)

| Range | Meaning      |
| ----- | ------------ |
| 2xx   | Success      |
| 4xx   | Client Error |
| 5xx   | Server Error |

We’ll **deeply understand 2xx, 4xx, 5xx** one by one.

---

# ✅ 2xx — Success Codes

➡️ Means: **Request was received, understood, and processed successfully**

### 🔹 Common 2xx Codes

| Code | Meaning    |
| ---- | ---------- |
| 200  | OK         |
| 201  | Created    |
| 204  | No Content |

---

### 🔸 200 OK (Most Common)

**When to use:**
✔ Data fetched successfully
✔ Login success
✔ Normal response

**Example (Express):**

```js
res.status(200).json({ message: "User fetched successfully" });
```

📌 Real-world analogy:
You ordered food → food arrives → everything fine ✅

---

### 🔸 201 Created

**When to use:**
✔ New resource created (POST request)

**Example:**

```js
res.status(201).json({ message: "User registered successfully" });
```

📌 Important:
201 is **better than 200** for creation

---

### 🔸 204 No Content

**When to use:**
✔ Request succeeded but no data to return
✔ Delete operation

**Example:**

```js
res.status(204).send();
```

📌 No JSON, no message — just success

---

# ❌ 4xx — Client Errors

➡️ Means: **Client made a mistake**

⚠️ Server is working fine
⚠️ Request is wrong

---

### 🔹 Common 4xx Codes

| Code | Meaning          |
| ---- | ---------------- |
| 400  | Bad Request      |
| 401  | Unauthorized     |
| 403  | Forbidden        |
| 404  | Not Found        |
| 422  | Validation Error |

---

### 🔸 400 Bad Request

**When to use:**
✔ Missing fields
✔ Invalid data

**Example:**

```js
if (!email) {
  return res.status(400).json({ error: "Email is required" });
}
```

📌 Client sent **wrong input**

---

### 🔸 401 Unauthorized

**When to use:**
✔ No login
✔ Invalid token

**Example:**

```js
res.status(401).json({ error: "Please login first" });
```

📌 Meaning: *“Who are you?”*

---

### 🔸 403 Forbidden

**When to use:**
✔ User is logged in
✔ But not allowed

**Example:**

```js
res.status(403).json({ error: "Access denied" });
```

📌 Difference:

* 401 → not logged in
* 403 → logged in but **no permission**

---

### 🔸 404 Not Found

**When to use:**
✔ Resource doesn’t exist

**Example:**

```js
res.status(404).json({ error: "User not found" });
```

📌 URL exists, but data doesn’t

---

# 🔥 5xx — Server Errors

➡️ Means: **Server failed**

⚠️ Client request was correct
⚠️ Bug / crash / DB issue

---

### 🔹 Common 5xx Codes

| Code | Meaning               |
| ---- | --------------------- |
| 500  | Internal Server Error |
| 502  | Bad Gateway           |
| 503  | Service Unavailable   |

---

### 🔸 500 Internal Server Error

**When to use:**
✔ Unexpected error
✔ Database crash
✔ Bug in code

**Example:**

```js
try {
  // DB logic
} catch (error) {
  res.status(500).json({ error: "Something went wrong" });
}
```

📌 Never expose real error to client

---

## 4.4 Very Important Rule (Official Best Practice)

✔ Always return **correct status code**
✔ Don’t send `200 OK` for errors
✔ Frontend depends on it

❌ Bad:

```js
res.status(200).json({ error: "Invalid password" });
```

✅ Good:

```js
res.status(401).json({ error: "Invalid password" });
```

---

## 🧠 Quick Memory Trick

* **2xx → “I did it”**
* **4xx → “You messed up”**
* **5xx → “I messed up”**

---
# **5️⃣ Validating API Inputs Using libraries like express-validator or Sanitization .**
---

## 5.1 Why API Input Validation is REQUIRED 

**Client input is never trusted.**
Validation ensures:

* Correct data type
* Required fields exist
* Prevents bad data reaching DB
* Reduces crashes & security risks

📌 Official idea (Express ecosystem + OWASP):

> Always validate and sanitize user input on the server side.

---

## 5.2 Validation vs Sanitization

| Term         | Meaning                      |
| ------------ | ---------------------------- |
| Validation   | Checking data is **correct** |
| Sanitization | Cleaning data to be **safe** |

### Example:

Input:

```text
email = "   USER@GMAIL.COM   "
```

* **Validation** → Is this a valid email?
* **Sanitization** → Trim spaces, lowercase it

---

## 5.3 express-validator (Officially Recommended)

📌 Built on **validator.js** (industry standard)
📌 Used by Express community

Install:

```bash
npm install express-validator
```

---

## 5.4 Basic Structure (Understand First)

express-validator works as **middleware**.

```js
[
  validation rules,
  validation result checker
]
```

Think:

> Request → validation → controller → response

---

## 5.5 Example: User Registration API

### Step 1️⃣ Import tools

```js
const { body, validationResult } = require("express-validator");
```

---

### Step 2️⃣ Add validation rules

```js
app.post(
  "/register",

  // 🔹 Validation rules
  [
    body("email")
      .isEmail()
      .withMessage("Invalid email"),

    body("password")
      .isLength({ min: 6 })
      .withMessage("Password must be at least 6 characters"),

    body("age")
      .isInt({ min: 18 })
      .withMessage("Age must be 18+"),
  ],

  // 🔹 Controller
  (req, res) => {
    const errors = validationResult(req);

    if (!errors.isEmpty()) {
      return res.status(422).json({
        errors: errors.array(),
      });
    }

    res.status(201).json({ message: "User registered successfully" });
  }
);
```

---

## 5.6 Why Status Code **422** Here?

📌 **422 Unprocessable Entity**

* Syntax is correct
* Data format is wrong

✔ Official REST practice

---

## 5.7 Sanitization (Cleaning Input)

express-validator also supports sanitization.

### Example:

```js
body("email").trim().normalizeEmail()
```

✔ Removes spaces
✔ Lowercases email
✔ Prevents duplicates

---

## 5.8 Common Sanitization Methods

| Method        | Purpose             |
| ------------- | ------------------- |
| trim()        | Remove spaces       |
| escape()      | Prevent HTML/script |
| toInt()       | Convert to number   |
| toLowerCase() | Normalize text      |

---

## 5.9 Security Example (XSS Protection)

Input:

```html
<script>alert("hack")</script>
```

Sanitize:

```js
body("comment").escape()
```

Stored safely as text, not executed.

---

## 5.10 Clean & Professional Pattern (Best Practice)

Create reusable validator file 👇

```js
// validators/userValidator.js
const { body } = require("express-validator");

exports.registerValidator = [
  body("email").isEmail(),
  body("password").isLength({ min: 6 }),
];
```

Use it:

```js
app.post("/register", registerValidator, controller);
```

---

## 🧠 Quick Memory Rule

* **Validation** → Is it correct?
* **Sanitization** → Is it safe?
* **422** → Validation error
* **Middleware** → Runs before controller

---
# 6️⃣**Security Handling - Rate Limiting with express-rate-limit ,XSS Attack , CSRF Attack , DOS Attack.**
---

##  Why Security Handling is Needed (Big Picture)

Any public API is exposed to:

* Too many requests (DoS)
* Malicious scripts (XSS)
* Fake form submissions (CSRF)
* Abuse without authentication

📌 **Golden rule (OWASP):**

> Never trust client input or client behavior.

---

## 6.1: Rate Limiting (DoS Protection)

###  What is Rate Limiting?

👉 Restricts **how many requests** a client can make in a given time.

**Prevents:**

* DoS attacks
* Brute-force login attacks
* API abuse

---

### express-rate-limit (Official Express Middleware)

Install:

```bash
npm install express-rate-limit
```

---

## Basic Example

```js
const rateLimit = require("express-rate-limit");

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // 100 requests per IP
  message: "Too many requests, try later",
});

app.use(limiter);
```

📌 Meaning:

* Same IP
* Max 100 requests
* In 15 minutes

---

## Best Practice

Apply stricter limit on sensitive routes:

```js
app.use("/login", loginLimiter);
```

---

## 6.2: XSS (Cross-Site Scripting)

### What is XSS?

👉 Attacker injects **malicious JavaScript** into your app.

### Example Attack Input:

```html
<script>alert("Hacked")</script>
```

If stored and rendered → script executes ❌

---

## How XSS Happens in APIs?

* User comments
* Form inputs
* Search fields

---

## XSS Protection (Sanitization)

### Use express-validator:

```js
body("comment").escape()
```

### Or use helmet (recommended):

```bash
npm install helmet
```

```js
const helmet = require("helmet");
app.use(helmet());
```

📌 Helmet sets safe HTTP headers automatically.

---

## Key Rule:

* **Escape input**
* **Never trust frontend sanitization alone**

---

## 6.3: CSRF (Cross-Site Request Forgery)

### What is CSRF?

👉 Logged-in user unknowingly performs an action.

### Example:

* You’re logged into bank.com
* You visit malicious site
* That site sends a request to bank.com using your cookies

💥 Money transferred without consent

---

### CSRF Happens When:

* Cookies used for auth
* No CSRF protection

---

### CSRF Protection

Use **csurf** middleware (for cookie-based auth):

```bash
npm install csurf
```

```js
const csrf = require("csurf");
const csrfProtection = csrf({ cookie: true });

app.post("/transfer", csrfProtection, (req, res) => {
  res.send("Money transferred");
});
```

📌 Token must match → request allowed

---

### Important Note

If you use:

* **JWT in headers** → CSRF risk is LOW
* **Cookies** → CSRF protection REQUIRED

---

##  6.4: DoS (Denial of Service)

### What is DoS?

👉 Overwhelming server with:

* Huge traffic
* Infinite requests
* Heavy payloads

Result: Server crash ❌

---

### DoS Protection Techniques

### 1️⃣ Rate Limiting (Already covered)

✔ Primary defense

### 2️⃣ Limit JSON body size

```js
app.use(express.json({ limit: "10kb" }));
```

### 3️⃣ Timeout & Error Handling

✔ Prevent long-running requests

---

## 🧠 Quick Comparison Table

| Attack      | What it Does    | Protection      |
| ----------- | --------------- | --------------- |
| XSS         | Injects JS      | Escape + Helmet |
| CSRF        | Fake requests   | CSRF tokens     |
| DoS         | Floods server   | Rate limiting   |
| Brute Force | Guess passwords | Rate limiting   |

---

## 🧠 Memory Trick

* **XSS** → Script inside input
* **CSRF** → Fake request using cookies
* **DoS** → Too many requests
* **Rate limit** → Traffic control 🚦

---