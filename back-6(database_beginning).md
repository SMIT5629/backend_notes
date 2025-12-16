## Topics covered :

1. Relational vs Non-Relational (MySQL vs MongoDB)
2. What is MongoDB & Why MongoDB?
3. MongoDB Compass + Terminal (mongosh)
4. MongoDB Local & Cloud (Atlas)
5. Collections, Documents & Datatypes
6. Connecting MongoDB with Node.js using Mongoose
7. Database Relationships (all types)
8. Handling relationships using `populate()`

---

# **1️⃣ Relational vs Non-Relational Databases**
---

### 🔸 Relational Database (MySQL)

Think like **Excel tables** with strict rules.

📌 **Structure**

* Tables
* Rows
* Columns
* Fixed schema

📌 **Example: MySQL**

```
Users Table
--------------------------------
id | name   | email
1  | Smit   | smit@gmail.com
2  | Rahul  | rahul@gmail.com
```

📌 **Rules**

* Schema is fixed
* Relationships using **Foreign Keys**
* Best for **banking, transactions**

---

### 🔸 Non-Relational Database (MongoDB)

Think like **JSON objects** stored inside collections.

📌 **Structure**

* Database
* Collections
* Documents (JSON-like)

📌 **Example: MongoDB**

```json
{
  "_id": "1",
  "name": "Smit",
  "email": "smit@gmail.com",
  "skills": ["JS", "Node", "MongoDB"]
}
```

📌 **Rules**

* Schema is flexible
* Easy to scale
* Best for **web apps, real-time apps**

---

### 🔥 MySQL vs MongoDB (Quick Table)

| Feature     | MySQL          | MongoDB          |
| ----------- | -------------- | ---------------- |
| Type        | Relational     | Non-Relational   |
| Schema      | Fixed          | Flexible         |
| Data Format | Rows & Columns | JSON (Documents) |
| Scaling     | Vertical       | Horizontal       |
| Used in     | Banking        | Web Apps, APIs   |

---


# **2️⃣ What is MongoDB? Why Use It?**
---

### 🔸 What is MongoDB?

MongoDB is a **NoSQL (Non-Relational) database** that stores data in **documents**, not tables.

📌 Each document looks like a JavaScript object:

```json
{
  name: "Smit",
  age: 21,
  skills: ["Node", "MongoDB"]
}
```

📌 These documents are stored inside a **collection**
📌 Collections are stored inside a **database**

---

### 🔸 MongoDB Structure (Very Important)

```
Database
 └── Collection
      └── Document
```

Example:

```
collegeDB
 └── students
      └── { name: "Smit", branch: "CSE" }
```

---

### 🔸 Why Use MongoDB? (Real Reasons)

✅ **Schema Flexible**

* You can add new fields anytime

```json
{ name: "Rahul" }
{ name: "Amit", age: 22 }
```

✅ **Fast Development**

* No need to design tables first

✅ **Scales Easily**

* Used by Netflix, Uber, Airbnb

✅ **Perfect for APIs**

* Works smoothly with Express & Node

---

### 🔥 Real-World Example

Instagram post data:

```json
{
  user: "smit",
  caption: "Learning MongoDB",
  likes: 120,
  comments: [
    { user: "rahul", text: "Nice!" }
  ]
}
```

👉 Very hard in MySQL
👉 Very easy in MongoDB

---

### 🧠 One-Line Memory Trick

> **MongoDB = JSON + Flexibility + Speed**

---
### ✅ Why MongoDB is better suited for Node.js than MySQL ?

MongoDB is better for Node.js **because both work with JSON-like data**.

* **Node.js** sends & receives data in **JSON**
* **MongoDB** stores data as **JSON documents (BSON)**
* So → **no conversion headache**, faster development, cleaner code

👉 In MySQL, you convert **tables → objects → JSON**
👉 In MongoDB, it’s **JSON → JSON** (simple & fast)

---
Perfect 👍
We’ll go **hands-on now**, very light theory.

# **3️⃣MongoDB Compass + Terminal (mongosh)**
---

