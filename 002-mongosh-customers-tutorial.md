# MongoDB Tutorial: Working with Mongosh

This tutorial will guide you through creating a MongoDB database, defining collections, and performing CRUD operations, indexing, and aggregation using `mongosh`.

### Prerequisites

- Install MongoDB on your system and ensure `mongosh` is available.
- Launch the MongoDB shell by typing `mongosh` in your terminal.

## Step 1: Creating a Database

To create or switch to a new database, use the `use` command. MongoDB will create the database when you insert data into it.

```sh
use shopDB
```

## Step 2: Creating Collections

MongoDB collections are similar to tables in relational databases. Let's create three collections: `customers`, `products`, and `inventory`.

```sh
db.createCollection("customers")
db.createCollection("products")
db.createCollection("inventory")
```

## Step 3: Inserting Documents (Create)

### Inserting into `customers`

```sh
db.customers.insertMany([
  { "name": "Zephyra Langston", "email": "zephyra.langston@example.com" },
  { "name": "Orik Dross", "email": "orik.dross@example.com" },
  { "name": "Elowen Silverleaf", "email": "elowen.silverleaf@example.com" }
])
```

### Inserting into `products`

```sh
db.products.insertMany([
  { "product_name": "Wireless Headphones", "price": 99.99 },
  { "product_name": "Bluetooth Speaker", "price": 49.99 },
  { "product_name": "Smartwatch", "price": 199.99 }
])
```

### Inserting into `inventory`

Change the ids or get it automatically

```sh
db.inventory.insertMany([
  { "product_id": ObjectId("PRODUCT_ID"), "quantity": 100 },
  { "product_id": ObjectId("PRODUCT_ID"), "quantity": 50 }
])
```

```sh
db.inventory.insertMany([
  { "product_id": "66e977e009d929e03bc73bfe", "quantity": 100 },
  { "product_id": "66e977e009d929e03bc73bff", "quantity": 50 }
])
```

## Step 4: Reading Data (Read)

### Finding All Documents in a Collection

```sh
db.customers.find()
db.products.find()
```

### Querying with Filters

```sh
db.customers.find({ "name": "Zephyra Langston" })
db.products.find({ "price": { "$gt": 50 } })
```

## Step 5: Updating Documents (Update)

### Updating a Customer's Email

```sh
db.customers.updateOne(
  { "name": "Orik Dross" },
  { "$set": { "email": "orik.dross123@example.com" } }
)
```

### Incrementing Product Quantity in Inventory

change the id:

```sh
db.inventory.updateOne(
  { "product_id": ObjectId("PRODUCT_ID") },
  { "$inc": { "quantity": 20 } }
)
```

```sh
db.inventory.updateOne(
  { "product_id": "66e977e009d929e03bc73bfe" },
  { "$inc": { "quantity": 20 } }
)
```

## Step 6: Deleting Documents (Delete)

### Deleting a Customer

```sh
db.customers.find()
```

```sh
db.customers.deleteOne({ "name": "Elowen Silverleaf" })
```

```sh
db.customers.find()
```

### Deleting All Products Under a Certain Price

```sh
db.products.deleteMany({ "price": { "$lt": 50 } })
```

## Step 7: Creating Indexes

### Indexing on Customer Names

```sh
db.customers.createIndex({ "name": 1 })
```

### Indexing on Product Prices

```sh
db.products.createIndex({ "price": 1 })
```

## Step 8: Aggregation

### Aggregating Average Product Price

```sh
db.products.aggregate([
  { "$group": { "_id": null, "averagePrice": { "$avg": "$price" } } }
])
```

## Step 9: Relations Between Collections

### Linking Customers and Orders

Assume we want to create a relationship between customers and their orders by embedding or referencing.

#### Embedding Example:

Change ids:

```sh
db.customers.insertOne({
  "name": "Faelan Windwalker",
  "email": "faelan.windwalker@example.com",
  "orders": [
    { "product_id": ObjectId("PRODUCT_ID"), "quantity": 1 },
    { "product_id": ObjectId("PRODUCT_ID"), "quantity": 2 }
  ]
})
```

```sh
db.customers.find()
```

```sh
db.customers.insertOne({
  "name": "Faelan Windwalker",
  "email": "faelan.windwalker@example.com",
  "orders": [
    { "product_id": '66e977e009d929e03bc73bfe', "quantity": 1 },
    { "product_id": '66e977e009d929e03bc73c00', "quantity": 2 }
  ]
})
```

```sh
db.customers.find()
```

#### Referencing Example:

Change ids:

```sh
db.customers.find()
```

```sh
db.products.find()
```

```sh
db.orders.insertOne({
  "customer_id": '66e977d409d929e03bc73bfb',
  "order_details": [
    { "product_id": '66e977e009d929e03bc73bfe', "quantity": 1 },
    { "product_id": '66e977e009d929e03bc73c00', "quantity": 3 }
  ]
})
```
