Let's perform all the CRUD operations (Create, Read, Update, Delete) on the given dataset in MongoDB.

### Setup
First, import the dataset into MongoDB. Save the dataset in a file named `orders.json` and import it into a MongoDB collection called `orders`.

### Import Dataset

```bash
mongoimport --db testdb --collection orders --file orders.json --jsonArray
```

Assuming you have a MongoDB database named `testdb` and a collection named `orders`.

### CRUD Operations

#### 1. Create

Inserting a new document into the `orders` collection.

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

#### 2. Read

Reading documents from the `orders` collection.

- **Find all documents**

```javascript
db.orders.find().pretty();
```

- **Find documents with status "A"**

```javascript
db.orders.find({ "status": "A" }).pretty();
```

- **Find a specific order by `order_id`**

```javascript
db.orders.findOne({ "order_id": 3 });
```

#### 3. Update

Updating documents in the `orders` collection.

- **Update a specific document**

```javascript
db.orders.updateOne(
  { "order_id": 2 },
  {
    $set: { "status": "C", "amount": 500 },
    $currentDate: { "lastModified": true }
  }
);
```

- **Update multiple documents**

```javascript
db.orders.updateMany(
  { "status": "A" },
  { $set: { "status": "B" } }
);
```

#### 4. Delete

Deleting documents from the `orders` collection.

- **Delete a specific document**

```javascript
db.orders.deleteOne({ "order_id": 1 });
```

- **Delete multiple documents**

```javascript
db.orders.deleteMany({ "status": "C" });
```

### Aggregation Operations

Here are some basic examples of aggregation operations on the `orders` collection.

#### 1. `$match`
Filter orders with status "A".

```javascript
db.orders.aggregate([
  { $match: { "status": "A" } }
]);
```

#### 2. `$group`
Group orders by customer ID and calculate the total amount spent by each customer.

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

#### 3. `$project`
Project only the `order_id` and the `items` array.

```javascript
db.orders.aggregate([
  { $project: { "order_id": 1, "items": 1, "_id": 0 } }
]);
```

#### 4. `$sort`
Sort orders by amount in descending order.

```javascript
db.orders.aggregate([
  { $sort: { "amount": -1 } }
]);
```

#### 5. `$lookup`
Perform a join with another collection (assuming another collection named `orderDetails` exists).

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

### Connecting with Node.js

Here's an example of how to connect to the MongoDB server and perform these operations using Node.js.

```javascript
const { MongoClient } = require('mongodb');

async function main() {
  const uri = "mongodb://localhost:27017/testdb";
  const client = new MongoClient(uri, { useNewUrlParser: true, useUnifiedTopology: true });

  try {
    await client.connect();
    const database = client.db('testdb');
    const orders = database.collection('orders');

    // Create
    await orders.insertOne({
      "order_id": 26,
      "cust_id": 1006,
      "status": "A",
      "amount": 275,
      "items": ["apple", "banana"],
      "date": "2023-01-26"
    });

    // Read
    const allOrders = await orders.find().toArray();
    console.log('All Orders:', allOrders);

    const specificOrder = await orders.findOne({ "order_id": 3 });
    console.log('Specific Order:', specificOrder);

    // Update
    await orders.updateOne(
      { "order_id": 2 },
      {
        $set: { "status": "C", "amount": 500 },
        $currentDate: { "lastModified": true }
      }
    );

    await orders.updateMany(
      { "status": "A" },
      { $set: { "status": "B" } }
    );

    // Delete
    await orders.deleteOne({ "order_id": 1 });
    await orders.deleteMany({ "status": "C" });

    // Aggregation
    const aggregatedOrders = await orders.aggregate([
      { $match: { "status": "B" } },
      {
        $group: {
          _id: "$cust_id",
          totalSpent: { $sum: "$amount" }
        }
      },
      { $sort: { "totalSpent": -1 } }
    ]).toArray();
    console.log('Aggregated Orders:', aggregatedOrders);

  } finally {
    await client.close();
  }
}

main().catch(console.error);
```

This Node.js script connects to the MongoDB server, performs all the CRUD operations, and demonstrates a basic aggregation operation. Make sure you have the MongoDB Node.js driver installed:

```bash
npm install mongodb
```

You can run the script with:

```bash
node script.js
```

Replace `script.js` with the filename where you saved the script.
