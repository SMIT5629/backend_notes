# ***Template Engine – EJS (Embedded Javascript)***

---

# 1. What is a Template Engine?

A **Template Engine** is a tool that helps us generate **dynamic HTML pages** on the **server side** by combining:

* Static HTML structure
* Dynamic data from backend (Node.js / Express)

Instead of sending only plain text or static HTML, we can inject data (like username, product list, login status) directly into HTML files.

### Why Template Engine is Needed (Problem It Solves)

Without a template engine:

* We must write HTML inside JavaScript strings
* Code becomes messy and hard to maintain
* Reusing layouts is difficult

#### ❌ Without Template Engine

```js
app.get("/", (req, res) => {
    const name = "Smit";
    res.send(`<html><body><h1>Hello ${name}</h1></body></html>`);
});
```

Problems:

* Hard to read
* Not reusable
* Mixing logic and UI

#### ✅ With Template Engine

```js
app.get("/", (req, res) => {
    res.render("home", { name: "Smit" });
});
```

```html
<h1>Hello <%= name %></h1>
```

### Key Benefits

* Clean separation of logic and UI
* Dynamic HTML generation
* Reusable templates
* Better scalability

---

# 2. Template Engine Options

There are multiple template engines available for Express:

### 1. EJS (Embedded JavaScript)

* Uses normal HTML
* JavaScript inside HTML
* Easy to learn

```html
<h1>Hello <%= name %></h1>
```

### 2. Handlebars

* Uses mustache syntax
* Limited logic

```html
<h1>Hello {{name}}</h1>
```

### 3. Pug / Jade

* Indentation-based syntax
* Not HTML

```pug
h1 Hello #{name}
```

### Why We Choose EJS

* Beginner friendly
* No new syntax
* Powerful JavaScript support
* Widely used with Express

---

# 3. Setting Up EJS Template Engine

### Step 1: Install EJS

```bash
npm install ejs
```

### Step 2: Configure Express to Use EJS

```js
const express = require("express");
const app = express();

app.set("view engine", "ejs");
```

📌 Express automatically handles EJS internally. We do **NOT** use `require('ejs')` manually.

### Default Behavior

* Express looks for templates inside a folder named **views**

---

# 4. Rendering First Page Using EJS

### Folder Structure

```
project
├── views
│   └── home.ejs
└── app.js
```

### app.js

```js
app.get("/", (req, res) => {
    res.render("home");
});
```

### home.ejs

```html
<!DOCTYPE html>
<html>
<head>
    <title>Home</title>
</head>
<body>
    <h1>Hello from EJS</h1>
</body>
</html>
```

### Difference Between res.send() and res.render()

| res.send()               | res.render()     |
| ------------------------ | ---------------- |
| Sends plain text or HTML | Renders template |
| No template processing   | Processes EJS    |

---

# 5. EJS Syntax (Very Important)

### 5.1 `<%= %>` → Output (Escaped / Safe)

Used to display variables safely. HTML tags are escaped.

```js
res.render("home", { msg: "<h1>Hello</h1>" });
```

```html
<%= msg %>
```

Output:

```
<h1>Hello</h1>
```

✔ Prevents XSS attacks
✔ Use for user input

---

### 5.2 `<% %>` → Logic Only

Used for:

* if / else
* loops
* variable declaration

```html
<% let age = 20; %>
<p>Age is declared</p>
```

---

### 5.3 `<%- %>` → Raw Output (Unescaped)

Used to render HTML directly.

```js
res.render("home", { content: "<b>Bold Text</b>" });
```

```html
<%- content %>
```

⚠️ Dangerous if used with user input (XSS risk)

---

# 6. Loop Statements in EJS

Used to render lists dynamically.

### app.js

```js
app.get("/users", (req, res) => {
    const users = ["Smit", "Aman", "Ravi"];
    res.render("users", { users });
});
```

### users.ejs

```html
<ul>
    <% users.forEach(user => { %>
        <li><%= user %></li>
    <% }) %>
</ul>
```

---

# 7. Conditional Statements in EJS

Used to control rendering based on conditions.

### app.js

```js
res.render("login", { isLoggedIn: true });
```

### login.ejs

```html
<% if (isLoggedIn) { %>
    <h1>Welcome Back</h1>
<% } else { %>
    <h1>Please Login</h1>
<% } %>
```

---

# 8. Locals in EJS

Data passed from Express to EJS templates are called **locals**.

```js
res.render("profile", { name: "Smit", age: 20 });
```

```html
<p>Name: <%= name %></p>
<p>Age: <%= age %></p>
```

---

# 9. Accessing Static Files Inside EJS

Static files include:

* CSS
* JavaScript
* Images

### Folder Structure

```
project
├── public
│   ├── css/style.css
│   ├── js/app.js
│   └── images/logo.png
├── views/home.ejs
└── app.js
```

### Configure Static Middleware

```js
app.use(express.static("public"));
```

### Using Static Files in EJS

```html
<link rel="stylesheet" href="/css/style.css">
<script src="/js/app.js"></script>
<img src="/images/logo.png" />
```

📌 `public` folder name is not used in URL

---

# 10. Important Exam Points

* EJS runs on **server side**
* Browser never understands EJS syntax
* EJS generates final HTML before sending
* `<%= %>` is safe
* `<%- %>` is unsafe
* `res.render()` renders templates

---

## Final Conclusion

EJS is a powerful yet simple server-side template engine that allows Express applications to generate dynamic HTML pages efficiently while keeping code clean and organized.

---
