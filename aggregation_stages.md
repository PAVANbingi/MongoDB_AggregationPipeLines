


# MongoDB Aggregation Pipelines
Hi, aliens! I am [Pavan](https://github.com/PAVANbingi). So in this [repository](https://github.com/PAVANbingi/MongoDB_AggregationPipeLines), I will explain all the aggregation stages in depth with basic examples. I will also include links to resources for further learning.

So this repository contains JSON files for various MongoDB aggregation pipelines. These pipelines demonstrate how to use different aggregation stages and operations to process and analyze data.

## Table of Contents

- [Introduction](#introduction)
- [CRUD Operations](#crud-operations)
- [Aggregation Stages](#aggregation-stages)
  - [$match](#match)
  - [$group](#group)
  - [$project](#project)
  - [$sort](#sort)
  - [$limit](#limit)
  - [$skip](#skip)
  - [$lookup](#lookup)
  - [$unwind](#unwind)
  - [$addFields](#addfields)
  - [$replaceRoot](#replaceroot)
- [Aggregation Operations](#aggregation-operations)
  - [$sum](#sum)
  - [$avg](#avg)
  - [$min](#min)
  - [$max](#max)
  - [$first](#first)
  - [$last](#last)
- [Example Datasets](#example-datasets)
- [Resources for Further Learning](#resources-for-further-learning)

## Introduction

Aggregation in MongoDB is a powerful way to process and analyze data stored in collections. It allows you to perform operations like filtering, grouping, sorting, and transforming data.

## CRUD Operations

### Create

```javascript
db.orders.insertOne({
  "order_id": 26,
  "cust_id": 1006,
  "status": "A",
  "amount": 275,
  "items": ["apple", "banana"],
  "date": "2023-01-26"
});
```

### Read

```javascript
db.orders.find().pretty();
```

### Update

```javascript
db.orders.updateOne(
  { "order_id": 2 },
  {
    $set: { "status": "C", "amount": 500 },
    $currentDate: { "lastModified": true }
  }
);
```

### Delete

```javascript
db.orders.deleteOne({ "order_id": 1 });
```

## Aggregation Stages

### $match

Filters the documents to pass only the documents that match the specified condition(s) to the next pipeline stage.

```javascript
db.orders.aggregate([
  { $match: { "status": "A" } }
]);
```

### $group

Groups input documents by the specified _id expression and for each distinct grouping, outputs a document. The _id field contains the unique group by value.

```javascript
db.orders.aggregate([
  {
    $group: {
      _id: "$cust_id",
      totalSpent: { $sum: "$amount" }
    }
  }
]);
```

### $project

Passes along the documents with the requested fields to the next stage in the pipeline.

```javascript
db.orders.aggregate([
  { $project: { "order_id": 1, "items": 1, "_id": 0 } }
]);
```

### $sort

Sorts all input documents and returns them to the pipeline in sorted order.

```javascript
db.orders.aggregate([
  { $sort: { "amount": -1 } }
]);
```

### $limit

Limits the number of documents passed to the next stage in the pipeline.

```javascript
db.orders.aggregate([
  { $limit: 5 }
]);
```

### $skip

Skips the first n documents and passes the remaining documents to the next stage in the pipeline.

```javascript
db.orders.aggregate([
  { $skip: 5 }
]);
```

### $lookup

Performs a left outer join to another collection in the same database to filter in documents from the "joined" collection for processing.

```javascript
db.orders.aggregate([
  {
    $lookup: {
      from: "orderDetails",
      localField: "order_id",
      foreignField: "order_id",
      as: "details"
    }
  }
]);
```

### $unwind

Deconstructs an array field from the input documents to output a document for each element.

```javascript
db.orders.aggregate([
  { $unwind: "$items" }
]);
```

### $addFields

Adds new fields to documents.

```javascript
db.orders.aggregate([
  { $addFields: { totalWithTax: { $multiply: ["$amount", 1.1] } } }
]);
```

### $replaceRoot

Replaces the input document with the specified document.

```javascript
db.orders.aggregate([
  { $replaceRoot: { newRoot: "$items" } }
]);
```

## Aggregation Operations

### $sum

Calculates and returns the sum of numeric values. $sum ignores non-numeric values.

```javascript
db.orders.aggregate([
  {
    $group: {
      _id: "$cust_id",
      totalSpent: { $sum: "$amount" }
    }
  }
]);
```

### $avg

Calculates and returns the average value of the numeric values.

```javascript
db.orders.aggregate([
  {
    $group: {
      _id: "$cust_id",
      averageSpent: { $avg: "$amount" }
    }
  }
]);
```

### $min

Returns the minimum value from the numeric values.

```javascript
db.orders.aggregate([
  {
    $group: {
      _id: "$cust_id",
      minSpent: { $min: "$amount" }
    }
  }
]);
```

### $max

Returns the maximum value from the numeric values.

```javascript
db.orders.aggregate([
  {
    $group: {
      _id: "$cust_id",
      maxSpent: { $max: "$amount" }
    }
  }
]);
```

### $first

Returns the first value from the documents for each group.

```javascript
db.orders.aggregate([
  {
    $group: {
      _id: "$cust_id",
      firstOrder: { $first: "$amount" }
    }
  }
]);
```

### $last

Returns the last value from the documents for each group.

```javascript
db.orders.aggregate([
  {
    $group: {
      _id: "$cust_id",
      lastOrder: { $last: "$amount" }
    }
  }
]);
```

## Example Datasets

Example documents used for performing CRUD and aggregation operations:

```json
[
  { "order_id": 1, "cust_id": 1001, "status": "A", "amount": 250, "items": ["apple", "banana"], "date": "2023-01-01" },
  { "order_id": 2, "cust_id": 1002, "status": "B", "amount": 450, "items": ["orange", "grape"], "date": "2023-01-02" },
  { "order_id": 3, "cust_id": 1001, "status": "A", "amount": 300, "items": ["apple", "orange"], "date": "2023-01-03" },
  { "order_id": 4, "cust_id": 1003, "status": "A", "amount": 150, "items": ["banana", "grape"], "date": "2023-01-04" },
  { "order_id": 5, "cust_id": 1002, "status": "C", "amount": 500, "items": ["apple", "banana"], "date": "2023-01-05" },
  { "order_id": 6, "cust_id": 1004, "status": "A", "amount": 350, "items": ["orange", "banana"], "date": "2023-01-06" },
  { "order_id": 7, "cust_id": 1005, "status": "B", "amount": 200, "items": ["grape", "banana"], "date": "2023-01-07" },
  { "order_id": 8, "cust_id": 1003, "status": "A", "amount": 100, "items": ["apple", "orange"], "date": "2023-01-08" },
  { "order_id": 9, "cust_id": 1004, "status": "C", "amount": 400, "items": ["banana", "grape"], "date": "2023-01-09" },
  { "order_id": 10, "cust_id": 1001, "status": "A", "amount": 250, "items": ["apple", "grape"], "date": "2023-01-10" },
  { "order_id": 11, "cust_id": 1002, "status": "B", "amount": 350, "items": ["orange", "banana"], "date": "2023-01-11" },
  { "order_id": 12, "cust_id": 1003, "status": "A", "amount": 450, "items": ["apple", "orange"], "date": "2023-01-12" },
  { "order_id": 13, "cust_id": 1005, "status": "A", "amount": 150, "items": ["banana", "grape"], "date": "2023-01-13" },
  { "order_id": 14, "cust_id": 1004, "status": "C

", "amount": 500, "items": ["apple", "banana"], "date": "2023-01-14" },
  { "order_id": 15, "cust_id": 1002, "status": "A", "amount": 300, "items": ["orange", "grape"], "date": "2023-01-15" },
  { "order_id": 16, "cust_id": 1003, "status": "B", "amount": 200, "items": ["apple", "banana"], "date": "2023-01-16" },
  { "order_id": 17, "cust_id": 1001, "status": "A", "amount": 250, "items": ["orange", "grape"], "date": "2023-01-17" },
  { "order_id": 18, "cust_id": 1005, "status": "A", "amount": 350, "items": ["apple", "banana"], "date": "2023-01-18" },
  { "order_id": 19, "cust_id": 1004, "status": "C", "amount": 400, "items": ["orange", "grape"], "date": "2023-01-19" },
  { "order_id": 20, "cust_id": 1001, "status": "B", "amount": 150, "items": ["apple", "orange"], "date": "2023-01-20" },
  { "order_id": 21, "cust_id": 1002, "status": "A", "amount": 500, "items": ["banana", "grape"], "date": "2023-01-21" },
  { "order_id": 22, "cust_id": 1003, "status": "A", "amount": 450, "items": ["apple", "banana"], "date": "2023-01-22" },
  { "order_id": 23, "cust_id": 1004, "status": "B", "amount": 350, "items": ["orange", "banana"], "date": "2023-01-23" },
  { "order_id": 24, "cust_id": 1005, "status": "A", "amount": 200, "items": ["grape", "banana"], "date": "2023-01-24" },
  { "order_id": 25, "cust_id": 1001, "status": "A", "amount": 300, "items": ["apple", "orange"], "date": "2023-01-25" }
]
```

## Resources for Further Learning

- [MongoDB Aggregation Documentation](https://docs.mongodb.com/manual/aggregation/)
- [MongoDB University Courses](https://university.mongodb.com/)
- [MongoDB Aggregation Pipeline Builder](https://studio3t.com/knowledge-base/articles/mongodb-aggregation-framework/)

Feel free to clone this repository and experiment with the aggregation pipelines provided. If you have any questions or suggestions, please open an issue or submit a pull request.
```

### Additional Aggregation Examples

To make the README more comprehensive, you can include more detailed examples for each aggregation stage and operation:

```markdown
## Additional Aggregation Examples

### $match

Filters documents by status "A" and amount greater than 200.

```javascript
db.orders.aggregate([
  { $match: { "status": "A", "amount": { $gt: 200 } } }
]);
```

### $group

Groups orders by status and calculates the total amount and average amount for each status.

```javascript
db.orders.aggregate([
  {
    $group: {
      _id: "$status",
      totalAmount: { $sum: "$amount" },
      averageAmount: { $avg: "$amount" }
    }
  }
]);
```

### $project

Projects the order ID, customer ID, and a calculated field for the total amount with tax (assuming 10% tax).

```javascript
db.orders.aggregate([
  {
    $project: {
      "order_id": 1,
      "cust_id": 1,
      "totalWithTax": { $multiply: ["$amount", 1.1] }
    }
  }
]);
```

### $sort

Sorts orders first by status in ascending order and then by amount in descending order.

```javascript
db.orders.aggregate([
  { $sort: { "status": 1, "amount": -1 } }
]);
```

### $limit

Limits the result to the top 3 orders with the highest amount.

```javascript
db.orders.aggregate([
  { $sort: { "amount": -1 } },
  { $limit: 3 }
]);
```

### $skip

Skips the first 5 orders and returns the rest.

```javascript
db.orders.aggregate([
  { $skip: 5 }
]);
```

### $lookup

Joins the `orders` collection with an `orderDetails` collection to add order details.

```javascript
db.orders.aggregate([
  {
    $lookup: {
      from: "orderDetails",
      localField: "order_id",
      foreignField: "order_id",
      as: "details"
    }
  }
]);
```

### $unwind

Deconstructs the `items` array in each order to output a document for each item.

```javascript
db.orders.aggregate([
  { $unwind: "$items" }
]);
```

### $addFields

Adds a new field `discountedAmount` which is 90% of the original amount.

```javascript
db.orders.aggregate([
  { $addFields: { discountedAmount: { $multiply: ["$amount", 0.9] } } }
]);
```

### $replaceRoot

Replaces the root document with the `items` array.

```javascript
db.orders.aggregate([
  { $replaceRoot: { newRoot: "$items" } }
]);
```

### $sum

Calculates the total amount for all orders.

```javascript
db.orders.aggregate([
  {
    $group: {
      _id: null,
      totalAmount: { $sum: "$amount" }
    }
  }
]);
```

### $avg

Calculates the average amount spent per order.

```javascript
db.orders.aggregate([
  {
    $group: {
      _id: null,
      averageAmount: { $avg: "$amount" }
    }
  }
]);
```

### $min

Finds the minimum amount spent on an order.

```javascript
db.orders.aggregate([
  {
    $group: {
      _id: null,
      minAmount: { $min: "$amount" }
    }
  }
]);
```

### $max

Finds the maximum amount spent on an order.

```javascript
db.orders.aggregate([
  {
    $group: {
      _id: null,
      maxAmount: { $max: "$amount" }
    }
  }
]);
```

### $first

Gets the first order placed (by date).

```javascript
db.orders.aggregate([
  { $sort: { "date": 1 } },
  {
    $group: {
      _id: null,
      firstOrder: { $first: "$$ROOT" }
    }
  }
]);
```

### $last

Gets the last order placed (by date).

```javascript
db.orders.aggregate([
  { $sort: { "date": -1 } },
  {
    $group: {
      _id: null,
      lastOrder: { $last: "$$ROOT" }
    }
  }
]);
```
```

This README file now provides a comprehensive guide to MongoDB aggregation pipelines, covering basic CRUD operations, all major aggregation stages, and operations, and includes example datasets and resources for further learning.
