# Private Routes

We are going to want to protect the shipping and payment screens so that if a user is not logged in, they cannot access them. We are going to do this by creating a private route component.

Creating protected routes on the frontend is more for user experience. So we can create our private routes based on the user in local storage. The real security is on the backend because that is where the real changes are made. If a user tries to access a protected backend route without having a valid token, the backend will return an error.

In the `components` folder, create a new file named `PrivateRoute.js`. Add the following code:

```js
import { Navigate, Outlet } from 'react-router-dom';
import { useSelector } from 'react-redux';

const PrivateRoute = () => {
  const { userInfo } = useSelector((state) => state.auth);
  return userInfo ? <Outlet /> : <Navigate to='/login' replace />;
};
export default PrivateRoute;
```

We are going to use the `Navigate` component from `react-router-dom` to redirect the user to the login screen if they are not logged in. We are also going to use the `Outlet` component to render the component that is passed in as a child of the `PrivateRoute` component.

## Using the PrivateRoute Component

To use this, open up the `index.js` where the router is and import the `PrivateRoute` component.

```js
import PrivateRoute from './components/PrivateRoute';
```

Then, we can wrap the shipping screen with the `PrivateRoute` component.

```js
{
  /* Registered users */
}
<Route path='' element={<PrivateRoute />}>
  <Route path='/shipping' element={<ShippingScreen />} />
</Route>;
```

Whenever we want to add a protected route, we can just wrap it with the `PrivateRoute` component.

Now make sure that you are logged out and go to the `/shipping` route. You should be redirected to the login screen.

Log in and you should be able to access the shipping screen.
