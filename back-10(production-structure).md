

# 1️⃣ Basic Structure of an Application (VERY DEEP)

### ❓ Why structure even matters

If your app has:

* 5 routes → any structure works
* 50 routes → bad structure becomes pain
* 500 routes → bad structure becomes **impossible**

**Structure = ability to scale without rewriting**

---

## ✅ Real-world production structure

```
project-root/
│
├── src/
│   ├── app.js
│   ├── server.js
│
│   ├── routes/
│   │   └── user.routes.js
│
│   ├── controllers/
│   │   └── user.controller.js
│
│   ├── services/
│   │   └── user.service.js
│
│   ├── models/
│   │   └── user.model.js
│
│   ├── middlewares/
│   │   ├── auth.middleware.js
│   │   └── error.middleware.js
│
│   └── utils/
│       ├── asyncHandler.js
│       └── response.js
│
├── config/
│   ├── db.js
│   ├── env.js
│   └── cors.js
│
├── .env
├── .gitignore
├── package.json
```

---

## `server.js` vs `app.js` (IMPORTANT CONCEPT)

### ❌ Common beginner mistake

```js
const app = express();
app.listen(3000);
```

### ✅ Correct separation

#### `app.js` → **configure app**

```js
import express from 'express';
import userRoutes from './routes/user.routes.js';
import errorHandler from './middlewares/error.middleware.js';

const app = express();

app.use(express.json());
app.use('/api/users', userRoutes);
app.use(errorHandler);

export default app;
```

#### `server.js` → **start server**

```js
import app from './app.js';
import { connectDB } from '../config/db.js';

connectDB();

app.listen(process.env.PORT, () => {
  console.log('Server running');
});
```

📌 Why this matters:

* testing without server
* PM2 clustering
* serverless compatibility

---

# 2️⃣ File Naming Conventions (WHY THIS EXACT FORMAT)

### ❌ Inconsistent naming breaks teamwork

```
UserRoutes.js
userController.js
USER_service.js
```

### ✅ Industry standard

```
user.routes.js
user.controller.js
user.service.js
user.model.js
```

📌 Rule:

> filename describes **what it is**, not **what it does**

---

# 3️⃣ Deep Folder Responsibilities (WITH FLOW)

Let’s trace **ONE API CALL**

### Example:

```
POST /api/users/login
```

---

## 1️⃣ `routes/user.routes.js` → URL only

```js
import express from 'express';
import { loginUser } from '../controllers/user.controller.js';

const router = express.Router();

router.post('/login', loginUser);

export default router;
```

🚫 NO logic
🚫 NO DB
🚫 NO try-catch

---

## 2️⃣ `controllers/user.controller.js` → Request/Response

```js
import { loginService } from '../services/user.service.js';

export const loginUser = async (req, res) => {
  const result = await loginService(req.body);

  res.status(200).json(result);
};
```

📌 Controller responsibility:

* read `req`
* call service
* send `res`

---

## 3️⃣ `services/user.service.js` → Business Logic

```js
import User from '../models/user.model.js';

export const loginService = async ({ email, password }) => {
  const user = await User.findOne({ email });
  if (!user) throw new Error('User not found');

  return { message: 'Login successful' };
};
```

📌 Services:

* validation
* calculations
* rules
* permissions

---

## 4️⃣ `models/user.model.js` → Database Shape

```js
import mongoose from 'mongoose';

const userSchema = new mongoose.Schema({
  email: String,
  password: String
});

export default mongoose.model('User', userSchema);
```

📌 Models **never** talk to Express

---

# 4️⃣ `config/` Folder (WHY IT EXISTS)

### ❌ Bad

```js
mongoose.connect(process.env.MONGO_URI);
```

(scattered everywhere)

### ✅ Good

```
config/db.js
```

```js
import mongoose from 'mongoose';

export const connectDB = async () => {
  await mongoose.connect(process.env.MONGO_URI);
};
```

📌 Config = setup logic only
📌 No routes, no business logic

---

# 5️⃣ `package.json` (NOT JUST DEPENDENCIES)

```json
{
  "type": "module",
  "scripts": {
    "dev": "nodemon src/server.js",
    "start": "node src/server.js",
    "lint": "eslint ."
  }
}
```

📌 Scripts define:

* how devs run project
* how CI/CD runs project
* how PM2 runs project

---

# 6️⃣ `.env` (SECURITY CONCEPT)

### ❌ NEVER do this

```js
const JWT_SECRET = "123456";
```

### ✅ Correct

```
JWT_SECRET=super-secret
```

```js
process.env.JWT_SECRET
```

📌 `.env` values:

* change per environment
* never committed
* injected in production

---

# 7️⃣ Production Environment (REAL WORLD)

---

## PM2 (WHY NOT node server.js)

### ❌ Node dies → app dies

### ✅ PM2 restarts automatically

```bash
pm2 start src/server.js --name api
pm2 logs
pm2 restart api
```

📌 PM2 handles:

* crashes
* memory leaks
* load balancing

---

## Central Error Handling (CRITICAL)

### ❌ This is bad

```js
try {
  ...
} catch(e) {
  res.send(e.message)
}
```

### ✅ Proper error middleware

```js
export default (err, req, res, next) => {
  res.status(500).json({
    success: false,
    message: err.message
  });
};
```

---

# 8️⃣ `asyncHandler.js` (WHY IT EXISTS)

### ❌ Without it

```js
try {
  await User.find()
} catch (e) {
  next(e)
}
```

### ✅ With asyncHandler

```js
export const asyncHandler = fn =>
  (req, res, next) =>
    Promise.resolve(fn(req, res, next)).catch(next);
```

📌 Used in **almost every professional backend**

---

# 9️⃣ CORS 
### Problem:

Frontend & backend on different origins

### Solution:

```js
app.use(cors({
  origin: 'http://localhost:3000',
  credentials: true
}));
```

📌 Without CORS:

* browser blocks request
* API still works (Postman)

---

# 🔟 ESLint & Prettier (WHY BOTH)

| Tool     | Purpose      |
| -------- | ------------ |
| ESLint   | catches bugs |
| Prettier | formats code |

### Example ESLint error:

```js
let a = 10;
```

❌ unused variable

### Prettier:

```js
{ name: "A", age: 10 }
```

→ formatted automatically

---

# 1️⃣1️⃣ Postman (REAL USAGE)

### What devs test:

* headers
* tokens
* cookies
* error cases

Example:

```
Authorization: Bearer <token>
```

📌 Postman ≠ just sending request
📌 It replaces frontend during backend development

---
