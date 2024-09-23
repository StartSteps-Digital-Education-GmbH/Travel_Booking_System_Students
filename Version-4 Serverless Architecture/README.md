### Serverless Architecture: A Guide for Deploying Microservices (Users and Flights) on Vercel

#### 1. **What is Serverless?**
Serverless architecture allows developers to build applications without managing servers. Cloud providers like Vercel handle the infrastructure, letting you focus solely on your code. Functions are invoked based on events (e.g., HTTP requests).

**Advantages of Serverless:**
- **Automatic Scaling**: The cloud provider automatically scales your application based on traffic.
- **Cost Efficiency**: You only pay for what you use.
- **Less Maintenance**: No need to manage server infrastructure.
- **Faster Deployments**: Serverless functions are quick to deploy and update.

### 2. **Step-by-Step Guide to Transition to Serverless on Vercel**

You will refactor your existing microservices for **Users** and **Flights** into serverless functions. Each service will be deployed independently to allow for better scaling and management.

#### Folder Structure
Given your current structure, we will create separate serverless functions for both the **flight** and **user** services.

**Your updated folder structure:**
```plaintext
project-root/
├── package.json
├── tsconfig.json
├── src/
│   ├── flight-service/
│   │   ├── flightRoutes.ts
│   │   └── server.ts
│   ├── user-service/
│   │   ├── userRoutes.ts
│   │   └── server.ts
├── api/
│   ├── flights.ts        # Serverless function for flights
│   └── users.ts          # Serverless function for users
└── vercel.json           # Vercel configuration
```

#### Step 1: **Refactor `server.ts` for Users and Flights**
To make your services serverless, refactor both `server.ts` files to remove `app.listen()` and manage MongoDB connections within the request lifecycle.

**User Service (`src/user-service/server.ts`):**
```ts
import express from 'express';
import router from './userRoutes.js';
import mongoose from 'mongoose';

const app = express();

// Middleware to handle JSON requests
app.use(express.json());

// Route handling for users
app.use('/users', router);

// MongoDB connection logic
const connectDB = async () => {
    const mongoUri = process.env.MONGODB_URI;
    try {
        await mongoose.connect(mongoUri);
        console.log("Connected to the DB");
    } catch (err) {
        console.log("Error in connecting to the DB", err);
    }
};

export { app, connectDB };
```

**Flight Service (`src/flight-service/server.ts`):**
```ts
import express from 'express';
import flightRouter from './flightRoutes.js';
import mongoose from 'mongoose';

const app = express();
app.use(express.json());
app.use('/flights', flightRouter);

// MongoDB connection logic
const connectDB = async () => {
    const mongoUri = process.env.MONGODB_URI;
    try {
        await mongoose.connect(mongoUri);
        console.log("Connected to the DB");
    } catch (err) {
        console.log("Error in connecting to the DB", err);
    }
};

export { app, connectDB };
```

#### Step 2: **Create Serverless Functions**

In Vercel, each file in the `api/` folder is treated as a separate serverless function. Create a function for both the **users** and **flights** services.

**User Service Function (`api/users.ts`):**
```ts
import { VercelRequest, VercelResponse } from '@vercel/node';
import { app, connectDB } from '../src/user-service/server';

export default async (req: VercelRequest, res: VercelResponse) => {
    await connectDB();  // Ensure database connection
    await app(req, res);  // Pass request to Express app
};
```

**Flight Service Function (`api/flights.ts`):**
```ts
import { VercelRequest, VercelResponse } from '@vercel/node';
import { app, connectDB } from '../src/flight-service/server';

export default async (req: VercelRequest, res: VercelResponse) => {
    await connectDB();  // Ensure database connection
    await app(req, res);  // Pass request to Express app
};
```

#### Step 3: **Set Up MongoDB Environment Variables**
Move your MongoDB connection string to a `.env` file for better security and easier management.

Create a `.env` file:
```plaintext
MONGODB_URI=mongodb+srv://...
```

#### Step 4: **Add Vercel Configuration**
Create a `vercel.json` file to configure your project for deployment.

**vercel.json:**
```json
{
  "version": 2,
  "builds": [
    {
      "src": "api/users.ts",
      "use": "@vercel/node"
    },
    {
      "src": "api/flights.ts",
      "use": "@vercel/node"
    }
  ],
  "routes": [
    {
      "src": "/api/users",
      "dest": "/api/users.ts"
    },
    {
      "src": "/api/flights",
      "dest": "/api/flights.ts"
    }
  ]
}
```

#### Step 5: **Install Dependencies**
Ensure all required packages are installed in your project.

Run:
```bash
npm install express mongoose @vercel/node
```

#### Step 6: **Deploy to Vercel**
1. **Install Vercel CLI** (if you haven't yet):
   ```bash
   npm install -g vercel
   ```

2. **Deploy the Project**:
   ```bash
   vercel
   ```

   Vercel will ask a few questions, and then your project will be deployed. It will handle the creation of serverless functions for both services.

#### Step 7: **Access Your Serverless APIs**
Once deployed, Vercel will give you a URL to access your serverless functions:
```
https://your-vercel-project.vercel.app/api/users
https://your-vercel-project.vercel.app/api/flights
```

### 3. **Why Deploy Services Separately in Serverless?**
- **Independent Scaling**: Each service (users, flights) scales based on its traffic. If the flight service gets more requests, it will scale without affecting the user service.
- **Isolated Failures**: If one service fails, it won’t affect the other. This isolation increases the robustness of your system.
- **Simplified Development**: Separate services allow for easier updates and testing since changes to one service won’t impact the other.

### 4. **Conclusion**
By deploying your **user** and **flight** services as separate serverless functions, you adhere to microservices best practices in a serverless environment. Each service remains independent, scalable, and isolated, ensuring better performance and manageability in production.