# Advanced MongoDB Commands in Mongosh

This section provides more complex examples of MongoDB commands for advanced use cases involving queries, aggregation, updates, and indexing.

## Step 1: Advanced Queries

### Querying Embedded Documents

In cases where documents have embedded sub-documents, you can query based on fields within them.

#### Example: Find Customers with Specific Orders

```sh
db.customers.find({ "orders.product_id": ObjectId("PRODUCT_ID") })
```

### Using `$in` and `$nin` for Multiple Matches

The `$in` operator matches any of the specified values, and `$nin` negates that match.

#### Example: Find Products with Price in a Specific Range

```sh
db.products.find({ "price": { "$in": [99.99, 199.99, 299.99] } })
```

#### Example: Find Customers Without Specific Emails

```sh
db.customers.find({ "email": { "$nin": ["zephyra.langston@example.com", "orik.dross@example.com"] } })
```

### Querying with Regular Expressions

You can use regular expressions to search for patterns within text fields.

#### Example: Find Customers with Email Addresses Containing 'example'

```sh
db.customers.find({ "email": { "$regex": /example/ } })
```

### Querying with `$and` and `$or` Conditions

MongoDB allows complex queries with logical conditions.

#### Example: Find Customers Who Ordered Specific Products AND Live in a Specific City

```sh
db.customers.find({
  "$and": [
    { "orders.product_id": ObjectId("PRODUCT_ID") },
    { "city": "New York" }
  ]
})
```

#### Example: Find Products with a Price Greater Than $100 OR Named 'Smartwatch'

```sh
db.products.find({
  "$or": [
    { "price": { "$gt": 100 } },
    { "product_name": "Smartwatch" }
  ]
})
```

## Step 2: Advanced Updates

### Using `$push` to Add to an Array

The `$push` operator allows you to append a value to an array.

#### Example: Add a New Order to a Customer's Order List

```sh
db.customers.updateOne(
  { "name": "Liora Vale" },
  { "$push": { "orders": { "product_id": ObjectId("NEW_PRODUCT_ID"), "quantity": 2 } } }
)
```

### Using `$pull` to Remove from an Array

The `$pull` operator removes elements from an array that match a specified condition.

#### Example: Remove a Specific Order from a Customer's Orders

```sh
db.customers.updateOne(
  { "name": "Ronan Darkwater" },
  { "$pull": { "orders": { "product_id": ObjectId("OLD_PRODUCT_ID") } } }
)
```

### Using `$addToSet` to Add Unique Values to an Array

The `$addToSet` operator adds a value to an array only if it doesn't already exist in the array.

#### Example: Add a Tag to a Product if Not Already Present

```sh
db.products.updateOne(
  { "product_name": "Bluetooth Speaker" },
  { "$addToSet": { "tags": "portable" } }
)
```

### Updating Multiple Documents with `$set` and `$mul`

You can update multiple fields at once and perform arithmetic operations.

#### Example: Set Multiple Fields and Multiply Price by a Factor

```sh
db.products.updateMany(
  { "price": { "$lt": 200 } },
  {
    "$set": { "status": "on sale" },
    "$mul": { "price": 0.9 }  // Apply a 10% discount
  }
)
```

## Step 3: Advanced Aggregation

### Aggregating with `$unwind` and `$lookup`

The `$unwind` operator deconstructs arrays in documents, and `$lookup` is used for joining collections.

#### Example: Find Total Orders per Customer and Lookup Product Details

```sh
db.customers.aggregate([
  { "$unwind": "$orders" },
  { "$lookup": {
      "from": "products",
      "localField": "orders.product_id",
      "foreignField": "_id",
      "as": "product_info"
    }
  },
  { "$group": {
      "_id": "$name",
      "totalOrders": { "$sum": 1 },
      "totalSpent": { "$sum": { "$multiply": [ "$orders.quantity", { "$arrayElemAt": ["$product_info.price", 0] } ] } }
    }
  }
])
```

### Conditional Aggregation with `$cond`

You can perform conditional logic inside an aggregation pipeline using `$cond`.

#### Example: Categorize Customers Based on Total Spent

```sh
db.customers.aggregate([
  { "$unwind": "$orders" },
  { "$lookup": {
      "from": "products",
      "localField": "orders.product_id",
      "foreignField": "_id",
      "as": "product_info"
    }
  },
  { "$group": {
      "_id": "$name",
      "totalSpent": { "$sum": { "$multiply": [ "$orders.quantity", { "$arrayElemAt": ["$product_info.price", 0] } ] } }
    }
  },
  { "$project": {
      "name": 1,
      "totalSpent": 1,
      "category": {
        "$cond": {
          "if": { "$gte": ["$totalSpent", 500] },
          "then": "Premium",
          "else": "Regular"
        }
      }
    }
  }
])
```

### Aggregating Nested Fields with `$reduce`

Use `$reduce` to apply operations across array elements.

#### Example: Calculate Total Quantity of Orders

```sh
db.customers.aggregate([
  { "$project": {
      "name": 1,
      "totalQuantity": {
        "$reduce": {
          "input": "$orders",
          "initialValue": 0,
          "in": { "$add": ["$$value", "$$this.quantity"] }
        }
      }
    }
  }
])
```

## Step 4: Advanced Indexing

### Creating Compound Indexes

A compound index on multiple fields can improve query performance.

#### Example: Indexing on Product Name and Price

```sh
db.products.createIndex({ "product_name": 1, "price": -1 })
```

### Text Indexing for Full-Text Search

Create a text index to perform searches across multiple fields.

#### Example: Create a Text Index on Product Name and Description

```sh
db.products.createIndex({ "product_name": "text", "description": "text" })
```

#### Example: Perform a Text Search on Indexed Fields

```sh
db.products.find({ "$text": { "$search": "wireless" } })
```

### Geospatial Indexing

MongoDB supports geospatial queries with 2D indexes.

#### Example: Create a 2D Geospatial Index for Location-Based Search

```sh
db.stores.createIndex({ "location": "2dsphere" })
```

#### Example: Find Stores Near a Specific Location

```sh
db.stores.find({
  "location": {
    "$near": {
      "$geometry": { "type": "Point", "coordinates": [ -73.97, 40.77 ] },
      "$maxDistance": 5000
    }
  }
})
```
