
---

# **📝 Logging Backend with Express.js**

This document explains **backend logging in Express.js**, why it is important, and how to use **Morgan, Winston, and Pino** with proper **error handling**.

---

## 📌 Why is Logging Important?

Logging is the process of recording events that happen inside a backend application.

Logging helps to:

* Debug errors in production
* Track API requests
* Monitor server behavior
* Understand application crashes
* Improve security and auditing

❌ `console.log()` is not reliable
✅ Logging libraries provide structured, timestamped, and persistent logs

---

## 🧠 Types of Logs

| Log Level | Description             |
| --------- | ----------------------- |
| info      | Normal application flow |
| warn      | Potential problem       |
| error     | Application failure     |
| debug     | Detailed internal info  |

---

## 🧩 Logging Libraries in Express.js

| Library     | Purpose                             |
| ----------- | ----------------------------------- |
| **Morgan**  | HTTP request logging                |
| **Winston** | Application & error logging         |
| **Pino**    | High-performance production logging |

---

# 1️⃣ Setting Up Morgan (HTTP Request Logging)

## Install Morgan

```bash
npm install morgan
```

## Use Morgan in Express

```js
const morgan = require("morgan");
app.use(morgan("dev"));
```

Morgan logs:

* HTTP method
* URL
* Status code
* Response time

---

## 🔹 Morgan Modes

### 1️⃣ dev

```js
app.use(morgan("dev"));
```

Output:

```
GET /api/notes 200 15ms
```

✔ Colored
✔ Best for development

---

### 2️⃣ short

```js
app.use(morgan("short"));
```

Output:

```
GET /api/notes HTTP/1.1 200 15ms
```

✔ Medium detail

---

### 3️⃣ tiny

```js
app.use(morgan("tiny"));
```

Output:

```
GET /api/notes 200 -
```

✔ Minimal logs
✔ Low noise

---

📌 Morgan logs **only HTTP requests**, not application errors.

---

# 2️⃣ Setting Up Winston (Application Logging)

## Install Winston

```bash
npm install winston
```

## Create Winston Logger

📁 `utils/logger.js`

```js
const winston = require("winston");

const logger = winston.createLogger({
  level: "info",
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.json()
  ),
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({
      filename: "error.log",
      level: "error"
    })
  ]
});

module.exports = logger;
```

---

## Use Winston in Routes

```js
const logger = require("./utils/logger");

app.get("/success", (req, res) => {
  logger.info("Success route called");
  res.send("OK");
});
```

---

# 3️⃣ Setting Up Pino (Fast Production Logging)

## Install Pino

```bash
npm install pino pino-http
```

## Create Pino Logger

📁 `utils/pinoLogger.js`

```js
const pino = require("pino");
const logger = pino({ level: "info" });
module.exports = logger;
```

---

## Attach Pino to Express

```js
const pinoHttp = require("pino-http");
const logger = require("./utils/pinoLogger");

app.use(pinoHttp({ logger }));
```

📌 Pino automatically logs all requests
📌 `req.log` is available inside routes

---

## Pino Route Logging

```js
app.get("/success", (req, res) => {
  req.log.info("Route called");
  res.send("OK");
});
```

---

# 4️⃣ Error Handling and Logging (IMPORTANT)

## ❌ Wrong Way (No Logging)

```js
catch (err) {
  res.status(500).send("Error");
}
```

---

## ✅ Correct Way (With Logging)

### Central Error Middleware (Winston)

```js
app.use((err, req, res, next) => {
  logger.error({
    message: err.message,
    stack: err.stack,
    url: req.originalUrl
  });

  res.status(500).json({ message: "Internal Server Error" });
});
```

---

### Central Error Middleware (Pino)

```js
app.use((err, req, res, next) => {
  req.log.error(err);
  res.status(500).json({ message: "Something went wrong" });
});
```

✔ Error stored
✔ Stack trace logged
✔ Safe response to client

---

## 🔁 Logging Flow Summary

```
Client Request
   ↓
Morgan / Pino logs request
   ↓
Controller logic
   ↓
Winston / Pino logs app events
   ↓
Error middleware logs failures
```

---

## 🧠 Memory Rule

* **Morgan** → HTTP requests
* **Winston** → readable logs + files
* **Pino** → fast JSON logs for production

---

## ✅ Conclusion

Logging is essential for backend reliability.
A well-logged backend is easier to debug, monitor, and scale.

This README is suitable for:

* College projects
* Backend documentation
* Real-world Express.js applications

---
