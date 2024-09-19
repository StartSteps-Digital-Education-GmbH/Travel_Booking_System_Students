# Comprehensive Tutorial: Building Microservices with Data Validation, Error Handling, and MongoDB Integration

## Introduction
Welcome to this tutorial! In this guide, we will explore how to build a travel booking system using microservices. We will cover essential concepts such as data validation, error handling, and how to use MongoDB for data storage. By the end of this tutorial, you will have a solid understanding of these topics and how they work together in a real-world application.

## Table of Contents
1. [What are Microservices?](#what-are-microservices)
2. [Setting Up Your Project](#setting-up-your-project)
3. [Data Validation](#data-validation)
4. [Understanding Regular Expressions (Regex)](#understanding-regular-expressions-regex)
5. [Error Handling in Express](#error-handling-in-express)
6. [Introduction to MongoDB](#introduction-to-mongodb)
7. [Using Mongoose](#using-mongoose)
8. [Building the User Service](#building-the-user-service)
9. [Building the Flight Service](#building-the-flight-service)
10. [Testing Your Services](#testing-your-services)
11. [Conclusion](#conclusion)

---

## What are Microservices?
Microservices are a way to design software applications as a collection of small, independent services. Each service focuses on a specific function and can be developed, deployed, and scaled separately. 

### Example:
Imagine a travel booking platform. Instead of having one large application that handles everything (like user accounts, flight bookings, and hotel reservations), you can create separate services:
- **User Service**: Manages user accounts.
- **Flight Service**: Handles flight searches and bookings.
- **Hotel Service**: Manages hotel reservations.

This approach makes it easier to update and maintain each part of the application without affecting the others.

---

## Setting Up Your Project
To get started, we need to set up our project environment.

1. **Install Node.js**: Make sure you have Node.js installed on your computer. You can download it from [Node.js official website](https://nodejs.org/).

2. **Create a New Project Directory**:
   - Open your terminal and run:
     ```bash
     mkdir travel-booking-system
     cd travel-booking-system
     ```

3. **Initialize a Node.js Project**:
   - This command creates a `package.json` file, which keeps track of your project’s dependencies.
   ```bash
   npm init -y
   ```

4. **Install Required Packages**:
   - We will need Express (for building our API), Mongoose (for MongoDB interaction), and validation libraries.
   ```bash
   npm install express mongoose joi express-validator
   ```

5. **Set Up TypeScript**:
   - TypeScript helps us write safer code by adding types. Initialize TypeScript configuration:
   ```bash
   npx tsc --init
   ```

---

## Data Validation
Data validation ensures that the data we receive is correct and safe. This is crucial for preventing errors and security issues.

### Using Joi
Joi is a popular library for validating data in JavaScript applications.

#### Example:
```javascript
import Joi from 'joi';

// Define a schema for user data
const schema = Joi.object({
  name: Joi.string().required(), // Name is required
  email: Joi.string().email().required(), // Email must be valid
});

// Validate data
const { error } = schema.validate(req.body);
if (error) {
  return res.status(400).send(error.details[0].message); // Respond with error message
}
```

### Using express-validator
Another option is express-validator, which integrates well with Express.

#### Example:
```javascript
import { body, validationResult } from 'express-validator';

// Validation middleware
router.post(
  '/',
  [
    body('name').isString().notEmpty().withMessage('Name is required'),
    body('email').isEmail().withMessage('Email is not valid'),
  ],
  (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() }); // Respond with errors
    }
    // Proceed with creating the user
  }
);
```

---

## Understanding Regular Expressions (Regex)
Regular expressions (regex) are patterns used to match character combinations in strings. They are useful for validating formats like email addresses and passwords.

### Basic Concepts:
- **Character Classes**: `[abc]` matches any character a, b, or c.
- **Quantifiers**: 
  - `*` means zero or more times.
  - `+` means one or more times.
  - `?` means zero or one time.
- **Anchors**: 
  - `^` indicates the start of a string.
  - `$` indicates the end of a string.

### Example for Email Validation:
```javascript
const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/; // Basic email regex
```

### Example for Password Validation:
```javascript
const passwordRegex = /^(?=.*[A-Z])(?=.*[0-9])(?=.*[!@#$&*]).{6,}$/; // At least one uppercase, one number, one special character, and minimum 6 characters
```

---

## Error Handling in Express
Error handling is essential for providing a good user experience. In Express, you can handle errors that occur during request processing.

### Synchronous Error Handling:
You can use middleware to catch errors that occur during request processing.

### Asynchronous Error Handling:
For asynchronous operations, you can use `try-catch` blocks or pass errors to the next middleware.

### Example:
```javascript
app.use((err, req, res, next) => {
  console.error(err.stack); // Log the error stack
  res.status(err.status || 500).send({ message: err.message || 'Something went wrong!' }); // Respond with error message
});
```

---

## Introduction to MongoDB
MongoDB is a NoSQL database that stores data in flexible, JSON-like documents. This means you can store data in a way that is more natural for your application.

### Key Features:
- **Document-Oriented**: Data is stored in documents, allowing for complex data structures.
- **Scalability**: Easily scales horizontally by sharding data across multiple servers.

### Basic Commands:
- **Insert Data**: `db.collection.insertOne({})`
- **Query Data**: `db.collection.find({})`

### Example:
To create a database and a collection, you can use the MongoDB shell:
```javascript
use travelBooking; // Create or switch to the travelBooking database
db.createCollection('users'); // Create a users collection
```

---

## Using Mongoose
Mongoose is an Object Data Modeling (ODM) library for MongoDB and Node.js. It provides a schema-based solution to model application data, making it easier to work with MongoDB.

### Key Features:
- **Schema Definition**: Define the structure of your documents.
- **Validation**: Built-in validation for data integrity.
- **Middleware**: Pre and post hooks for document operations.

### Example:
1. **Installation**:
   ```bash
   npm install mongoose
   ```

2. **Define a Model**:
   ```javascript
   import mongoose, { Schema } from 'mongoose';

   const userSchema = new Schema({
     name: { type: String, required: true },
     email: { type: String, required: true, unique: true },
   });

   const User = mongoose.model('User', userSchema);
   ```

3. **Using the Model**:
   ```javascript
   const newUser = new User({ name: 'Alice', email: 'alice@example.com' });
   await newUser.save(); // Save to MongoDB
   ```

---

## Building the User Service
Now that we have covered the theory, let’s build the User Service using the concepts we’ve learned.

1. **Create the User Service Structure**:
   - Create a directory for the user service and necessary files.

2. **Implement the User Service**:
   - Use Express to create routes for user management.
   - Implement data validation using Joi or express-validator.
   - Connect to MongoDB using Mongoose.

---

## Building the Flight Service
Similarly, we will build the Flight Service.

1. **Create the Flight Service Structure**:
   - Create a directory for the flight service and necessary files.

2. **Implement the Flight Service**:
   - Use Express to create routes for flight management.
   - Implement data validation for flight data.
   - Connect to MongoDB using Mongoose.

---

## Testing Your Services
Once both services are built, you can test them using Postman or any API testing tool. 

### Example Endpoints:
- **User Service**:
  - Create User: `POST http://localhost:3001/users`
  - Get All Users: `GET http://localhost:3001/users`

- **Flight Service**:
  - Create Flight: `POST http://localhost:3002/flights`
  - Get All Flights: `GET http://localhost:3002/flights`

---

## Conclusion
In this tutorial, we explored the essential concepts of building a travel booking system using microservices. We covered data validation, error handling, and how to use MongoDB with Mongoose. These concepts are foundational for developing robust and scalable applications. As you continue your learning journey, you will gain deeper insights into each topic, enabling you to build more complex systems.
