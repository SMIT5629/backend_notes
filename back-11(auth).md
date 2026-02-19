# **1. Authentication vs Authorization**
## 1. Authentication (AuthN)

Authentication answers:

> "Who are you?"

It verifies identity using:

* Username & Password
* OTP
* OAuth (Google, GitHub)
* JWT tokens
* Biometric

### Example

User logs in with email and password.
Server checks credentials.
If valid → user is authenticated.

```js
app.post('/login', async (req, res) => {
  const user = await User.findOne({ email: req.body.email });
  if (!user) return res.status(401).send('Invalid credentials');
});
```

---

## 2. Authorization (AuthZ)

Authorization answers:

> "What are you allowed to do?"

After authentication, system checks permissions.

### Example

* Admin → Can delete users
* User → Cannot delete users

```js
if (req.user.role !== 'admin') {
  return res.status(403).send('Forbidden');
}
```

---

## Key Difference

| Authentication    | Authorization           |
| ----------------- | ----------------------- |
| Verifies identity | Verifies permission     |
| Happens first     | Happens after login     |
| Example: Login    | Example: Access control |

---

## Real Flow

Login → Authentication → Authorization → Resource Access

---

## Summary

Authentication = Identity
Authorization = Permission

---
# **2. Working with Passwords and Authentication - Cookie Authentication , OAuth** 
Authentication 
## 1️⃣ Password-Based Authentication (Traditional Login)

Let’s think first:

If a user registers with:

* Email: `abc@gmail.com`
* Password: `123456`

❓ Should we store `"123456"` directly in database?

Why not?

👉 Tell me one risk of storing plain passwords.

---

### ✅ Correct Way: Hash Passwords

We use **bcrypt**.

### Install:

```bash
npm install bcrypt
```

---

### 🔹 During Registration

```js
const bcrypt = require("bcrypt");

app.post("/register", async (req, res) => {
  const { email, password } = req.body;

  const hashedPassword = await bcrypt.hash(password, 10);

  await User.create({
    email,
    password: hashedPassword
  });

  res.send("User Registered");
});
```

👉 What does `10` mean here? (Hint: security cost factor)

---

### 🔹 During Login

```js
app.post("/login", async (req, res) => {
  const { email, password } = req.body;

  const user = await User.findOne({ email });
  if (!user) return res.send("User not found");

  const isMatch = await bcrypt.compare(password, user.password);

  if (!isMatch) return res.send("Wrong password");

  res.send("Login successful");
});
```

💡 Flow:

1. User sends password
2. We compare with hashed one
3. If match → login success

---

## 2️⃣ Cookie-Based Authentication (Session Login)

Now question:

After login…
How does the server remember the user on the next request?

That’s where sessions + cookies come in.

We use:

* **express-session**

---

### Install

```bash
npm install express-session
```

---

### Setup

```js
const session = require("express-session");

app.use(session({
  secret: "mysecret",
  resave: false,
  saveUninitialized: false,
}));
```

---

### Modify Login

```js
app.post("/login", async (req, res) => {
  const user = await User.findOne({ email: req.body.email });

  const isMatch = await bcrypt.compare(req.body.password, user.password);

  if (!isMatch) return res.send("Wrong password");

  req.session.userId = user._id;

  res.send("Logged in with session");
});
```

---

### Protected Route

```js
app.get("/dashboard", (req, res) => {
  if (!req.session.userId) {
    return res.send("Please login");
  }

  res.send("Welcome to dashboard");
});
```

---

💡 What happens internally?

1. Server creates session
2. Server sends session ID in cookie
3. Browser stores it
4. Browser automatically sends cookie on every request

---

## 3️⃣ OAuth Authentication (Google Login)

Now let’s think:

Why do companies use "Login with Google"?

👉 So they don't handle passwords.

We use:

* **passport**
* **passport-google-oauth20**

---

### Install

```bash
npm install passport passport-google-oauth20
```

---

### Basic Setup

