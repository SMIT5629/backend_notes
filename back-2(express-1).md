

# ***Express.js***

# **1. What is Express.js and Why Use It**

**Definition:**

* Express.js is a **fast, minimal, and flexible Node.js web framework**.
* It helps to **build web applications and APIs** easily on top of Node.js.

**Why Use Express.js:**

* Simplifies **server-side code** compared to raw Node.js HTTP module.
* Handles **routing**, **middleware**, and **HTTP requests** easily.
* Supports **templating engines** for dynamic HTML.
* Used for building **RESTful APIs**.
* Huge **community support** and widely adopted.

**Example:**
Without Express:

```js
const http = require('http');

const server = http.createServer((req, res) => {
    res.writeHead(200, {'Content-Type': 'text/plain'});
    res.end('Hello World!');
});

server.listen(3000, () => console.log('Server running on port 3000'));
```

With Express:

```js
const express = require('express');
const app = express();

app.get('/', (req, res) => {
    res.send('Hello World!');
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

✅ Much simpler and cleaner.

---

# **2. Setting Up Express Server**

**Steps:**

1. Initialize Node project:

```bash
npm init -y
```

2. Install Express:

```bash
npm install express
```

3. Create `index.js` and set up a server:

```js
const express = require('express');
const app = express();

// Define a route
app.get('/', (req, res) => {
    res.send('Welcome to Express Server');
});

// Start server
app.listen(3000, () => {
    console.log('Server is running on http://localhost:3000');
});
```

---

# **3. Returning Response from the Server**

Express provides multiple methods to send responses:

1. `res.send()` – Sends string, buffer, JSON, HTML:

```js
app.get('/text', (req, res) => {
    res.send('This is a text response');
});
```

2. `res.json()` – Sends JSON:

```js
app.get('/json', (req, res) => {
    res.json({ name: 'Smit', age: 20 });
});
```

3. `res.sendFile()` – Sends a file:

```js
app.get('/file', (req, res) => {
    res.sendFile(__dirname + '/sample.html');
});
```

4. `res.status()` – Sets HTTP status:

```js
app.get('/error', (req, res) => {
    res.status(404).send('Page Not Found');
});
```

---

# **4. Using Query Parameters and URL Parameters**


## **🌐 1). What Are URL Parameters?**

URL parameters are **part of the actual route path**.
They are used when you want to identify a **specific resource**.

### 📌 General Form

```
/resource/:parameterName
```

### 📌 Example URL

```
/users/15
```

Here, `15` is the value of the parameter `id`.

### 📌 Express Example

```js
app.get('/users/:id', (req, res) => {
  const userId = req.params.id;  
  res.send(`User ID is: ${userId}`);
});
```

### 👉 Output for `/users/15`

```
User ID is: 15
```

### **When to use URL params**

Use them when the value is:

* Required
* A specific resource identifier
  Examples:
* `/products/101`
* `/posts/9/comments`
* `/orders/823`

---

## **🔍 2). What Are Query Parameters?**

Query parameters come **after the `?` in the URL**.
They are optional and are used for **searching, filtering, sorting**, etc.

### 📌 General Form

```
/route?key=value&key2=value2
```

### 📌 Example URL

```
/search?keyword=iphone&sort=asc
```

### 📌 Express Example

```js
app.get('/search', (req, res) => {
  const keyword = req.query.keyword;
  const sort = req.query.sort;
  res.send(`Searching for: ${keyword}, sorted by: ${sort}`);
});
```

### 👉 Output for:

```
/search?keyword=laptop&sort=desc
```

```
Searching for: laptop, sorted by: desc
```

### **When to use Query params**

Use them when the value is:

* Optional
* Used for filters or settings
  Examples:
* `/products?category=shoes&price=low`
* `/search?q=javascript`
* `/blogs?page=2&limit=10`

---

## **🆚 3). URL Params vs Query Params (Quick Comparison)**

| Feature           | URL Params             | Query Params                  |
| ----------------- | ---------------------- | ----------------------------- |
| Format            | `/users/:id`           | `/users?id=10`                |
| Required?         | Yes                    | Optional                      |
| Used for          | Identifying a resource | Filtering, searching, sorting |
| Access in Express | `req.params`           | `req.query`                   |
| Example           | `/product/42`          | `/product?color=red&size=M`   |

---

## **🎯 4). Combined Example (Both Together)**

### URL:

```
/products/100?color=red&size=L
```

### Express:

```js
app.get('/products/:id', (req, res) => {
  const id = req.params.id;
  const color = req.query.color;
  const size = req.query.size;

  res.send(`Product ${id}, Color: ${color}, Size: ${size}`);
});
```

---



# **5. HTTP Requests in Express**

HTTP requests have **methods** that define action:

| Method | Use Case             |
| ------ | -------------------- |
| GET    | Retrieve data        |
| POST   | Send/create data     |
| PUT    | Update complete data |
| PATCH  | Update partial data  |
| DELETE | Delete data          |

**Example Routes:**

```js
// GET
app.get('/get-data', (req, res) => res.send('GET request'));

// POST
app.post('/add-data', (req, res) => res.send('POST request'));

// PUT
app.put('/update-data', (req, res) => res.send('PUT request'));

// PATCH
app.patch('/update-partial', (req, res) => res.send('PATCH request'));

// DELETE
app.delete('/delete-data', (req, res) => res.send('DELETE request'));
```

---

# **6. Serving Static Files with express.static()**

**Purpose:** Serve HTML, CSS, JS, images easily.

1. Create a folder `public` and put your files (like `index.html`, `style.css`).
2. Use middleware `express.static`:

```js
app.use(express.static('public'));
```

3. Now files are accessible directly via URL:
   `http://localhost:3000/index.html`

**Example Folder Structure:**

```
project/
├── index.js
└── public/
    ├── index.html
    └── style.css
```

---

### ✅ **Conclusion / Key Points**

* Express simplifies Node.js server creation.
* Routes handle different URLs and HTTP methods.
* Query and URL parameters help pass data in requests.
* `res` object helps send responses.
* `express.static()` serves static files easily.

---

