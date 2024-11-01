# User Logout

To log out a user, we need to do two things:

- Clear the JWT cookie
- Clear the user data from the Redux store & localStorage

## Clear JWT Cookie

To clear the JWT cookie, we need to set the cookie to an empty string and set the expiration date to the past. We already have an endpoint in the backend to do this. Now, we need to add a new mutation to our frontend `usersApiSlice.js` file.

```js
export const usersApiSlice = apiSlice.injectEndpoints({
  endpoints: (builder) => ({
    login: builder.mutation({
      query: (data) => ({
        url: `${USERS_URL}/auth`,
        method: 'POST',
        body: data,
      }),
    }),
    logout: builder.mutation({
      query: () => ({
        url: `${USERS_URL}/logout`,
        method: 'POST',
      }),
    }),
  }),
});

export const { useLoginMutation, useLogoutMutation } = usersApiSlice;
```

We will also add a logout reducer to our `authSlice.js` file.

```js
const authSlice = createSlice({
  name: 'auth',
  initialState,
  reducers: {
    setCredentials: (state, action) => {
      state.userInfo = action.payload;
      localStorage.setItem('userInfo', JSON.stringify(action.payload));
    },
    logout: (state, action) => {
      state.userInfo = null;
      localStorage.removeItem('userInfo');
    },
  },
});
```

This is responsible for clearing the user data from the Redux store and localStorage.

## Implementing the Logout

We already have a logout link in the header/navbar with a handler. Let's open the `Header.js` file and finish the logout handler.

Import the `useLogoutMutation` and `logout` actions from the `usersApiSlice.js` and `authSlice.js` files. Also import the `useNavigate` hook from `react-router-dom` and `useDispatch` from `react-redux`.

```js
import { useLogoutMutation } from '../slices/usersApiSlice';
import { logout } from '../slices/authSlice';
import { useNavigate } from 'react-router-dom';
import { useSelector, useDispatch } from 'react-redux';
```

Add the following code above the return statement:

```js
const dispatch = useDispatch();
const navigate = useNavigate();

const [logoutApiCall] = useLogoutMutation();

const logoutHandler = async () => {
  try {
    await logoutApiCall().unwrap();
    dispatch(logout());
    navigate('/login');
  } catch (err) {
    console.error(err);
  }
};
```

We are initializing the `dispatch` and `navigate` variables. We are also initializing the `logoutApiCall` variable, which is the `useLogoutMutation` hook.

Then we make the `logoutHandler` async function. We are calling the `logoutApiCall` function and then we are dispatching the `logout` action. Finally, we are navigating to the `/login` page.

If you click the logout link, you should be logged out and redirected to the login page.

You can open the `Application` tab in your devtools and see the user is not in localstorage and the JWT cookie is cleared.