I’ll assume **Windows** (tell me later if different).

---

## **1. MongoDB Compass (GUI)**

### What is MongoDB Compass?

👉 **Official GUI tool** from MongoDB
👉 Used to **visually see databases, collections & documents**

Think of it like:

> **phpMyAdmin for MongoDB**

---

### How Compass works (concept)

* MongoDB runs in background (server)
* Compass connects to it using a **connection string**
* Shows data in **JSON-like view**

---

### Default Connection String (Local)

```
mongodb://localhost:27017
```

* `localhost` → your computer
* `27017` → MongoDB default port

---

### What you can do in Compass

✅ Create database
✅ Create collection
✅ Insert documents
✅ View & edit data
✅ Run queries (filter, sort)

---

## **2. MongoDB Terminal (mongosh)**

### What is mongosh?

👉 **MongoDB Shell**
👉 Used to interact with MongoDB using commands

Think of it like:

> **MySQL terminal for MongoDB**

---

### How to open mongosh

1. Open **Command Prompt**
2. Type:

```bash
mongosh
```

If MongoDB is running, you’ll see:

```
test>
```

---

##  **3. Basic mongosh Commands (Very Important)**

### Show databases

```js
show dbs
```

### Use / create database

```js
use collegeDB
```

👉 If DB doesn’t exist → MongoDB creates it automatically

---

### Create collection + insert data

```js
db.students.insertOne({
  name: "Smit",
  branch: "CSE",
  age: 21
})
```

---

### View data

```js
db.students.find()
```

---

## 🔥 What just happened?

* `collegeDB` → database
* `students` → collection
* `{}` → document (JSON-like)

---
Nice 👍 let’s continue — still **simple + practical**.

# **4️⃣ MongoDB Local vs MongoDB Cloud (Atlas)**

---

## **1. MongoDB **Local** (On Your Computer)**

### What does “local” mean?

👉 MongoDB runs **on your own system**
👉 Data stored on **your hard disk**

### When to use local MongoDB?

* Learning
* Practice
* College projects
* Development

### How it works

```
Node.js App
   ↓
MongoDB (localhost)
```

### Connection string (local)

```
mongodb://localhost:27017/collegeDB
```

---

## **2. MongoDB **Cloud** (MongoDB Atlas)**

### What is MongoDB Atlas?

👉 **Official cloud service** by MongoDB
👉 Database runs on **internet servers**

### When to use Atlas?

* Production apps
* Sharing project with others
* Hosting APIs

### How it works

```
Node.js App
   ↓ (internet)
MongoDB Atlas (cloud)
```

---

### Atlas Connection String (example)

```
mongodb+srv://username:password@cluster0.mongodb.net/collegeDB
```

---

## 🔥 Local vs Cloud (Simple Table)

| Feature         | Local MongoDB | Atlas (Cloud)     |
| --------------- | ------------- | ----------------- |
| Internet needed | ❌             | ✅                 |
| Speed           | Very fast     | Network dependent |
| Use case        | Learning      | Production        |
| Backup          | Manual        | Automatic         |

---

### ⚠️ Important Rule (Real-life)

❌ Never use local MongoDB for live users
✅ Use Atlas for hosted apps

---
Got it 👍
Let’s explain **exactly as your prompt says** — **Datatypes, Collections, and Documents**, **step-by-step**, **well-structured**, **no extra theory**, with **clear examples**.

---

# **5️⃣Understanding Datatypes, Collections & Documents** (MongoDB)

## **1️. Document (First & Most Important)**

### What is a Document?

A **document** is **one single record** stored in MongoDB.
It is written in **JSON-like format**.

### Example (Student Document)

```js
{
  _id: ObjectId("64abc..."),
  name: "Smit",
  age: 21,
  branch: "CSE"
}
```

👉 Similar to a **JavaScript object**
👉 Smallest unit of data in MongoDB

---

## **2️. Collection (Group of Documents)**

### What is a Collection?

A **collection** is a **group of related documents**.

### Example

