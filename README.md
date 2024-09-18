# Travel Booking System: Student Version

## Overview

In this exercise, we will create two microservices:
1. **User Service**: Manages user accounts and preferences.
2. **Flight Service**: Handles flight search, booking, and cancellations.

### Table of Contents

1. [Step 1: Set Up the Project](#step-1-set-up-the-project)
2. [Step 2: Configure TypeScript](#step-2-configure-typescript)
3. [Step 3: Create Directory Structure](#step-3-create-directory-structure)
4. [Step 4: Build the User Service](#step-4-build-the-user-service)
5. [Step 5: Build the Flight Service](#step-5-build-the-flight-service)
6. [Step 6: Build and Run the Application](#step-6-build-and-run-the-application)
7. [Step 7: Test the Endpoints](#step-7-test-the-endpoints)
8. [Step 8: Discuss Microservices Architecture](#step-8-discuss-microservices-architecture)
9. [Conclusion](#conclusion)

## Step 1: Set Up the Project

1. **Open Your Terminal**:
   Ensure you have Node.js and npm installed. Check with:
   ```bash
   node -v
   npm -v
   ```

2. **Create a New Directory for the Project**:
   ```bash
   mkdir travel-booking-system
   cd travel-booking-system
   ```

3. **Initialize a Node.js Project**:
   This command creates a `package.json` file with default values.
   ```bash
   npm init -y
   ```

4. **Edit `package.json`**:
   Open `package.json` and add `"type": "module"` to enable ES module syntax. Your `package.json` should look like this:
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
     "dependencies": {
       "express": "^4.17.1"
     },
     "devDependencies": {
       "typescript": "^5.0.0",
       "@types/node": "^18.0.0",
       "@types/express": "^4.17.13",
       "ts-node": "^10.0.0"
     }
   }
   ```

5. **Install Required Packages**:
   Install Express and TypeScript along with type definitions.
   ```bash
   npm install express
   npm install --save-dev typescript @types/node @types/express ts-node
   ```

6. **Create TypeScript Configuration**:
   Generate a `tsconfig.json` file for TypeScript configuration.
   ```bash
   npx tsc --init
   ```

## Step 2: Configure TypeScript

1. **Edit `tsconfig.json`**:
   Open `tsconfig.json` and ensure it looks like this:
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
     "include": [
       "src/**/*"
     ],
     "exclude": [
       "node_modules",
       "**/*.spec.ts"
     ]
   }
   ```

## Step 3: Create Directory Structure

1. **Create Source Directory**:
   ```bash
   mkdir src
   ```

2. **Create Service Directories**:
   Inside the `src` directory, create directories for each service:
   ```bash
   mkdir src/user-service src/flight-service
   ```

3. **Create Files for Each Service**:
   ```bash
   touch src/user-service/userRoutes.ts src/user-service/server.ts
   touch src/flight-service/flightRoutes.ts src/flight-service/server.ts
   ```

## Step 4: Build the User Service

1. **Edit `src/user-service/server.ts`**:
   Open `src/user-service/server.ts` and add the following code:
   ```typescript
   // src/user-service/server.ts
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

2. **Edit `src/user-service/userRoutes.ts`**:
   Open `src/user-service/userRoutes.ts` and add the following code:
   ```typescript
   // src/user-service/userRoutes.ts
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

1. **Edit `src/flight-service/server.ts`**:
   Open `src/flight-service/server.ts` and add the following code:
   ```typescript
   // src/flight-service/server.ts
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

2. **Edit `src/flight-service/flightRoutes.ts`**:
   Open `src/flight-service/flightRoutes.ts` and add the following code:
   ```typescript
   // src/flight-service/flightRoutes.ts
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

1. **Compile TypeScript Files**:
   Run the following command to compile the TypeScript files:
   ```bash
   npx tsc
   ```

2. **Start the User Service**:
   Open a new terminal window and run:
   ```bash
   node build/user-service/server.js
   ```

3. **Start the Flight Service**:
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
