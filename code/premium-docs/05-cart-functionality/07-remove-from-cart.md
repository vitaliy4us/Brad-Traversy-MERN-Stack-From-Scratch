# Remove Items From Cart

Now that we can see all of the items in the cart and add items to it, we want to be able to remove items. We already have the button in the UI, we just need to add the functionality.

Open the `slices/cartSlice.js` file. Add the `removeFromCart` reducer:

```js
removeFromCart: (state, action) => {
  // Filter out the item to remove from the cart
  state.cartItems = state.cartItems.filter((x) => x._id !== action.payload);

  // Update the prices and save to storage
  return updateCart(state);
};
```

As you can see, we are simply filtering out the item to remove from the cart. Then we are calling the `updateCart` function to update the prices and save the cart to localStorage.

Export the `removeFromCart` action:

```js
export const { addToCart, removeFromCart } = cartSlice.actions;
```

## Remove From Cart Handler

Now, back in the `CartScreen.js` file, we need to add the handler for the remove button. First, bring in the `removeFromCart` action:

```js
import { addToCart, removeFromCart } from '../slices/cartSlice';
```

Create the handler function:

```js
const removeFromCartHandler = (id) => {
  dispatch(removeFromCart(id));
};
```

Then, add the handler to the button:

```js
<Button
  type='button'
  variant='light'
  onClick={() => removeFromCartHandler(item._id)}
>
  <FaTrash />
</Button>
```

Now when you click the trash icon, the item will be removed from the cart.

## Checkout Handler

The last thing I want to do here is add the checkout handler, which will just redirect to the shipping screen. Create the handler:

```js
const checkoutHandler = () => {
  navigate('/login?redirect=/shipping');
};
```

Add the handler to the button:

```js
<Button
  type='button'
  className='btn-block'
  disabled={cartItems.length === 0}
  onClick={checkoutHandler}
>
  Proceed To Checkout
</Button>
```

Obviously, this page will not work, but at least we have the redirect. Ultimately, what will happen is if the user is not logged in, they will go to login and if they are, they will continue with the checkout process.