```
students  (collection)
 ├── { name: "Smit", age: 21 }
 ├── { name: "Rahul", age: 22 }
```

👉 Like a **table** in MySQL
👉 No fixed schema (documents can differ)

---

## **3️. Datatypes (Values inside Documents)**

### What are Datatypes?

Datatypes define **what kind of data** a field holds **inside a document**.

---

## 🔸 Common MongoDB Datatypes (with examples)

### 1. String

```js
name: "Smit"
```

---

### 2. Number

```js
age: 21
```

---

### 3. Boolean

```js
isActive: true
```

---

### 4. Array

```js
skills: ["Node", "MongoDB"]
```

---

### 5. Object (Embedded Document)

```js
address: {
  city: "Surat",
  pincode: 395007
}
```

---

### 6. Date

```js
createdAt: new Date()
```

---

### 7. ObjectId (Important)

```js
_id: ObjectId("64abc...")
```

👉 Automatically added
👉 Unique identifier for each document

---

## **4️. How All Three Work Together**

```js
Database: collegeDB

Collection: students

Document:
{
  name: "Smit",          // String
  age: 21,               // Number
  skills: [],            // Array
  isActive: true         // Boolean
}
```

---


* **Document = One record**
* **Collection = Group of records**
* **Datatypes = Type of values inside document**

---
Great topic 👍 — this is **core backend knowledge** for MongoDB + Node.js.

Before we start, just to **calibrate** (quick check 👇):
👉 **Do you already know basic MongoDB concepts** like *documents, collections, ObjectId,* and *CRUD*?
(Just reply **yes / little / no** — one word is enough.)

---

## Big Picture (Roadmap)

We’ll learn this **step-by-step**, exactly how it’s done in **official Mongoose style**:

1. **Connecting MongoDB to Node.js using Mongoose**
2. **Understanding Database Relationships**

   * One-to-One
   * One-to-Many / Many-to-One
   * Many-to-Many
   * Polymorphic
3. **Handling Relationships with `populate()`**
4. **When to embed vs reference (very important)**

I’ll **not dump everything at once**.
We’ll build **realistic schemas**, ask you small questions, and confirm understanding before moving on.

---

# **6️⃣ Connecting MongoDB to Node.js (Mongoose way)**
---

### First, what is **Mongoose**?

Think of it as:

> **A layer on top of MongoDB that gives structure to data**

MongoDB itself is **schema-less**, but Mongoose adds:

* Schemas
* Models
* Validation
* Relationships (`populate`)

---

## 1. Install dependencies

```bash
npm install mongoose
```

---

## 2. Basic Project Structure (recommended)

```
project/
│── models/
│   └── User.js
│── db.js
│── index.js
```

This structure is **official-style & scalable**.

---

## 3. Connecting to MongoDB (db.js)

```js
const mongoose = require("mongoose");

const connectDB = async () => {
  try {
    await mongoose.connect("mongodb://127.0.0.1:27017/mydb");
    console.log("MongoDB Connected");
  } catch (error) {
    console.error(error);
    process.exit(1);
  }
};

module.exports = connectDB;
```

### Key ideas (important 🔑):

* `mongoose.connect()` → opens connection
* DB name = `mydb`
* Connection happens **once**, not in every file

---

## 4. Use connection in `index.js`

```js
const express = require("express");
const connectDB = require("./db");

const app = express();
connectDB();

app.listen(3000, () => {
  console.log("Server running");
});
```

👉 Now your **Node.js + MongoDB connection is ready**

---

Perfect 👍
Let’s move **one clean step forward**.

---

#  **7️⃣ Mongoose **Schema** & **Model** (Foundation for Relationships)**

Before relations, you **must** clearly understand these two:

### 🔹 Schema → *Blueprint*

### 🔹 Model → *Actual collection handler*

Think like this:

> **Schema = Design**
> **Model = Builder using that design**

---

## 1️. What is a Schema?

A **Schema defines**:

* Fields
* Data types
* Rules (required, unique, etc.)

### Example: `User` Schema

📁 `models/User.js`

