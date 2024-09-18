# Travel Booking System

## Overview

In this exercise, we will create two microservices:
1. **User Service**: Manages user accounts and preferences.
2. **Flight Service**: Handles flight search, booking, and cancellations.

Microservices architecture allows us to build applications as a collection of small, independent services that communicate over a network. This modular approach enhances flexibility, scalability, and maintainability.

## Step 1: Set Up the Project

### 1. Open Your Terminal
Before starting, ensure that you have Node.js and npm (Node Package Manager) installed. Node.js is a JavaScript runtime that allows you to run JavaScript on the server side, while npm is used to manage packages (libraries) for Node.js applications.

### 2. Create a New Directory for the Project
Creating a new directory helps organize your project files. By naming it `travel-booking-system`, we clearly define the purpose of this project.

```bash
mkdir travel-booking-system
cd travel-booking-system
```

### 3. Initialize a Node.js Project
Running `npm init -y` initializes a new Node.js project and creates a `package.json` file. This file contains metadata about the project, including its name, version, and dependencies. The `-y` flag automatically accepts the default settings.

```bash
npm init -y
```

### 4. Edit `package.json`
Adding `"type": "module"` allows us to use ES module syntax (import/export) in our JavaScript files. This is important for modern JavaScript development.

The `scripts` section is also updated to include commands for building and running the application:
- **build**: Compiles TypeScript files into JavaScript.
- **dev**: Runs the application using `ts-node`, which allows us to run TypeScript files directly.

```json
{
  "name": "travel-booking-system",
  "version": "1.0.0",
  "description": "A travel booking system with microservices",
  "main": "index.js",
  "type": "module",
  "scripts": {
    "build": "tsc",
    "dev": "ts-node src/user-service/server.ts"
  },
  ...
}
```

### 5. Install Required Packages
We install the necessary packages for our project:
- **express**: A web framework for Node.js that simplifies building web applications and APIs.
- **typescript**: A superset of JavaScript that adds static types, making it easier to catch errors during development.
- **@types/node** and **@types/express**: Type definitions for Node.js and Express, allowing TypeScript to understand the types used in these libraries.
- **ts-node**: A TypeScript execution environment for Node.js, enabling us to run TypeScript files directly.

```bash
npm install express
npm install --save-dev typescript @types/node @types/express ts-node
```

### 6. Create TypeScript Configuration
The command `npx tsc --init` generates a `tsconfig.json` file, which is used to configure TypeScript options for the project. This file specifies how TypeScript should compile the code.

```bash
npx tsc --init
```

## Step 2: Configure TypeScript

### 1. Edit `tsconfig.json`
In the `tsconfig.json` file, we configure various options:
- **target**: Specifies the ECMAScript version to compile to (e.g., ES2020).
- **module**: Defines the module system to use (NodeNext for modern Node.js).
- **rootDir**: The root directory of our TypeScript source files.
- **outDir**: The directory where the compiled JavaScript files will be placed.
- **strict**: Enables strict type-checking options, which helps catch errors early.
- **esModuleInterop**: Allows for better interoperability between CommonJS and ES Modules.

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "NodeNext",
    "rootDir": "./src",
    "outDir": "./build",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "noImplicitAny": true,
    "moduleResolution": "node",
    "resolveJsonModule": true
  },
  ...
}
```

## Step 3: Create Directory Structure

### 1. Create Source Directory
Creating a `src` directory helps organize our source code files. This is a common practice in software development to keep the project structure clean.

```bash
mkdir src
```

### 2. Create Service Directories
Inside the `src` directory, we create separate directories for each microservice. This modular approach allows us to manage each service independently.

```bash
mkdir src/user-service src/flight-service
```

### 3. Create Files for Each Service
We create the necessary files for each service. This includes the main server file and route handling files.

```bash
touch src/user-service/userRoutes.ts src/user-service/server.ts
touch src/flight-service/flightRoutes.ts src/flight-service/server.ts
```

## Step 4: Build the User Service

### 1. Edit `src/user-service/server.ts`
In this file, we set up the Express server for the User Service. We import the necessary modules and define the server's behavior.

- **Express**: We create an instance of the Express application.
- **Middleware**: We use `express.json()` to parse incoming JSON requests.
- **Routing**: We define the `/users` endpoint to handle user-related requests.

```typescript
import express from 'express';
import userRoutes from './userRoutes'; // Import user routes

