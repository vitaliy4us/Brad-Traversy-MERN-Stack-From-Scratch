# Add To Cart Reducer

We are now going to add the reducer function to add to cart. Open the `/slices/cartSlice.js` file.

Before we add the reducer, let's create a simple helper function to add the correct number of decimals (2) to the prices:

```js
const addDecimals = (num) => {
  return (Math.round(num * 100) / 100).toFixed(2);
};
```

Now, let's add the `addToCart` reducer. Right now, we will just stick all of the code in this file, but later we will move it to a separate file. I will comment the code below so that you know exactly what we are doing. Add the following to the `cartSlice`:

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

      // Calculate the items price
      state.itemsPrice = addDecimals(
        state.cartItems.reduce((acc, item) => acc + item.price * item.qty, 0)
      );

      // Calculate the shipping price | If items price is greater than 100, shipping is free | If not, shipping is 10
      state.shippingPrice = addDecimals(state.itemsPrice > 100 ? 0 : 10);

      // Calculate the tax price | Tax is 15% of the items price
      state.taxPrice = addDecimals(
        Number((0.15 * state.itemsPrice).toFixed(2))
      );

      // Calculate the total price | Total price is the sum of the items price, shipping price and tax price
      state.totalPrice = (
        Number(state.itemsPrice) +
        Number(state.shippingPrice) +
        Number(state.taxPrice)
      ).toFixed(2);

      // Save the cart to localStorage
      localStorage.setItem('cart', JSON.stringify(state));
    },
  },
});
```

Now, we just need to export the `addToCart` action creator. Add the following to the bottom of the file:

```js
export const { addToCart } = cartSlice.actions;
```

This may seem a bit confusing because `addToCart` is being referenced as both a reducer and an action creator. This is because `createSlice` automatically creates an action creator for each reducer. You can read more about this [here](https://redux-toolkit.js.org/api/createSlice#returns).

`addToCart` is a reducer function that takes the current state and an action as arguments, and returns the updated state. It is responsible for updating the state based on the action's payload.

When you export the `addToCart` function using `export const { addToCart } = cartSlice.actions;`, you're exporting the auto-generated action creator that corresponds to the addToCart reducer.

An action creator is a function that creates and returns an action object with the appropriate action type and payload. The action creator is used to dispatch actions to the store.

In the next lesson, we will use the addToCart action creator from within the `ProductScreen` component.
