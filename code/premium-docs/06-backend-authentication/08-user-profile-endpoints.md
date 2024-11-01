# User Profile Endpoints

We need a way to check the HTTP-only cookie and return the user data. We also want to be able to update the name and email.

## Get User Profile

Add the following to the `userController.js` file for the `getUserProfile` function:

```js
const getUserProfile = asyncHandler(async (req, res) => {
  const user = await User.findById(req.user._id);

  if (user) {
    res.json({
      _id: user._id,
      name: user.name,
      email: user.email,
      isAdmin: user.isAdmin,
    });
  } else {
    res.status(404);
    throw new Error('User not found');
  }
});
```

This function will get the user data from the database, based on the ID in the JWT cookie and return it to the client.

## Update User Profile

Add the following to the `userController.js` file for the `updateUserProfile` function:

```js
const updateUserProfile = asyncHandler(async (req, res) => {
  const user = await User.findById(req.user._id);

  if (user) {
    user.name = req.body.name || user.name;
    user.email = req.body.email || user.email;

    if (req.body.password) {
      user.password = req.body.password;
    }

    const updatedUser = await user.save();

    res.json({
      _id: updatedUser._id,
      name: updatedUser.name,
      email: updatedUser.email,
      isAdmin: updatedUser.isAdmin,
    });
  } else {
    res.status(404);
    throw new Error('User not found');
  }
});
```

This function will get the user data from the database, based on the ID in the JWT cookie, and update the name and email. If the password is included in the request, it will also update the password.

In Postman, create a new request called `Update User Profile` and make a `PUT` request to `http://localhost:5000/api/users/profile`. In the body, add the following:

```json
{
  "name": "Updated Name"
}
```

Now if you make a request to `Get User Profile`, you should see the updated name.

If you change your password and logout, you will need to use your new password.
