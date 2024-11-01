# Set Frontend User Expiration

NOTE: I did not implemen this in the course because I set the expiration to 30 days. If you set it to something less like 1 week or 1 day or 1 hour, you should probably do this.

The way we have it right now, our JWT cookie expires in 1 day. That is because that is how we set it up in the backend. However, our frontend local storage user data does not expire. This means that after 24 hours, it will seem as though we are logged in in the UI, but once we try and make a request to a protected route, it won't work because the JWT cookie will have expired.

There are a few approaches we can take to fix this. We could have an endpoint to validate the JWT cookie and then update the frontend local storage user data. However, this would require an extra request to be made every time we load a page.

What I want to do is add an expiration to the frontend local storage user data that expires at the same time the JWT cookie does. This way, we can check if the user data has expired and if it has, we can log the user out.

## Add Expiration to User Data

Modify the `setCredentials` reducer in the `userSlice.js` file to add an expiration time to the user data. We will set it to 1 minute for now so that we can test. I will add it for an hour as well, but comment it out.

We will also add the removal of expirationTime in the `logout` reducer.

```js
  reducers: {
    setCredentials: (state, action) => {
      state.userInfo = action.payload;
      localStorage.setItem('userInfo', JSON.stringify(action.payload));

      // const expirationTime = new Date().getTime() + 24 * 60 * 60 * 1000; // 1 day
      const expirationTime = new Date().getTime() + 60 * 1000; // 1 minute (for testing)
      localStorage.setItem('expirationTime', expirationTime);
    },
    logout: (state, action) => {
      state.userInfo = null;
      localStorage.removeItem('userInfo');
      localStorage.removeItem('expirationTime');
    },
  },
```

You can test by logging in and checking the local storage. You should see the expiration time in there. When you logout, it should be removed.

## Check Expiration

Now, we want to check the expiration time when we load the app. We will do this in the `App.js` file. We will add a new `useEffect` hook that will check the expiration time and log the user out if it has expired.

Import the following into the `App.js` file:

```js
import { useEffect } from 'react';
import { useDispatch } from 'react-redux';
import { logout } from './slices/userSlice';
```

Then initialize the dispatch and add the useEffect:

```js
const App = () => {
  const dispatch = useDispatch();

  useEffect(() => {
    const expirationTime = localStorage.getItem('expirationTime');
    if (expirationTime) {
      const currentTime = new Date().getTime();
      if (currentTime > expirationTime) {
        dispatch(logout());
      }
    }
  }, [dispatch]);

  // ... rest of the code
```

Now, after 1 minute, you will be logged out from the UI.

Keep in mind, the JWT cookie will still be valid for another 59 minutes. You could make it so that the JWT cookie expires at the same time as the frontend local storage user data, but I will leave that as an exercise for you if you want. We shouldn't have to do that since we will have the same expiration time on the backend.

In the `authSlice.js`, uncomment and change the exp time on the frontend to 1 hour, or whatever you want, just make sure it matches the backend.