const app = express();
const PORT = process.env.PORT || 3001; // User service runs on port 3001

app.use(express.json()); // Middleware to parse JSON bodies

// Use user routes for handling '/users' endpoint
app.use('/users', userRoutes);

app.listen(PORT, () => {
    console.log(`User Service is running on port ${PORT}`);
});
```

### 2. Edit `src/user-service/userRoutes.ts`
In this file, we define the routes for managing users. Each route corresponds to a specific operation (create, read, update, delete).

- **User Interface**: We define a TypeScript interface to structure user data.
- **Mock Database**: We use an in-memory array to store user data temporarily.
- **Routes**: We implement routes for creating, retrieving, updating, and deleting users.

```typescript
import express from 'express';

const router = express.Router();

// User interface to define the structure of a user
interface User {
    id: number;
    name: string;
    email: string;
}

// Mock database (in-memory storage)
let users: User[] = [];

// Route to create a new user
router.post('/', (req, res) => {
    const { name, email } = req.body;
    const newUser: User = {
        id: users.length + 1, // Simple ID generation
        name,
        email,
    };
    users.push(newUser); // Add user to the mock database
    res.status(201).send(newUser); // Respond with the created user
});

// Route to get all users
router.get('/', (req, res) => {
    res.status(200).send(users); // Respond with the list of users
});

// Route to get a user by ID
router.get('/:id', (req, res) => {
    const userId = parseInt(req.params.id);
    const user = users.find(u => u.id === userId);
    if (!user) {
        return res.status(404).send({ message: 'User not found' });
    }
    res.status(200).send(user); // Respond with the found user
});

// Route to update a user
router.put('/:id', (req, res) => {
    const userId = parseInt(req.params.id);
    const userIndex = users.findIndex(u => u.id === userId);
    if (userIndex === -1) {
        return res.status(404).send({ message: 'User not found' });
    }
    const { name, email } = req.body;
    users[userIndex] = { id: userId, name, email }; // Update user
    res.status(200).send(users[userIndex]); // Respond with the updated user
});

// Route to delete a user
router.delete('/:id', (req, res) => {
    const userId = parseInt(req.params.id);
    users = users.filter(u => u.id !== userId); // Remove user
    res.status(204).send(); // Respond with no content
});

export default router;
```

## Step 5: Build the Flight Service

### 1. Edit `src/flight-service/server.ts`
Similar to the User Service, we set up the Express server for the Flight Service. This service will handle flight-related requests.

```typescript
import express from 'express';
import flightRoutes from './flightRoutes'; // Import flight routes

const app = express();
const PORT = process.env.PORT || 3002; // Flight service runs on port 3002

app.use(express.json()); // Middleware to parse JSON bodies

// Use flight routes for handling '/flights' endpoint
app.use('/flights', flightRoutes);

app.listen(PORT, () => {
    console.log(`Flight Service is running on port ${PORT}`);
});
```

### 2. Edit `src/flight-service/flightRoutes.ts`
In this file, we define the routes for managing flights. Each route corresponds to a specific operation (create, read, delete).

- **Flight Interface**: We define a TypeScript interface to structure flight data.
- **Mock Database**: We use an in-memory array to store flight data temporarily.
- **Routes**: We implement routes for creating, retrieving, and deleting flights.

```typescript
import express from 'express';

const router = express.Router();

// Flight interface to define the structure of a flight
interface Flight {
    id: number;
    origin: string;
    destination: string;
    price: number;
}

