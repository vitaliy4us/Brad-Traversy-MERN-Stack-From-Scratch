# Clean Up Add To Cart

Right now, add to cart is working fine, but if we look in our `cartSlice.js` file, we can see that we have a lot of code in that one reducer. We also have a helper function for the decimals. I want to put some of this in a separate utility file to clean up a bit.

## Create a utility file

Create a folder in `frontend/src` called `utils` and create a file called `cartUtils.js`.

We are going to cut our `addDecimals` function and paste it into this file. Add an `export` to it as well:

```js
export const addDecimals = (num) => {
  return (Math.round(num * 100) / 100).toFixed(2);
};
```

Now, we will create a function called `updateCart`. Cut the following code from your addToCart reducer and add it here. This function will take in the state and add the prices. It will also save the cart to localStorage. We can reuse this when we need to remove items as well.

```js
export const updateCart = (state) => {
  // Calculate the items price
  state.itemsPrice = addDecimals(
    state.cartItems.reduce((acc, item) => acc + item.price * item.qty, 0)
  );

  // Calculate the shipping price
  state.shippingPrice = addDecimals(state.itemsPrice > 100 ? 0 : 10);

  // Calculate the tax price
  state.taxPrice = addDecimals(Number((0.15 * state.itemsPrice).toFixed(2)));

  // Calculate the total price
  state.totalPrice = (
    Number(state.itemsPrice) +
    Number(state.shippingPrice) +
    Number(state.taxPrice)
  ).toFixed(2);

  // Save the cart to localStorage
  localStorage.setItem('cart', JSON.stringify(state));

  return state;
};
```

Now go back to the `cartSlice.js` file and import the `updateCart` function:

```js
import { updateCart } from '../utils/cartUtils';
```

And now your slice should look like this:

```js
const cartSlice = createSlice({
  name: 'cart',
  initialState,
  reducers: {
    addToCart: (state, action) => {
      // The item to add to the cart
      const item = action.payload;

      // Check if the item is already in the cart
      const existItem = state.cartItems.find((x) => x._id === item._id);

      if (existItem) {
        // If exists, update quantity
        state.cartItems = state.cartItems.map((x) =>
          x._id === existItem._id ? item : x
        );
      } else {
        // If not exists, add new item to cartItems
        state.cartItems = [...state.cartItems, item];
      }

      // Update the prices and save to storage
      return updateCart(state, item);
    }
});
```

Remove the items from your cart using the devtools and then add some items to your cart. You should see the same results as before.
