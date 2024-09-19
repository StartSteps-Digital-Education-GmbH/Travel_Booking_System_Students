### Student Version: Travel Booking System with Enhanced Concepts

#### Overview
In this exercise, we will create a **Travel Booking System** consisting of two microservices:
- **User Service**: Manages user accounts and preferences.
- **Flight Service**: Handles flight search, booking, and cancellations.

### New Concepts Introduced
1. **Microservices Architecture**: Understanding how to build independent services that communicate over HTTP.
2. **Data Validation**: Using libraries for validating user input.
3. **Regular Expressions (Regex)**: Understanding how regex can be used for validating email and password formats.
4. **Error Handling**: Implementing error handling in Express for both synchronous and asynchronous operations.
5. **MongoDB**: Introduction to MongoDB as a NoSQL database.
6. **Mongoose**: Using Mongoose as an Object Data Modeling (ODM) library for MongoDB.

### Benefits
- **Scalability**: Each service can be scaled independently based on demand.
- **Maintainability**: Smaller codebases are easier to manage and update.
- **Data Validation**: Ensures that only valid data is processed, improving application reliability.

### Challenges
- **Complexity**: Managing multiple services can introduce complexity in deployment and communication.
- **Data Consistency**: Ensuring data consistency across services can be challenging.

### Installing MongoDB and Using It from localhost

To set up MongoDB on your local machine, follow these steps:

#### Step 1: Download MongoDB

