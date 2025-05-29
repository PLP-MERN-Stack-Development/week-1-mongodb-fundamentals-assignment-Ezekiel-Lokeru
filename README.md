# MongoDB Queries Reference (`queries.js`)

This file contains a collection of MongoDB queries and aggregation pipelines for the `plp_bookstore` database, specifically targeting the `books` collection.

## Contents

- Basic CRUD operations
- Advanced queries (filtering, projection, sorting, pagination)
- Aggregation pipelines (data analysis)
- Indexing and performance analysis

---

## Usage

Copy and paste these queries into your MongoDB Shell (`mongosh`) or MongoDB Compass "Aggregations" or "Filter" sections as appropriate.  
Make sure you have already populated your database using [`insert_books.js`](insert_books.js).

---

## Query List

### 1. Find all books in a specific genre
```js
db.books.find({ genre: "Fiction" })
```

### 2. Find books published after a certain year
```js
db.books.aggregate([{ $match: { published_year: { $gte: 1960 }}}])
```

### 3. Find books by a specific author
```js
db.books.find({ author: "George Orwell" })
```

### 4. Update the price of a specific book
```js
db.books.updateOne(
  { title: "1984" },
  { $set: { price: 12.99 } }
)
```

### 5. Delete a book by its title
```js
db.books.deleteOne({ title: "Brave New World" })
```

### 6. Find books that are both in stock and published after 2010
```js
db.books.find({ in_stock: true, published_year: { $gt: 2010 } })
```

### 7. Projection: Return only the title, author, and price fields
```js
db.books.find(
  {},
  { title: 1, author: 1, price: 1, _id: 0 }
)
```

### 8. Sorting by price  
Ascending:
```js
db.books.find().sort({ price: 1 })
```
Descending:
```js
db.books.find().sort({ price: -1 })
```

### 9. Pagination (5 books per page)
```js
const pageSize = 5;
const pageNumber = 1; // Change this for different pages

db.books.find()
  .skip((pageNumber - 1) * pageSize)
  .limit(pageSize)
```

### 10. Aggregation: Average price of books by genre
```js
db.books.aggregate([
  { $group: { _id: "$genre", averagePrice: { $avg: "$price" } } },
  { $sort: { averagePrice: 1 } }
])
```

### 11. Aggregation: Author with the most books
```js
db.books.aggregate([
  { $group: { _id: "$author", bookCount: { $sum: 1 } } },
  { $sort: { bookCount: -1 } },
  { $limit: 1 }
])
```

### 12. Aggregation: Group books by publication decade and count them
```js
db.books.aggregate([
  { $group: { _id: { $floor: { $divide: ["$published_year", 10] } }, bookCount: { $sum: 1 } } },
  { $project: { decade: { $multiply: ["$_id", 10] }, bookCount: 1, _id: 0 } },
  { $sort: { decade: 1 } }
])
```

### 13. Indexing  
Create an index on the title field:
```js
db.books.createIndex({ title: 1 })
```
Create a compound index on author and published_year:
```js
db.books.createIndex({ author: 1, published_year: -1 })
```

### 14. Performance Analysis with explain()
```js
db.books.find({ title: "1984" }).explain("executionStats")
```

---

## Notes

- These queries assume your database is named `plp_bookstore` and your collection is `books`.
- For aggregation pipelines, use the "Aggregations" tab in MongoDB Compass or run in `mongosh`.
- Adjust parameters (e.g., genre, author, page number) as needed for your use case.