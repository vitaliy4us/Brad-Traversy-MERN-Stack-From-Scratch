# Auth & User API Slices

We now have the backend API all setup with authentication. We can login, create a JWT, store it in an HTTP-only cookie and that cookie is then sent with every request and validated through our middleware.

Now, we need to setup our user interface to take advantage of that. The way this will work, is we will have an `authSlice` in our Redux state, which will be very simple. It will have a `setCredentials` action to set the user to local storage and a `logout` to clear the user. All of the actual calls to our backend will be from our `usersApiSlice`. Just like we have a `productsApiSlice` to deal with products. We will create both slices in this video and add the `login` endpoint.

Let's start with adding a constant for the users endpoint to our `constants.js` file:

```js
export const USERS_URL = '/api/users';
```

The job of the `authSlice.js` slice is to handle the state after the user is authenticated. It has nothing to do with making requests or the user data. The `usersApiSlice.js` slice will handle that. The `authSlice.js` slice will be really simple, so let's handle that first.

## Create Auth Slice

Create a new file called `authSlice.js` in the `src/slices` folder. Add the following code:

```js
import { createSlice } from '@reduxjs/toolkit';

const initialState = {
  userInfo: localStorage.getItem('userInfo')
    ? JSON.parse(localStorage.getItem('userInfo'))
    : null,
};

const authSlice = createSlice({
  name: 'auth',
  initialState,
  reducers: {
    setCredentials: (state, action) => {
      state.userInfo = action.payload;
      localStorage.setItem('userInfo', JSON.stringify(action.payload));
    },
  },
});

export const { setCredentials } = authSlice.actions;

export default authSlice.reducer;
```

We are going to save the user in localStorage after authentication, which is what we are doing in the `setCredentials` action. We are also setting the `userInfo` property in the state to the user data.

## Add Auth Slice to Store

Open the `store.js` file and add the auth reducer from the slice. Your code should look like this:

```js
import { configureStore } from '@reduxjs/toolkit';
import { apiSlice } from './slices/apiSlice';
import cartSliceReducer from './slices/cartSlice';
import authSliceReducer from './slices/authSlice'; // add this line

const store = configureStore({
  reducer: {
    [apiSlice.reducerPath]: apiSlice.reducer,
    cart: cartSliceReducer,
    auth: authSliceReducer, // add this line
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(apiSlice.middleware),
  devTools: true,
});

export default store;
```

If you look in your Redux DevTools, you should see the `auth` slice with the `userInfo` property.

## Create Users API Slice

Now, we want to be able to work with our backend user API. We will create a new slice called `usersApiSlice.js` to handle this. Our first task and endpoint is to authenticate/login the user. We will add that endpoint now.

Add the following code to the `usersApiSlice.js` file:

```js
import { apiSlice } from './apiSlice';
import { USERS_URL } from '../constants';

export const usersApiSlice = apiSlice.injectEndpoints({
  endpoints: (builder) => ({
    login: builder.mutation({
      query: (data) => ({
        url: `${USERS_URL}/auth`,
        method: 'POST',
        body: data,
      }),
    }),
  }),
});

export const { useLoginMutation } = usersApiSlice;
```

We are using the `apiSlice` to create a new endpoint called `auth`. We are using the `login` mutation from the `apiSlice` to create the `useLoginMutation` hook. We will use this hook to login the user from the loginScreen, which we will create in the next lesson.