```js
const passport = require("passport");
const GoogleStrategy = require("passport-google-oauth20").Strategy;

passport.use(new GoogleStrategy({
    clientID: "GOOGLE_CLIENT_ID",
    clientSecret: "GOOGLE_SECRET",
    callbackURL: "/auth/google/callback"
  },
  async (accessToken, refreshToken, profile, done) => {

    let user = await User.findOne({ googleId: profile.id });

    if (!user) {
      user = await User.create({
        googleId: profile.id,
        name: profile.displayName
      });
    }

    return done(null, user);
  }
));
```

---

### Routes

```js
app.get("/auth/google",
  passport.authenticate("google", { scope: ["profile", "email"] })
);

app.get("/auth/google/callback",
  passport.authenticate("google", { failureRedirect: "/" }),
  (req, res) => {
    res.redirect("/dashboard");
  }
);
```

---

💡 OAuth Flow:

1. User clicks login
2. Redirect to Google
3. User approves
4. Google sends profile data
5. You create/find user
6. User logged in

---

## 🔥 Now Let’s Compare Deeply

| Feature            | Password     | Session (Cookie) | OAuth     |
| ------------------ | ------------ | ---------------- | --------- |
| Stores Password?   | Yes (hashed) | Yes (hashed)     | No        |
| Uses Cookie?       | Optional     | Yes              | Yes       |
| External Provider? | No           | No               | Yes       |
| More Secure?       | Medium       | Medium           | High      |
| Easy for Users?    | Normal       | Normal           | Very Easy |

---

## 🧠 Big Picture (Important)

Think like this:

* **Password** → How user proves identity
* **Session/Cookie** → How server remembers user
* **OAuth** → Someone else proves identity for you

---

# **3. Session vs Token Authentication**

## 1. Session-Based Authentication

Flow:

1. User logs in
2. Server creates session
3. Session ID stored in cookie
4. Server stores session data

### Pros

* Easy logout
* Server control

### Cons

* Requires session storage
* Harder to scale

---

## 2. Token-Based Authentication (JWT)

Flow:

1. User logs in
2. Server generates JWT
3. Client stores token
4. Client sends token in Authorization header

```js
Authorization: Bearer <token>
```

### Pros

* Stateless
* Scalable
* Good for APIs

### Cons

* Hard to revoke
* Token theft risk

---

## Comparison

| Feature  | Session | JWT     |
| -------- | ------- | ------- |
| Storage  | Server  | Client  |
| Scalable | Limited | High    |
| Logout   | Easy    | Complex |

---

## Summary

Use Session → Server-rendered apps
Use JWT → MERN / APIs / Mobile apps
# **4. Implementing JWT Authentication**

## Install

```
npm install jsonwebtoken
```

---

## 1. Generate Token

```js
const jwt = require('jsonwebtoken');

app.post('/login', (req, res) => {
  const user = { id: 1, role: 'user' };

  const token = jwt.sign(user, process.env.JWT_SECRET, {
    expiresIn: '15m'
  });

  res.json({ token });
});
```

---

## 2. Verify Token Middleware

```js
function authenticate(req, res, next) {
  const authHeader = req.headers.authorization;
  if (!authHeader) return res.sendStatus(401);

  const token = authHeader.split(' ')[1];

  jwt.verify(token, process.env.JWT_SECRET, (err, user) => {
    if (err) return res.sendStatus(403);
    req.user = user;
    next();
  });
}
```

---

## 3. Protect Route

```js
app.get('/dashboard', authenticate, (req, res) => {
  res.send('Protected content');
});
```

---

## Security Notes

* Keep JWT_SECRET private
* Use short expiration
* Store token in httpOnly cookie for security

---

## Summary

JWT = Stateless authentication
Token contains user data
Server verifies signature each request

# **5. Securing Passwords with bcrypt (Hashing & Salt)**

## Why Hash Passwords?

Never store plain passwords.
If database leaks → users compromised.

---

## Install

```
npm install bcrypt
```

---

## 1. Hash Password

```js
const bcrypt = require('bcrypt');

const saltRounds = 10;

const hashedPassword = await bcrypt.hash(password, saltRounds);
```

---

## 2. Compare Password

```js
const isMatch = await bcrypt.compare(inputPassword, user.password);
```

---

## What is Salt?

Salt is random data added before hashing.
It prevents:

* Rainbow table attacks
* Duplicate hash detection

---

## Flow

Register → hash password → store hash
Login → compare hash → authenticate

---

## Summary

