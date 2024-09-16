# MongoDB CRUD Operations with Shell

MongoDB is a NoSQL database that allows you to store and query large amounts of unstructured data. In this tutorial, we will go through the basic CRUD (Create, Read, Update, Delete) operations using the MongoDB shell. Additionally, we will explore creating indexes and performing aggregations.

This tutorial cover basic CRUD operations, indexes, and aggregation in MongoDB. With these commands, you can effectively manage and query data in MongoDB.

## Table of Contents

1. [Create Operations](#create-operations)
   - [Creating a Database](#creating-a-database)
   - [Creating a Collection](#creating-a-collection)
   - [Inserting Documents](#inserting-documents)
2. [Read Operations](#read-operations)
3. [Update Operations](#update-operations)
4. [Delete Operations](#delete-operations)
5. [Indexes](#indexes)
6. [Aggregations](#aggregations)

---

## Create Operations

### Creating a Database

MongoDB does not explicitly provide a command to create a database. A database is automatically created when you insert data into it for the first time. You can switch to a database using the `use` command:

```shell
use myDatabase
```

This command switches the context to `myDatabase`. If it doesn't exist, it will be created when a collection or document is added.

### Creating a Collection

A collection in MongoDB is analogous to a table in relational databases. A collection is automatically created when you first insert a document. However, you can explicitly create one:

```shell
db.createCollection("myCollection")
```

This creates a collection named `myCollection`.

### Inserting Documents

Documents in MongoDB are JSON-like objects. You can insert a single document using the `insertOne` command or multiple documents using `insertMany`.

```shell
// Inserting a single document
db.myCollection.insertOne({
  name: "Victor May",
  age: 28,
  profession: "Engineer"
})

// Inserting multiple documents
db.myCollection.insertMany([
  { name: "Jasmine May", age: 26, profession: "Doctor" },
  { name: "Samanta Stevenson", age: 32, profession: "Artist" }
])
```

---

## Read Operations

To retrieve data, MongoDB provides multiple query mechanisms. The basic command to find documents is `find`.

```shell
// Fetching all documents in the collection
db.myCollection.find()

// Fetching documents with a query filter
db.myCollection.find({ age: { $gt: 30 } })
```

You can also use `findOne` to retrieve a single document:

```shell
db.myCollection.findOne({ name: "Victor May" })
```

---

## Update Operations

You can update documents using `updateOne`, `updateMany`, or `replaceOne`. These commands allow you to modify the data in existing documents.

```shell
// Updating a single document
db.myCollection.updateOne(
  { name: "Victor May" },
  { $set: { age: 29 } }
)

// Updating multiple documents
db.myCollection.updateMany(
  { age: { $gt: 30 } },
  { $set: { profession: "Senior Engineer" } }
)
```

You can also completely replace a document using `replaceOne`:

```shell
db.myCollection.replaceOne(
  { name: "Victor May" },
  { name: "Victor May", age: 30, profession: "Consultant" }
)
```

---

## Delete Operations

MongoDB provides the `deleteOne` and `deleteMany` commands to remove documents.

```shell
// Deleting a single document
db.myCollection.deleteOne({ name: "Victor May" })

// Deleting multiple documents
db.myCollection.deleteMany({ age: { $lt: 30 } })
```

---

## Indexes

Indexes in MongoDB improve the efficiency of query operations. You can create an index using the `createIndex` command.

```shell
// Creating an index on the 'name' field
db.myCollection.createIndex({ name: 1 })
```

The number `1` indicates ascending order, and `-1` indicates descending order.

To list all indexes on a collection:

```shell
db.myCollection.getIndexes()
```

To remove an index:

```shell
db.myCollection.dropIndex("name_1")
```

---

## Aggregations

Aggregation operations process data records and return computed results. They are used to perform operations such as grouping, sorting, and filtering.

### Basic Aggregation Example

```shell
db.myCollection.aggregate([
  { $match: { profession: "Engineer" } },
  { $group: { _id: "$profession", total: { $sum: 1 } } }
])
```

This example filters documents by `profession: Engineer` and then groups them by `profession`, counting the number of engineers.

### Other Aggregation Stages

- `$project`: Selects specific fields to include or exclude.
- `$sort`: Sorts the documents.
- `$limit`: Limits the number of documents returned.

For example, sorting the documents by age in descending order:

```shell
db.myCollection.aggregate([
  { $sort: { age: -1 } }
])
```

---
