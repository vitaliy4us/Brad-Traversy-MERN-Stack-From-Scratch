# Order API Slice & Screen

We need to add the Redux state that handles orders. We also need to create the `PlaceOrderScreen` component.

Let's start by adding the `ORDERS_URL` constant to the `frontend/constants.js` file:

```js
export const ORDERS_URL = '/api/orders';
```

Let's create the order api slice. Create a new file called `ordersApiSlice.js` in the `frontend/slices` folder. Add the following code:

```js
import { apiSlice } from './apiSlice';
import { ORDERS_URL } from '../constants';

export const ordersApiSlice = apiSlice.injectEndpoints({
  endpoints: (builder) => ({
    createOrder: builder.mutation({
      query: (order) => ({
        url: ORDERS_URL,
        method: 'POST',
        body: { ...order },
      }),
    }),
  }),
});

export const { useCreateOrderMutation } = ordersApiSlice;
```

It's pretty self explanatory now that we know how we are handling endpoints. We are creating a mutation that will create an order. We are using the `ORDERS_URL` constant that we created earlier.

The body will contain all of the order data.

## Clear Cart

After making an order, we need to clear the cart items. Open up the `cartSlice.js` file in the `frontend/slices` folder. Add the following reducer:

```js
clearCartItems: (state, action) => {
  state.cartItems = []
  localStorage.setItem('cart', JSON.stringify(state))
},
```

This will set the cart items to an empty array and save the cart to local storage as empty.

Export the `clearCartItems` action creator from the `cartSlice.js` file:

```js
export const {
  addToCart,
  removeFromCart,
  saveShippingAddress,
  savePaymentMethod,
  clearCartItems,
} = cartSlice.actions;
```

## Place Order Screen

We now need to create the place order screen. Create a new file called `PlaceOrderScreen.jsx` in the `frontend/screens` folder. Add the following code:

```js
import { useEffect } from 'react';
import { Link, useNavigate } from 'react-router-dom';
import { Button, Row, Col, ListGroup, Image, Card } from 'react-bootstrap';
import { useSelector } from 'react-redux';
import CheckoutSteps from '../components/CheckoutSteps';

const PlaceOrderScreen = () => {
  const navigate = useNavigate();

  const cart = useSelector((state) => state.cart);

  useEffect(() => {
    if (!cart.shippingAddress.address) {
      navigate('/shipping');
    } else if (!cart.paymentMethod) {
      navigate('/payment');
    }
  }, [cart.paymentMethod, cart.shippingAddress.address, navigate]);

  return (
    <>
      <CheckoutSteps step1 step2 step3 step4 />
      <Row>
        <Col md={8}>Column</Col>
        <Col md={4}>Column</Col>
      </Row>
    </>
  );
};

export default PlaceOrderScreen;
```

We are getting the cart from the Redux state. In the useEffect, we are checking if the shipping address and payment method are set. If they are not, we are redirecting to the shipping and payment screens.

Then we are just displaying the checkout steps and two columns. We will add the rest of the UI in the next lesson.

## Add To router

Open up the `frontend/index.js` file. Add the following route:

```js
import PlaceOrderScreen from './screens/PlaceOrderScreen';
```

```js
<Route path='' element={<PrivateRoute />}>
  <Route path='/placeorder' element={<PlaceOrderScreen />} />
</Route>
```

Make sure it is nested in the `PrivateRoute` component.
