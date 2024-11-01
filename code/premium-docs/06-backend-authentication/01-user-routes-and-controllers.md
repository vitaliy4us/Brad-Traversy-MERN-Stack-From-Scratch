# User Routes & Controllers

Now we are at a point in our frontend, where we need the user to be authenticated before we can move further. So, we need to create a user API to handle the authentication. We already have a user schema and model. So we know what a user will look like. We now need to create the routes and controllers to handle the user registration and authentication.

In this lesson, I want to just create the needed routes and controller functions for users and authentication.

Let's start by creating a new file called `userRoutes.js` in the `backend/routes` folder. We will also create a new file called `userController.js` in the `controllers` folder.

## Controller Functions

Let's map out all of the controller functions just so we have them ready and you can have an overview of what we are going to create. In the user controller, add the following code:

```js
import asyncHandler from '../middleware/asyncHandler.js';
import User from '../models/userModel.js';

// @desc    Auth user & get token
// @route   POST /api/users/login
// @access  Public
const authUser = asyncHandler(async (req, res) => {
  res.send('auth user');
});

// @desc    Register a new user
// @route   POST /api/users
// @access  Public
const registerUser = asyncHandler(async (req, res) => {
  res.send('register user');
});

// @desc    Logout user / clear cookie
// @route   POST /api/users/logout
// @access  Public
const logoutUser = (req, res) => {
  res.send('logout user');
};

// @desc    Get user profile
// @route   GET /api/users/profile
// @access  Private
const getUserProfile = asyncHandler(async (req, res) => {
  res.send('get user profile');
});

// @desc    Update user profile
// @route   PUT /api/users/profile
// @access  Private
const updateUserProfile = asyncHandler(async (req, res) => {
  res.send('update user profile');
});

// @desc    Get all users
// @route   GET /api/users
// @access  Private/Admin
const getUsers = asyncHandler(async (req, res) => {
  res.send('get users');
});

// @desc    Delete user
// @route   DELETE /api/users/:id
// @access  Private/Admin
const deleteUser = asyncHandler(async (req, res) => {
  res.send('delete user');
});

// @desc    Get user by ID
// @route   GET /api/users/:id
// @access  Private/Admin
const getUserById = asyncHandler(async (req, res) => {
  res.send('get user by id');
});

// @desc    Update user
// @route   PUT /api/users/:id
// @access  Private/Admin
const updateUser = asyncHandler(async (req, res) => {
  res.send('update user');
});

export {
  authUser,
  registerUser,
  logoutUser,
  getUserProfile,
  updateUserProfile,
  getUsers,
  deleteUser,
  getUserById,
  updateUser,
};
```

We first bring in the `asyncHandler` middleware and the `User` model. Then we create the controller functions for the user routes. We will add the logic to these functions in the coming lessons.

## Adding Routes

Now that we have the controller functions and we are exporting them, we need to connect them to the routes. In the `userRoutes.js` file, add the following code:

```js
import express from 'express';
import {
  authUser,
  registerUser,
  logoutUser
  getUserProfile,
  updateUserProfile,
  getUsers,
  deleteUser,
  getUserById,
  updateUser,
} from '../controllers/userController.js';

const router = express.Router();

router.route('/').post(registerUser).get(getUsers);
router.post('/logout', logoutUser);
router.post('/login', authUser);
router.route('/profile').get(getUserProfile).put(updateUserProfile);

router.route('/:id').delete(deleteUser).get(getUserById).put(updateUser);

export default router;
```

We first import the express router and the controller functions. Then we create the routes for the user API. Later we will add the authentication middleware to the routes that need it. Right now, everything will be public.

## Adding Routes to Server

Now that we have the routes, we need to add them to the server. In the `server.js` file, add the following code:

```js
import userRoutes from './routes/userRoutes.js';
```

Then add the following code to the `app.use` section:

```js
app.use('/api/users', userRoutes);
```

Now, open up Postman and test the routes. You do not have to create all of the saved requests right now. You can if you want, but I will be creating them as we get to adding the functionality.

- GET http://localhost:5000/api/users
- POST http://localhost:5000/api/users
- POST http://localhost:5000/api/users/auth
- POST http://localhost:5000/api/users/logout
- GET http://localhost:5000/api/users/profile
- PUT http://localhost:5000/api/users/profile
- GET http://localhost:5000/api/users/:id
- PUT http://localhost:5000/api/users/:id
- DELETE http://localhost:5000/api/users/:id

You should see the message for each one.
