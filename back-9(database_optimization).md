# 1. Indexing for Performance in MongoDB

### ❓ What is an Index? (SQL link)

An **index** is a special data structure that helps MongoDB **find documents faster**.

Just like **INDEX in SQL**, MongoDB indexes avoid scanning every document in a collection.

* **Without index** → MongoDB performs a *collection scan* (checks every document)
* **With index** → MongoDB directly locates matching documents

Indexes are stored separately from the actual data and are automatically used by MongoDB’s query optimizer when beneficial.

---

### 🔹 Single-Field Index

An index created on **one field only**.

```js
db.users.createIndex({ email: 1 })
```

**Use case**

```js
db.users.find({ email: "abc@gmail.com" })
```

* `1` → ascending order
* `-1` → descending order

✔ Very fast lookups
✔ Most commonly used index
✔ Ideal for unique fields like email, username, phone

---

### 🔹 Compound Index

An index created on **multiple fields together**.

```js
db.orders.createIndex({ userId: 1, createdAt: -1 })
```

MongoDB stores this index in the **same order** as defined.

**Efficient for queries like:**

```js
{ userId: 101 }
{ userId: 101, createdAt: { $gt: ISODate("2024-01-01") } }
```

❌ **Not efficient for:**

```js
{ createdAt: ... }
```

👉 Similar to SQL composite index:

```sql
INDEX(userId, createdAt)
```

---

### 🔹 Text Index

Used for **text searching** inside string fields.

```js
db.posts.createIndex({ title: "text", content: "text" })
```

Query:

```js
db.posts.find({ $text: { $search: "mongodb indexing" } })
```

* Searches words, not exact strings
* Supports relevance scoring

✔ Useful for blogs, articles, search bars
❌ Not suitable for pattern matching like regex

---

### 🔹 Wildcard Index

Used when documents contain **dynamic or unknown fields**.

```js
db.products.createIndex({ "attributes.$**": 1 })
```

Example document:

```js
attributes: {
  color: "red",
  size: "M",
  brand: "Nike"
}
```

✔ Flexible schema support
✔ Useful in product catalogs
❌ Slightly heavier than normal indexes

---

## ✅ Best Practices for Indexing

✔ Create indexes on fields frequently used in:

* `find()`
* `sort()`
* `$match` (aggregation)

✔ Place the **most selective field first** in compound indexes
✔ Remove unused indexes periodically

❌ Avoid over-indexing

* Each index increases storage
* Slows down insert, update, and delete operations

✔ Use `explain()` to analyze performance

```js
db.users.find({ age: 25 }).explain("executionStats")
```

---

# 2. MongoDB Aggregation

Aggregation is a way to **process multiple documents and return computed results**.

It works using an **aggregation pipeline**, where documents pass through multiple stages.

SQL equivalent:

```sql
SELECT department, COUNT(*) FROM employees GROUP BY department;
```

MongoDB equivalent:

```js
db.employees.aggregate([
  { $group: { _id: "$department", count: { $sum: 1 } } }
])
```

Aggregation is commonly used for reports, analytics, and dashboards.

---

# 3. Comparison Operators

Used to **compare field values** during querying.

| Operator | Meaning                       |
| -------- | ----------------------------- |
| `$eq`    | equal                         |
| `$ne`    | not equal                     |
| `$lt`    | less than                     |
| `$gt`    | greater than                  |
| `$lte`   | less than or equal            |
| `$gte`   | greater than or equal         |
| `$in`    | value exists in array         |
| `$nin`   | value does not exist in array |

Examples:

```js
db.users.find({ age: { $gte: 18, $lte: 25 } })
```

```js
db.users.find({ role: { $in: ["admin", "manager"] } })
```

---

# 4. Logical Operators

Used to **combine multiple conditions**.

### 🔹 `$and`

```js
db.users.find({
  $and: [
    { age: { $gt: 18 } },
    { city: "Delhi" }
  ]
})
```

### 🔹 `$or`

```js
db.users.find({
  $or: [
    { role: "admin" },
    { role: "manager" }
  ]
})
```

### 🔹 `$not`

```js
db.users.find({ age: { $not: { $gte: 18 } } })
```

### 🔹 `$nor`

```js
db.users.find({
  $nor: [
    { city: "Mumbai" },
    { city: "Delhi" }
  ]
})
```

---

# 5. Array Operators

Used to **modify array fields**.

Assume:

```js
skills: ["JS", "Node"]
```

### 🔹 `$push`

Adds a value to the array.

```js
db.users.updateOne(
  { _id: 1 },
  { $push: { skills: "MongoDB" } }
)
```

### 🔹 `$addToSet`

Adds value **only if it does not already exist**.

```js
{ $addToSet: { skills: "Node" } }
```

### 🔹 `$pull`

Removes a specific value.

```js
{ $pull: { skills: "JS" } }
```

### 🔹 `$pop`

Removes element by position.

```js
{ $pop: { skills: 1 } }   // last
{ $pop: { skills: -1 } } // first
```

---

# 6. Aggregation Pipeline Stages

### 🔹 `$match`

Filters documents early.

```js
{ $match: { age: { $gt: 18 } } }
```

### 🔹 `$group`

Groups documents and applies calculations.

```js
{
  $group: {
    _id: "$department",
    total: { $sum: 1 }
  }
}
```

### 🔹 `$project`

Controls which fields appear in output.

```js
{ $project: { name: 1, age: 1, _id: 0 } }
```

### 🔹 `$sort`

Orders documents.

```js
{ $sort: { age: -1 } }
```

### 🔹 `$lookup`

Performs join-like operations.

```js
{
  $lookup: {
    from: "orders",
    localField: "_id",
    foreignField: "userId",
    as: "orders"
  }
}
```

---

# 7. Creating Database

### 🔹 Local MongoDB

```bash
mongosh
use mydb
```

The database is created automatically when data is inserted.

---

### 🔹 MongoDB Atlas

Steps:

1. Create cluster
2. Whitelist IP & create user
3. Get connection string
4. Connect using Compass or application

```js
mongodb+srv://user:pass@cluster.mongodb.net/mydb
```

---

# 8. Parallel Pipeline with `$facet`

Allows running **multiple aggregation pipelines in parallel**.

```js
db.products.aggregate([
  {
    $facet: {
      priceStats: [
        { $group: { _id: null, avg: { $avg: "$price" } } }
      ],
      categories: [
        { $group: { _id: "$category" } }
      ]
    }
  }
])
```

Frequently used in analytics dashboards and reporting APIs.

---

# 9. MongoDB Operator Types

### 🔹 Comparison Operators

Used for filtering values.

`$eq, $gt, $lt, $in`

---

### 🔹 Regex Operators

Used for pattern matching.

```js
db.users.find({ name: { $regex: "^A" } })
```

---

### 🔹 Update Operators

Used to modify documents.

`$set, $unset, $inc, $push`

```js
{ $set: { age: 25 } }
```

---

### 🔹 Aggregation Operators

Used inside aggregation pipeline.

`$sum, $avg, $max, $min`
