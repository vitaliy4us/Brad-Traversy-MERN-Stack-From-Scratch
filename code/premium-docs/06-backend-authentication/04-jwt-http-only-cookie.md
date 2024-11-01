# JWT HTTP Only Cookie

In this lesson, you will learn how to use JWT to authenticate users and how to store the JWT in an HTTP Only Cookie.

You may have seen other courses, including my own and including the first version of this course were we generate the token and then send it to the client. The client then stores the token in local storage and sends it back to the server with every request. This is not the best practice because then the token is available on the client. The token is just a string and it can be easily copied and pasted. This is why we are going to store the token in an HTTP Only Cookie.

Once we set the HTTP-only cookie, it will be sent with every request to the server. The server can then verify the token and if it is valid, the user is authenticated. If the token is not valid, the user is not authenticated.

We will be using the `jsonwebtoken` package to generate the token. Let's install that:

```bash
npm install jsonwebtoken
```

Now import it at the top of the user controller:

```js
import jwt from 'jsonwebtoken';
```

Now in the `authUser` function, add the following:

```js
const authUser = asyncHandler(async (req, res) => {
  const { email, password } = req.body;

  const user = await User.findOne({ email });

  if (user && (await user.matchPassword(password))) {
    const token = jwt.sign({ userId: user._id }, process.env.JWT_SECRET, {
      expiresIn: '1d',
    });

    // Set JWT as an HTTP-Only cookie
    res.cookie('jwt', token, {
      httpOnly: true,
      secure: process.env.NODE_ENV !== 'development', // Use secure cookies in production
      sameSite: 'strict', // Prevent CSRF attacks
      maxAge: 30 * 24 * 60 * 60 * 1000; // 30 days
    });

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

Once we validate the user, we are creating the token with `jwt.sign`. We are passing in the user ID as the payload and the JWT secret. We are also setting the `expiresIn` option to 1 hour. This means that the token will expire after 1 hour and the user will have to log in again.

We are also setting the `secure` option to true, which means that the cookie will only be sent over HTTPS. We are also setting the `sameSite` option to `strict`, which means that the cookie will only be sent if the request originated from the same site. This prevents CSRF attacks. I am going to set the `maxAge` option to 1 day. This means that the cookie will expire after 24 hours and they will have to log in again. You can set this to whatever you want. Obviously, the shorter, the more secure, but also the more annoying it is for the user. If you want to set it to 1 hour, you could do this:

```js
const token = jwt.sign({ userId: user._id }, process.env.JWT_SECRET, {
  expiresIn: '1d',
});
```

Then set the maxAge to 1 hour:

```js
maxAge: 30 * 24 * 60 * 60 * 1000 // 30 Days
```

I'm going to keep it at 1 day for now.

## JWT Secret

Now let's add the JWT secret to our `.env` file:

```bash
JWT_SECRET=abc123def456
```

You can make this anything that you want. Restart the server once you add this.

## Testing the JWT HTTP Only Cookie

Now let's test. Make the request to `/api/users/auth` with the correct email and password. You will get the user data, but you can also click on the cookies tab and you will see the HTTP-only cookie called `jwt`.

<img src="./images/jwtcookie2.png" width="500">

It says "secure" is false because we are using HTTP and not HTTPS in development.

If you click on the `headers` tab, you will also see it. This is because the cookie is sent with every request. You can also see the `secure` and `sameSite` options.

<img src="./images/jwtcookie1.png" width="500">

Sometimes the cookie does not get added to the cookie tab automatically. I'm not sure why, but you can just attach it to the headers yourself. So if you are still seeing an error that says "No token", go to your `headers` tab in the request and add `Cookie` as the key and paste your token as the value. It will be something like this:

```bash
jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiI2NDBhMTRjN2M0MzAzZGFlMDdiMGJjYjMiLCJpYXQiOjE2NzkyMjcyMjgsImV4cCI6MTY3OTIzMDgyOH0.eNGwCH1ILsnDrYabEVdZ6Q21dVUuyX_jh5_hIcKLehU; Max-Age=3600; Path=/; Expires=Sun, 19 Mar 2023 13:00:28 GMT; HttpOnly; Secure; SameSite=Strict
```

<img src="./images/jwtcookie3.png" width="500">

You can also highlight the cookie and select `save as variable` and use that variable in other requests. Until the cookie expires or you logout.

```

```
