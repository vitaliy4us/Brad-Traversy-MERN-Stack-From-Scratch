# React PayPal Integration

In the last lesson, we created the backend functionality to update an order to 'paid'. We also registerd an app with PayPal and created a sandbox account. In this lesson, we will create the frontend functionality to allow users to pay for their orders using PayPal.

## React Paypal JS

We will be using a package called [@paypal/react-paypal-js](https://www.npmjs.com/package/@paypal/react-paypal-js) to integrate PayPal into our application. This package is a wrapper around the [PayPal JS SDK](https://developer.paypal.com/docs/business/javascript-sdk/javascript-sdk-reference/). The PayPal JS SDK is a JavaScript library that allows you to integrate PayPal into your application. Using this package will make things easier for us.

Install the package in the `frontend` directory:

```bash
cd frontend
npm install @paypal/react-paypal-js
```

We need to add the `PayPalScriptProvider` to our app. This component will provide the PayPal JS SDK to our application. Import it into the `index.js` file:

```js
import { PayPalScriptProvider } from '@paypal/react-paypal-js';
```

Then wrap the `RouteProvider` component with the `PayPalScriptProvider` component:

```js
root.render(
  <React.StrictMode>
    <Provider store={store}>
      <PayPalScriptProvider deferLoading={true}>
        <RouterProvider router={router} />
      </PayPalScriptProvider>
    </Provider>
  </React.StrictMode>
);
```

## Paypal URL Constant

Open the `constants.js` file and add the following constant:

```js
export const PAYPAL_URL = '/api/config/paypal';
```

## Add Reducers

Open the `orders.js` file and import the constant:

```js
import { ORDERS_URL, PAYPAL_URL } from '../constants';
```

Then add the following reducer functions:

```js
payOrder: builder.mutation({
  query: ({ orderId, details }) => ({
    url: `${ORDERS_URL}/${orderId}/pay`,
    method: 'PUT',
    body: details,
}),
}),
getPaypalClientId: builder.query({
  query: () => ({
    url: PAYPAL_URL,
  }),
  keepUnusedDataFor: 5,
}),
```

Be sure to export them:

```js
export const {
  useCreateOrderMutation,
  useGetOrderDetailsQuery,
  usePayOrderMutation,
  useGetPaypalClientIdQuery,
} = ordersApiSlice;
```

## PayPal functionality

Open the `OrderScreen.js` file and add the following imports:

```js
import { useEffect } from 'react';
import { Link, useParams } from 'react-router-dom';
import { Row, Col, ListGroup, Image, Card, Button } from 'react-bootstrap';
import { PayPalButtons, usePayPalScriptReducer } from '@paypal/react-paypal-js';
import { useSelector } from 'react-redux';
import { toast } from 'react-toastify';
import Message from '../components/Message';
import Loader from '../components/Loader';
import {
  useGetOrderDetailsQuery,
  usePayOrderMutation,
  useGetPaypalClientIdQuery,
} from '../slices/ordersApiSlice';
```

Right under where we used the `useGetOrderDetailsQuery` hook, add the following:

```js
const [payOrder, { isLoading: loadingPay }] = usePayOrderMutation();

const { userInfo } = useSelector((state) => state.auth);

const [{ isPending }, paypalDispatch] = usePayPalScriptReducer();

const {
  data: paypal,
  isLoading: loadingPayPal,
  error: errorPayPal,
} = useGetPaypalClientIdQuery();
```

We are using the `usePayOrderMutation` hook to call the `payOrder` mutation. We are also using the `useSelector` hook to get the `userInfo` from the Redux store.

e are also using the `usePayPalScriptReducer` hook to get the `isPending` state from the PayPal JS SDK. This state will be `true` when the PayPal JS SDK is loading and `false` when it is loaded.

We are also using the `useGetPaypalClientIdQuery` hook to get the PayPal client ID from the backend. This will be used to initialize the PayPal JS SDK.

Now, we need to add our `useEffect` hook to initialize the PayPal JS SDK:

```js
useEffect(() => {
  if (!errorPayPal && !loadingPayPal && paypal.clientId) {
    const loadPaypalScript = async () => {
      paypalDispatch({
        type: 'resetOptions',
        value: {
          'client-id': paypal.clientId,
          currency: 'USD',
        },
      });
      paypalDispatch({ type: 'setLoadingStatus', value: 'pending' });
    };
    if (order && !order.isPaid) {
      if (!window.paypal) {
        loadPaypalScript();
      }
    }
  }
}, [errorPayPal, loadingPayPal, order, paypal, paypalDispatch]);
```

This hook will run when any of its dependencies change. If there is no error, the PayPal client is not loading, and the PayPal client ID is loaded, then it will create the function `loadPayPalScript`. Then it will check if the order is paid. If the order is not paid, then it will check if the `window.paypal` object exists (if paypal is already loaded). If it does not exist, then it will call the `loadPaypalScript` function. This function will reset the options for the PayPal JS SDK and set the loading status to `pending`.