// Mock database (in-memory storage)
let flights: Flight[] = [];

// Route to create a new flight
router.post('/', (req, res) => {
    const { origin, destination, price } = req.body;
    const newFlight: Flight = {
        id: flights.length + 1, // Simple ID generation
        origin,
        destination,
        price,
    };
    flights.push(newFlight); // Add flight to the mock database
    res.status(201).send(newFlight); // Respond with the created flight
});

// Route to get all flights
router.get('/', (req, res) => {
    res.status(200).send(flights); // Respond with the list of flights
});

// Route to get a flight by ID
router.get('/:id', (req, res) => {
    const flightId = parseInt(req.params.id);
    const flight = flights.find(f => f.id === flightId);
    if (!flight) {
        return res.status(404).send({ message: 'Flight not found' });
    }
    res.status(200).send(flight); // Respond with the found flight
});

// Route to delete a flight
router.delete('/:id', (req, res) => {
    const flightId = parseInt(req.params.id);
    flights = flights.filter(f => f.id !== flightId); // Remove flight
    res.status(204).send(); // Respond with no content
});

export default router;
```

## Step 6: Build and Run the Application

### 1. Compile TypeScript Files
Run the following command to compile the TypeScript files into JavaScript:
```bash
npx tsc
```

### 2. Start the User Service
Open a new terminal window and run:
```bash
node build/user-service/server.js
```

### 3. Start the Flight Service
In another terminal window, run:
```bash
node build/flight-service/server.js
```

## Step 7: Test the Endpoints

Use Postman or any API testing tool to test the following endpoints for both services.

### User Service Endpoints

- **Create User**:
  - **Method**: POST
  - **URL**: `http://localhost:3001/users`
  - **Body** (JSON):
    ```json
    {
        "name": "Alice Johnson",
        "email": "alice@example.com"
    }
    ```

- **Get All Users**:
  - **Method**: GET
  - **URL**: `http://localhost:3001/users`

- **Get User by ID**:
  - **Method**: GET
  - **URL**: `http://localhost:3001/users/1`

- **Update User**:
  - **Method**: PUT
  - **URL**: `http://localhost:3001/users/1`
  - **Body** (JSON):
    ```json
    {
        "name": "Alice Smith",
        "email": "alice.smith@example.com"
    }
    ```

- **Delete User**:
  - **Method**: DELETE
  - **URL**: `http://localhost:3001/users/1`

### Flight Service Endpoints

- **Create Flight**:
  - **Method**: POST
  - **URL**: `http://localhost:3002/flights`
  - **Body** (JSON):
    ```json
    {
        "origin": "New York",
        "destination": "Los Angeles",
        "price": 300
    }
    ```

- **Get All Flights**:
  - **Method**: GET
  - **URL**: `http://localhost:3002/flights`

- **Get Flight by ID**:
  - **Method**: GET
  - **URL**: `http://localhost:3002/flights/1`

- **Delete Flight**:
  - **Method**: DELETE
  - **URL**: `http://localhost:3002/flights/1`

## Step 8: Discuss Microservices Architecture

- **Independent Functionality**: Each microservice (User Service and Flight Service) handles specific functionalities. This allows for independent development, deployment, and scaling.

- **Communication**: Microservices communicate over HTTP, allowing them to be part of a larger system where different services can interact with each other.

- **Scalability**: If the user management service needs to handle more requests, it can be scaled independently of the flight service.

- **Real-World Application**: In a real-world application, this travel booking system could be part of a larger platform where different services handle user management, flight booking, hotel reservations, etc. Each service can be developed and maintained by different teams, allowing for faster development cycles and better resource allocation.

## Conclusion

This live coding exercise provides a comprehensive overview of building a travel booking system with microservices. By following these steps, students can understand how microservices work, how to implement them, and how they can be applied in real-world scenarios. Encourage students to experiment with the code, modify it, and think about how they would integrate these microservices into a larger application.
