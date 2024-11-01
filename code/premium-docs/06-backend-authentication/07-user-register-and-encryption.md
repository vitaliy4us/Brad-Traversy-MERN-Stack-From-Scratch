# User Register & Password Encryption

Now that we can authenticate current users, we need a way to create/register new users. We also need to encrypt the passwords so that they are not stored in plain text.

Open the `userController.js` file and in the `registerUser` function, add the following code:

```js
const registerUser = asyncHandler(async (req, res) => {
  const { name, email, password } = req.body;

  const userExists = await User.findOne({ email });

  if (userExists) {
    res.status(400);
    throw new Error('User already exists');
  }

  const user = await User.create({
    name,
    email,
    password,
  });

  if (user) {
    res.status(201).json({
      _id: user._id,
      name: user.name,
      email: user.email,
      isAdmin: user.isAdmin,
    });
  } else {
    res.status(400);
    throw new Error('Invalid user data');
  }
});
```

Here, we are checking if the user already exists in the database. If they do, we throw an error. If they don't, we create a new user and send back the user data.

## Encrypting Passwords

Right now, if we ran the code above, we would be able to see the password in plain text in the database. We need to encrypt the password so that it is not stored in plain text. We will be using the `bcryptjs` package to do this. Instead of doing it here, we can do it in the `userModel.js` file.

Add this to your `userModel.js` file:

```js
// Encrypt password using bcrypt
userSchema.pre('save', async function (next) {
  if (!this.isModified('password')) {
    next();
  }

  const salt = await bcrypt.genSalt(10);
  this.password = await bcrypt.hash(this.password, salt);
});
```

What we are doing here is adding some Mongoose middleware to the `save` method. We are checking if the password has been modified. If it has, we are generating a salt and hashing the password. We are then setting the password to the hashed password.

We could just do this in the controller, but again, I am trying to keep the controller as clean as possible.

## JWT Token

I want users to essentially be authenticated after registration, so we need to create our token and save to a cookie, just like we did in the `authUser` function.

Since we are doing the same thing in both places, let's create a helper function to generate the token. Create a new file called `generateToken.js` in the `utils` folder. Add the following code:

```js
import jwt from 'jsonwebtoken';

const generateToken = (res, userId) => {
  const token = jwt.sign({ userId }, process.env.JWT_SECRET, {
    expiresIn: '1h',
  });

  // Set JWT as an HTTP-Only cookie
  res.cookie('jwt', token, {
    httpOnly: true,
    secure: process.env.NODE_ENV !== 'development', // Use secure cookies in production
    sameSite: 'strict', // Prevent CSRF attacks
    maxAge: 60 * 60 * 1000, // 1 hour in milliseconds
  });
};

export default generateToken;
```

Now, in your `userController.js` file, import the `generateToken` function

```js
import generateToken from '../utils/generateToken.js';
```

Add it to the `registerUser` function right before the response:

```js
if (user) {
  generateToken(res, user._id); // Add this line

  res.status(201).json({
    _id: user._id,
    name: user.name,
    email: user.email,
    isAdmin: user.isAdmin,
  });
} else {
  res.status(400);
  throw new Error('Invalid user data');
}
```

Also, replace the code in the `authUser` function with the following:

```js
const authUser = asyncHandler(async (req, res) => {
  const { email, password } = req.body;

  const user = await User.findOne({ email });

  if (user && (await user.matchPassword(password))) {
    generateToken(res, user._id); // Add this line

    res.json({
      _id: user._id,
      name: user.name,
      email: user.email,
      isAdmin: user.isAdmin,
    });
  } else {
    res.status(401);
    throw new Error('Invalid email or password');
  }
});
```

## Test Register

Go into Postman and create a new request to register a user. Make a `POST` request to `http://localhost:5000/api/users`. In the body, add the following:

```json
{
  "name": "Test User",
  "email": "test@email.com",
  "password": "123456"
}
```

Click `Send`. You should get a response with the user data. You should also see the jwt cookie. You are now authenticated and can make requests to protected routes as long as they are not admin routes.