```js
const mongoose = require("mongoose");

const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true
  },
  email: {
    type: String,
    required: true,
    unique: true
  },
  age: Number
});

module.exports = mongoose.model("User", userSchema);
```

### Understand line-by-line 🔍

* `new mongoose.Schema()` → creates structure
* `type` → data type
* `required` → validation
* `unique` → creates unique index
* `"User"` → **collection name becomes `users`** (pluralized automatically)

📌 **Official behavior of Mongoose**

---

## 2️. What is a Model?

A **Model**:

* Talks to the database
* Performs CRUD
* Uses Schema rules

```js
const User = mongoose.model("User", userSchema);
```

👉 `User` can now:

```js
User.find()
User.create()
User.findById()
```

---

## 3️. Insert a Document (Test)

```js
const User = require("./models/User");

const createUser = async () => {
  const user = await User.create({
    name: "Amit",
    email: "amit@gmail.com",
    age: 22
  });
  console.log(user);
};

createUser();
```

📌 MongoDB will store:

* `_id` → auto-generated `ObjectId`
* timestamps (if enabled later)

---

## ⚠️ Why this step is CRITICAL? 

Because **relationships in MongoDB are created by storing another document’s `_id` (ObjectId)**, and **Mongoose can understand and use those relationships only when they are clearly defined**.

---

👉 **No reference in Schema (`ObjectId + ref`) = No relationship**
👉 **No Model = No `populate()`**

---
Alright 👍
I’ll explain **ALL database relationships** **clearly, step-by-step**, with **simple structure**, **real examples**, and **Mongoose-official style**.
No confusion, no unnecessary theory.

---

#  **8️⃣Database Relationships(with Mongoose)**

MongoDB **does NOT have joins like SQL**.
Relationships are handled using:

* **ObjectId**
* **References**
* **populate()**

---

## **1️. One-to-One Relationship**

## Meaning

👉 One document is related to **exactly one** other document.

### Real-world example

* User ↔ Profile
* Person ↔ Passport

---

## Structure (Official Mongoose Style)

### User Schema

```js
const userSchema = new mongoose.Schema({
  name: String,
  email: String
});
```

### Profile Schema (reference user)

```js
const profileSchema = new mongoose.Schema({
  bio: String,
  address: String,
  user: {
    type: mongoose.Schema.Types.ObjectId,
    ref: "User",
    unique: true
  }
});
```

### Why `unique: true`?

✔ Ensures **one profile per user**

---

## Fetch using populate

```js
Profile.findOne().populate("user");
```

---

## Key idea 🧠

> **One-to-One = one document stores the other’s `_id` with uniqueness**

---

## **2️. One-to-Many / Many-to-One**

(Same relationship, different direction)

---

## Meaning

👉 One entity has **many** related entities
👉 Many entities belong to **one** entity

### Real-world example

* User → many Posts
* Category → many Products

---

## Structure (RECOMMENDED way)

### Post Schema (many → one)

```js
const postSchema = new mongoose.Schema({
  title: String,
  content: String,
  user: {
    type: mongoose.Schema.Types.ObjectId,
    ref: "User"
  }
});
```

📌 Each post stores **one User ID**

---

## Fetch posts with user

```js
Post.find().populate("user");
```

---

## Alternative (embedding IDs in User) ⚠️

```js
posts: [ObjectId]
```

❌ Not scalable
❌ User document grows too large

---

## Key idea 🧠

> **One-to-Many = many documents store one parent’s `_id`**

---

## **3️. Many-to-Many Relationship**

---

## Meaning

👉 Many documents relate to **many** others

### Real-world example

* Students ↔ Courses
* Users ↔ Roles

---

## Structure (Best Practice)

### Student Schema

```js
courses: [{
  type: mongoose.Schema.Types.ObjectId,
  ref: "Course"
}]
```

### Course Schema

```js
students: [{
  type: mongoose.Schema.Types.ObjectId,
  ref: "Student"
}]
```

✔ Both store arrays of ObjectIds

---

## Fetch

```js
Student.find().populate("courses");
```

