### 1. Scenario: Linking Users to Flights
Suppose we want to create a flight booking system where each flight is associated with a user. The microservices for users and flights are separate, but we want them to communicate when a user books a flight. For example, when creating a flight, we also want to validate if the user exists in the user service.

### 2. Microservice Communication Strategy
One common way to achieve communication between microservices is through **HTTP requests** (using REST APIs). This is a simple and widely used method where one service makes a request to another.

Let's modify your `flightController.create` function to communicate with the user service running on port 3001.

### Example: Creating a Flight with User Validation

#### Step 1: Modify `flightController.create`
Before creating a flight, the flight service can make an HTTP request to the user service to check if the user exists.

```ts
import axios from 'axios'; // Use axios for making HTTP requests

const create = async (req: Request, res: Response) => {
  const { userId, origin, destination, price } = req.body;

  try {
    // Make a request to the User Service to check if the user exists
    const userResponse = await axios.get(`http://localhost:3001/users/${userId}`);
    
    if (userResponse.status === 200) {
      // User exists, proceed with flight creation
      const newFlight = { userId, origin, destination, price };
      //saving the flight to the database
      return res.status(201).json({ message: 'Flight created', flight: newFlight });
    } else {
      return res.status(404).json({ message: 'User not found' });
    }
  } catch (error) {
    return res.status(500).json({ message: 'Error communicating with User Service', error: error.message });
  }
};

```

#### Step 2: Update the Flight Routes
The `POST /flights` route now requires a `userId` to link the flight to the user:

#### Step 3: User Service Implementation
Your user service already has a `GET /users/:id` route that returns a user by ID. This is where the flight service sends its request to validate if the user exists.

#### Explaination:
- **Communication Type**: The flight service communicates with the user service using an HTTP request (`axios.get`). This is an example of **synchronous communication** where the flight service waits for a response from the user service before proceeding.
- **Service Independence**: The user and flight services are independently deployed and can run on separate servers or containers. They only communicate through well-defined APIs.
- **Error Handling**: The flight service needs to handle errors, such as when the user service is down or the user does not exist.