1. **Visit the MongoDB Download Center**:
   - Go to the [MongoDB Download Center](https://www.mongodb.com/try/download/community).

2. **Select Your Version**:
   - Choose the latest version of MongoDB Community Server.

3. **Choose Your Operating System**:
   - Select your operating system (Windows, macOS, or Linux).

4. **Download the Installer**:
   - Click on the download link to get the installer for your OS.

#### Step 2: Install MongoDB

1. **Run the Installer**:
   - Open the downloaded installer and follow the installation prompts.

2. **Choose Setup Type**:
   - Select "Complete" for a full installation.

3. **Configure MongoDB**:
   - During installation, you may be prompted to configure MongoDB as a service. This allows MongoDB to start automatically when your computer starts. You can choose to enable this option.

4. **Finish Installation**:
   - Complete the installation process.

#### Step 3: Set Up MongoDB Data Directory

1. **Create Data Directory**:
   - MongoDB requires a data directory to store its data. By default, it uses `C:\data\db` on Windows or `/data/db` on macOS/Linux.
   - Create the directory using the following commands:
     - **Windows**:
       ```bash
       mkdir C:\data\db
       ```
     - **macOS/Linux**:
       ```bash
       sudo mkdir -p /data/db
       ```

2. **Set Permissions (Linux/macOS)**:
   - Ensure that the MongoDB process has permission to write to the data directory:
     ```bash
     sudo chown -R `id -un` /data/db
     ```

#### Step 4: Start MongoDB

1. **Open a Terminal or Command Prompt**:
   - Depending on your OS, open the appropriate command line interface.

2. **Start MongoDB**:
   - Run the following command to start the MongoDB server:
     ```bash
     mongod
     ```
   - This command starts the MongoDB server and listens for connections on the default port (27017).

3. **Verify MongoDB is Running**:
   - You should see logs indicating that MongoDB is running. Look for a message like:
     ```
     [initandlisten] waiting for connections on port 27017
     ```

#### Step 5: Connect to MongoDB

1. **Open Another Terminal or Command Prompt**:
   - Open a new terminal window while keeping the MongoDB server running.

2. **Connect to MongoDB**:
   - Use the MongoDB shell to connect to the server:
     ```bash
     mongo
     ```
   - This command opens the MongoDB shell, allowing you to interact with the database.

3. **Verify Connection**:
   - Once connected, you should see a prompt like:
     ```
     MongoDB shell version vX.X.X
     connecting to: mongodb://127.0.0.1:27017
     ```

#### Step 6: Basic MongoDB Commands

1. **Show Databases**:
   - To see the databases available, run:
     ```javascript
     show dbs
     ```

2. **Create a Database**:
   - Switch to a new database (it will be created when you insert data):
     ```javascript
     use travelBooking
     ```

3. **Create a Collection**:
   - Create a collection (similar to a table in SQL):
     ```javascript
     db.createCollection('users')
     ```

4. **Insert Data**:
   - Insert a document into the collection:
     ```javascript
     db.users.insertOne({ name: "Alice Johnson", email: "alice@example.com" })
     ```

5. **Query Data**:
   - Retrieve documents from the collection:
     ```javascript
     db.users.find()
     ```

### Step-by-Step Guidance

#### Step 1: Set Up the Project

1. **Open Your Terminal**: Ensure you have Node.js and npm installed. Check with:
   ```bash
   node -v
   npm -v
   ```

2. **Create a New Directory for the Project**:
   ```bash
   mkdir travel-booking-system
   cd travel-booking-system
   ```

3. **Initialize a Node.js Project**: This command creates a `package.json` file with default values.
   ```bash
   npm init -y
   ```

4. **Install Required Packages**: Install Express, Mongoose, Joi, express-validator, and TypeScript.
   ```bash
   npm install express mongoose joi express-validator
   npm install --save-dev typescript @types/node @types/express ts-node
   ```

5. **Create TypeScript Configuration**: Generate a `tsconfig.json` file for TypeScript configuration.
   ```bash
   npx tsc --init
   ```

#### Step 2: Configure TypeScript

1. **Edit `tsconfig.json`**: Open `tsconfig.json` and ensure it looks like this:
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
     "include": ["src/**/*"],
     "exclude": ["node_modules", "**/*.spec.ts"]
   }
   ```

#### Step 3: Create Directory Structure

1. **Create Source Directory**:
   ```bash
   mkdir src
   ```

2. **Create Service Directories**: Inside the `src` directory, create directories for each service:
   ```bash
   mkdir src/user-service src/flight-service
   ```

3. **Create Files for Each Service**:
   ```bash
   touch src/user-service/userRoutes.ts src/user-service/server.ts
   touch src/flight-service/flightRoutes.ts src/flight-service/server.ts
   ```

#### Step 4: Build the User Service with MongoDB

1. **Edit `src/user-service/server.ts`**: Open `src/user-service/server.ts` and replace the content with the following code. This code sets up the Express server and connects to MongoDB.

   ```typescript
   // src/user-service/server.ts
    // Import Express framework
    // Import Mongoose for MongoDB interaction
    // Import user routes

    // Create an Express application
    // Set the port for the user service

   // Middleware to parse JSON bodies

   // Connect to MongoDB
   mongoose.connect('mongodb://localhost:27017/travel-booking', {
     useNewUrlParser: true,
     useUnifiedTopology: true,
   })
   .then(() => console.log('Connected to MongoDB')) // Log success message
   .catch(err => console.error('Could not connect to MongoDB:', err)); // Log error message

   // Use user routes for handling '/users' endpoint
   

   app.listen(PORT, () => {
     console.log(`User Service is running on port ${PORT}`); // Log the running port
   });
   ```

2. **Edit `src/user-service/userRoutes.ts`**: Open `src/user-service/userRoutes.ts` and replace the content with the following code. This code defines the routes for user management.

   ```typescript
   // src/user-service/userRoutes.ts
    // Import Express framework
    // Import express-validator for input validation
    // Import User model

    // Create a new router instance

   // Route to create a new user
   router.post(
     '/',
     [
        // Validate name
        // Validate email
     ],
     async (req, res) => {
        // Check for validation errors
       if (!errors.isEmpty()) {
         // Respond with errors
       }

        // Destructure name and email from request body
        // Create a new user instance
       // Save user to MongoDB
       // Respond with the created user
     }
   );

   // Route to get all users
   router.get('/', async (req, res) => {
      // Retrieve all users from MongoDB
      // Respond with the list of users
   });

   // Route to get a user by ID
   router.get('/:id', async (req, res) => {
      // Get user ID from request parameters
      // Find user by ID
     if (!user) {
        // Respond with not found message
     }
      // Respond with the found user
   });

   // Route to update a user
   router.put('/:id', async (req, res) => {
      // Get user ID from request parameters
     // Update user
     if (!user) {
        // Respond with not found message
     }
      // Respond with the updated user
   });

   // Route to delete a user
   router.delete('/:id', async (req, res) => {
      // Get user ID from request parameters
      // Remove user
     if (!user) {
       // Respond with not found message
     }
      // Respond with no content
   });

    // Export the router
   ```

3. **Create User Model**: Create a new file `src/user-service/userModel.ts` and add the following code. This code defines the User model for MongoDB.

   ```typescript
   // src/user-service/userModel.ts
    // Import Mongoose and types

   // User interface to define the structure of a user
   interface IUser extends Document {
      // User's name
      // User's email
   }

   // User schema definition
   const userSchema: Schema = new Schema({
     // Name is required
      // Email is required and must be unique
   });

   // User model
    // Create User model

    // Export User model
   ```

#### Step 5: Build the Flight Service with MongoDB

1. **Edit `src/flight-service/server.ts`**: Open `src/flight-service/server.ts` and replace the content with the following code. This code sets up the Express server and connects to MongoDB.

   ```typescript
   // src/flight-service/server.ts
    // Import Express framework
   // Import Mongoose for MongoDB interaction
    // Import flight routes

    // Create an Express application
    // Set the port for the flight service

    // Middleware to parse JSON bodies

   // Connect to MongoDB
   mongoose.connect('mongodb://localhost:27017/travel-booking', {
     useNewUrlParser: true,
     useUnifiedTopology: true,
   })
   .then(() => console.log('Connected to MongoDB')) // Log success message
   .catch(err => console.error('Could not connect to MongoDB:', err)); // Log error message

   // Use flight routes for handling '/flights' endpoint
   app.use('/flights', flightRoutes);

   app.listen(PORT, () => {
      // Log the running port
   });
   ```

2. **Edit `src/flight-service/flightRoutes.ts`**: Open `src/flight-service/flightRoutes.ts` and replace the content with the following code. This code defines the routes for flight management.

   ```typescript
   // src/flight-service/flightRoutes.ts
    // Import Express framework
    // Import express-validator for input validation
    // Import Flight model

    // Create a new router instance

   // Route to create a new flight
   router.post(
     '/',
     [
        // Validate origin
        // Validate destination
        // Validate price
     ],
     async (req, res) => {
        // Check for validation errors
       if (!errors.isEmpty()) {
          // Respond with errors
       }

        // Destructure origin, destination, and price from request body
        // Create a new flight instance
        // Save flight to MongoDB
       // Respond with the created flight
     }
   );

   // Route to get all flights
   router.get('/', async (req, res) => {
      // Retrieve all flights from MongoDB
      // Respond with the list of flights
   });

   // Route to get a flight by ID
   router.get('/:id', async (req, res) => {
      // Get flight ID from request parameters
      // Find flight by ID
     if (!flight) {
        // Respond with not found message
     }
      // Respond with the found flight
   });

   // Route to delete a flight
   router.delete('/:id', async (req, res) => {
      // Get flight ID from request parameters
      // Remove flight
     if (!flight) {
        // Respond with not found message
     }
      // Respond with no content
   });

    // Export the router
   ```

3. **Create Flight Model**: Create a new file `src/flight-service/flightModel.ts` and add the following code. This code defines the Flight model for MongoDB.

   ```typescript
   // src/flight-service/flightModel.ts
    // Import Mongoose and types

   // Flight interface to define the structure of a flight
   interface IFlight extends Document {
      // Flight's origin
      // Flight's destination
      // Flight's price
   }

   // Flight schema definition
   const flightSchema: Schema = new Schema({
      // Origin is required
      // Destination is required
      // Price is required
   });

   // Flight model
    // Create Flight model

    // Export Flight model
   ```

#### Step 6: Build Error Handling

1. **Error Handling in Express**: Modify `src/user-service/server.ts` to include error handling middleware. Add the following code at the end of the file, just before the closing bracket of the `app.listen` function.

   ```typescript
   // Central error handling middleware
   app.use((err, req, res, next) => {
      // Log the error stack
      // Respond with error message
   });
   ```

   - **Explanation**: This middleware catches any errors that occur in the application. It logs the error stack to the console and sends a response with the error message and status code. If no specific status is set, it defaults to 500 (Internal Server Error).

#### Step 7: Build and Run the Application

1. **Compile TypeScript Files**: Run the following command to compile the TypeScript files:
   ```bash
   npx tsc
   ```

2. **Start the User Service**: Open a new terminal window and run:
   ```bash
   node build/user-service/server.js
   ```

3. **Start the Flight Service**: In another terminal window, run:
   ```bash
   node build/flight-service/server.js
   ```

#### Step 8: Test the Endpoints

Use Postman or any API testing tool to test the following endpoints for both services.

##### User Service Endpoints

- **Create User**:
  - **Method**: POST
  - **URL**: `http://localhost:3001/users`
  - **Body (JSON)**:
    ```json
    { "name": "Alice Johnson", "email": "alice@example.com" }
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
  - **Body (JSON)**:
    ```json
    { "name": "Alice Smith", "email": "alice.smith@example.com" }
    ```

- **Delete User**:
  - **Method**: DELETE
  - **URL**: `http://localhost:3001/users/1`