---

## Key idea 🧠

> **Many-to-Many = arrays of ObjectIds on both sides**

---

## 4️. Polymorphic Relationship ⭐ (Advanced)

---

## Meaning

👉 One document can relate to **multiple model types**

### Real-world example

* Comment on **Post OR Video**
* Like on **Post OR Photo**

---

## Structure (Official Mongoose Pattern)

### Comment Schema

```js
const commentSchema = new mongoose.Schema({
  text: String,
  parentId: {
    type: mongoose.Schema.Types.ObjectId,
    required: true
  },
  parentType: {
    type: String,
    required: true,
    enum: ["Post", "Video"]
  }
});
```

---

## Populate dynamically

```js
Comment.find().populate({
  path: "parentId",
  model: doc => doc.parentType
});
```

---

## Key idea 🧠

> **Polymorphic = one `_id`, multiple possible models**

---

# 🔁 Summary Table (EXAM + INTERVIEW READY)

| Relationship | How it’s built            |
| ------------ | ------------------------- |
| One-to-One   | ObjectId + unique         |
| One-to-Many  | Child stores parent `_id` |
| Many-to-Many | Arrays of ObjectIds       |
| Polymorphic  | `_id` + `type` field      |

---
Great 👍
Now we’ll focus **only on `populate()`**, **step-by-step**, the way it’s used in **real projects** and **official Mongoose patterns**.

I’ll go **slow**, and after each concept I’ll check understanding.

---

# **9️⃣ Handling Relationships with `populate()`**

## First: What `populate()` actually does?

> **populate() replaces a referenced `_id` with the actual document from another collection**

Nothing more.

---

## 1️. Basic `populate()` (Single Reference)

### Example: Post → User (One-to-Many)

### Post Schema

```js
const postSchema = new mongoose.Schema({
  title: String,
  user: {
    type: mongoose.Schema.Types.ObjectId,
    ref: "User"
  }
});
```

---

### Query

```js
Post.find().populate("user");
```

### Result (concept)

```js
{
  title: "Hello",
  user: {
    _id: "...",
    name: "Amit",
    email: "amit@gmail.com"
  }
}
```

🧠 **ID → full object**

---

## 2️. Select specific fields 
By default, populate brings **everything** ❌
We usually want **limited data** ✅

### Query

```js
Post.find().populate("user", "name email");
```

### Meaning

* `"user"` → field to populate
* `"name email"` → fields to include

📌 Good for **performance & security**

---

## 3️. Populate Multiple Fields

### Example: Order → User + Product

```js
Order.find()
  .populate("user", "name")
  .populate("product", "title price");
```

🧠 You can call `populate()` **multiple times**

---

## 4️. Nested Populate (MOST IMPORTANT ⭐)

### Example

* User → Posts
* Post → Comments
* Comment → User

---

### Query

```js
User.find().populate({
  path: "posts",
  populate: {
    path: "comments",
    populate: {
      path: "user",
      select: "name"
    }
  }
});
```

### Meaning (read slowly):

* populate posts
* inside posts → populate comments
* inside comments → populate user

📌 This is **real-world usage**

---

## 5️. Populate with condition (filter using `match`)

### Example: only active users

```js
Post.find().populate({
  path: "user",
  match: { isActive: true },
  select: "name"
});
```

📌 If condition fails → `user` becomes `null`

---
## 6. Performance Rules (VERY IMPORTANT ⚠️)

❌ Bad:

* Populating large arrays
* Deep nesting everywhere

✅ Good:

* Select fields
* Paginate
* Populate only when needed

🧠 **populate is powerful but expensive**

---

# 🧾 Final Mental Model (MEMORIZE)

```
Schema tells WHAT to link
Model enables populate()
populate() fetches related data
```

---

# 🔁 Quick Summary Table

| Feature           | Use                  |
| ----------------- | -------------------- |
| Basic populate    | Replace ID           |
| select            | Limit fields         |
| multiple populate | Many refs            |
| nested populate   | Deep relations       |
| match             | Conditional populate |

---