bcrypt protects passwords by:

* Hashing
* Salting
* Making brute force harder

# **6 .Role-Based Access Control (RBAC)**

## What is RBAC?

RBAC restricts access based on user roles.

Example roles:

* admin
* user
* moderator

---

## Example User Schema

```js
const userSchema = new mongoose.Schema({
  email: String,
  password: String,
  role: {
    type: String,
    enum: ['admin', 'user'],
    default: 'user'
  }
});
```

---

## Role Middleware

```js
function authorize(role) {
  return (req, res, next) => {
    if (req.user.role !== role) {
      return res.status(403).send('Forbidden');
    }
    next();
  };
}
```

---

## Protect Route

```js
app.delete('/users', authenticate, authorize('admin'), (req, res) => {
  res.send('User deleted');
});
```

---

## Summary

Authentication → Who you are
Authorization (RBAC) → What you can do
Good 👍 this is important for backend interviews.

---
# **7. Passport.js**


## 1️⃣ What Problem Does Passport Solve?

Let’s think first.

Without Passport:

* You write login logic
* You write session logic
* You manage user persistence
* You handle OAuth manually

With Passport:

* It standardizes authentication
* It separates **strategy** from **session**

👉 Question for you:
Is Passport handling database storage automatically?
(Yes or No?)

---

## 2️⃣ How Passport Actually Works (Mental Model)

Passport has 3 main parts:

1. **Strategy** → How user proves identity
2. **Serialize** → What to store in session
3. **Deserialize** → How to get user back from session

Think of it like:

Login → Strategy verifies → serializeUser → session stores ID
Next request → deserializeUser → full user attached to `req.user`

---

## 3️⃣ Local Strategy (Username + Password)

Uses:

* **passport-local**

You wrote:

```js
passport.use(new LocalStrategy(async (username, password, done) => {
  const user = await User.findOne({ username });
  if (!user) return done(null, false);
  done(null, user);
}));
```

But something is missing here 👀

Where is password verification?

👉 What should we use to compare passwords?

(Hint: hashing library)

---

Correct version should include:

```js
const bcrypt = require("bcrypt");

passport.use(new LocalStrategy(async (username, password, done) => {
  const user = await User.findOne({ username });
  if (!user) return done(null, false);

  const isMatch = await bcrypt.compare(password, user.password);
  if (!isMatch) return done(null, false);

  return done(null, user);
}));
```

---

## 4️⃣ Serialize & Deserialize (Very Important 🔥)

Most students get confused here.

### serializeUser

```js
passport.serializeUser((user, done) => {
  done(null, user.id);
});
```

👉 What are we storing in session here?
Full user object or just ID?

Why do we store only ID?

Think about performance.

---

### deserializeUser

```js
passport.deserializeUser(async (id, done) => {
  const user = await User.findById(id);
  done(null, user);
});
```

This runs on **every request** after login.

It attaches user to:

```js
req.user
```

---

## 5️⃣ Google OAuth Strategy

Uses:

* **passport-google-oauth20**

```js
passport.use(new GoogleStrategy({
  clientID: process.env.GOOGLE_ID,
  clientSecret: process.env.GOOGLE_SECRET,
  callbackURL: '/auth/google/callback'
}, async (accessToken, refreshToken, profile, done) => {

  let user = await User.findOne({ googleId: profile.id });

  if (!user) {
    user = await User.create({
      googleId: profile.id,
      name: profile.displayName
    });
  }

  done(null, user);
}));
```

👉 Important question:

In Google OAuth,
Do we ever receive the user’s password?

(Think carefully.)

---

## 6️⃣ Protecting Routes

```js
function isAuthenticated(req, res, next) {
  if (req.isAuthenticated()) return next();
  res.status(401).send('Unauthorized');
}
```

Where does `req.isAuthenticated()` come from?

👉 From Passport session middleware.

---

## 7️⃣ Full Flow (Big Picture)

Let’s connect everything:

### Login Flow

1. User submits login
2. `passport.authenticate("local")`
3. Strategy verifies
4. serializeUser stores ID in session
5. Session ID sent in cookie

### Next Request

1. Cookie sent automatically
2. Session read
3. deserializeUser runs
4. `req.user` available

---

