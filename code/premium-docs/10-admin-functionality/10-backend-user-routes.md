# Backend User Routes

Just like we can list orders and products for admins, we also want to be able to list all of the users. If we go to our backend `userController.js` file, we can see that we already have all of our functions created. The last four, which are `getUsers`, `deleteUser`, `getUserById`, and `updateUser` are just returning a string at the moment. So I want to finish these up.

## `getUsers`

Add the following code to the `getUsers` function:

```js
const getUsers = asyncHandler(async (req, res) => {
  const users = await User.find({});
  res.json(users);
});
```

This is very simple, we are just getting all the users from the database and returning them.

## `deleteUser`

We need to be able to delete users from the database. Add the following code to the `deleteUser` function:

```js
const deleteUser = asyncHandler(async (req, res) => {
  const user = await User.findById(req.params.id);

  if (user) {
    if (user.isAdmin) {
      res.status(400);
      throw new Error('Can not delete admin user');
    }
    await User.deleteOne({ _id: user._id });
    res.json({ message: 'User removed' });
  } else {
    res.status(404);
    throw new Error('User not found');
  }
});
```

We are getting the user by ID and checking to see if it exists. If so, then we are checking if the user is admin and if so, we can not delete the user. If they are not admin, then we remove them. If the user does not exist, we throw an error.

## `getUserById`

Add the following code to get a single user by ID:

```js
const getUserById = asyncHandler(async (req, res) => {
  const user = await User.findById(req.params.id).select('-password');

  if (user) {
    res.json(user);
  } else {
    res.status(404);
    throw new Error('User not found');
  }
});
```

We are getting the user by ID and returning it. We are also excluding the password from the response.

## `updateUser`

Add the following code to the `updateUser` function:

```js
const updateUser = asyncHandler(async (req, res) => {
  const user = await User.findById(req.params.id);

  if (user) {
    user.name = req.body.name || user.name;
    user.email = req.body.email || user.email;
    user.isAdmin = Boolean(req.body.isAdmin);

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

This is pretty simple. We are getting the user by ID and then updating the name, email, and isAdmin properties. We are then saving the user and returning the updated user.

You can test these in Postman if you would like. Just be sure to login as admin and send the cookie with the request.
