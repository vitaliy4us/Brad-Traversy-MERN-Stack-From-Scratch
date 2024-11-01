# Logout User / Clear Cookie

Now that we have a way to authenticate and get an HTTP-Only cookie, we need a way to clear the cookie when the user logs out.

We already have the route and controller for logging out, so we just need to add the functionality to clear the cookie.

Open the `userController.js` file and add the following code to the `logout` function:

```js
const logoutUser = (req, res) => {
  res.cookie('jwt', '', {
    httpOnly: true,
    expires: new Date(0),
  });
  res.status(200).json({ message: 'Logged out successfully' });
};
```

This will clear the `jwt` cookie by setting the value to an empty string, and setting the expiration date to `new Date(0)`, which is the beginning of time.

## Test It Out

In Postman, create a new request called 'Logout User' and make a POST request to `http://localhost:5000/api/users/logout`. Your cookie should clear. Send another request to `http://localhost:5000/api/users/profile` and you should get a 401 error.