##### Flight Service Endpoints

- **Create Flight**:
  - **Method**: POST
  - **URL**: `http://localhost:3002/flights`
  - **Body (JSON)**:
    ```json
    { "origin": "New York", "destination": "Los Angeles", "price": 300 }
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

#### Step 9: Discuss Regular Expressions (Regex)

- **What is Regex?**: Regular expressions are patterns used to match character combinations in strings. They are useful for validating formats, such as email addresses and passwords.

- **Basic Regex Concepts**:
  - **Character Classes**: `[abc]` matches any character a, b, or c.
  - **Quantifiers**: `*` (zero or more), `+` (one or more), `?` (zero or one).
  - **Anchors**: `^` (start of a string), `$` (end of a string).

- **Email Validation Example**:
  ```javascript
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/; // Basic email regex
  ```

- **Password Validation Example**:
  ```javascript
  const passwordRegex = /^(?=.*[A-Z])(?=.*[0-9])(?=.*[!@#$&*]).{6,}$/; // At least one uppercase, one number, one special character, and minimum 6 characters
  ```

#### Step 10: Discuss Microservices Architecture

- **Independent Functionality**: Each microservice (User Service and Flight Service) handles specific functionalities. This allows for independent development, deployment, and scaling.

- **Communication**: Microservices communicate over HTTP, allowing them to be part of a larger system where different services can interact with each other.

- **Scalability**: If the user management service needs to handle more requests, it can be scaled independently of the flight service.

- **Real-World Application**: In a real-world application, this travel booking system could be part of a larger platform where different services handle user management, flight booking, hotel reservations, etc. Each service can be developed and maintained by different teams, allowing for faster development cycles and better resource allocation.

### Conclusion
This live coding exercise provides a comprehensive overview of building a travel booking system with microservices, MongoDB, data validation, and error handling. By following these steps, students can understand how microservices work, how to implement them, and how they can be applied in real-world scenarios. Encourage students to experiment with the code, modify it, and think about how they would integrate these microservices into a larger application.
