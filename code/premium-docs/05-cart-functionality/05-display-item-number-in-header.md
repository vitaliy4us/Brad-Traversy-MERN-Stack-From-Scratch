# Display the Item Number in Header

Now, we are going to show the number of items in our cart in the header.

We need to bring in the cart state from the Redux store. We will use the `useSelector` hook to do this.

Open the `components/Header.js` file and add the following:

```js
import { useSelector } from 'react-redux';
```

Now, we need to get the cart state from the Redux store. We will use the `useSelector` hook to do this. Add this right above the return statement:

```js
const { cartItems } = useSelector((state) => state.cart);
```

Find this part of the JSX:

```js
<Nav.Link>
  <FaShoppingCart /> Cart
</Nav.Link>
```

and add this above the ending `</Nav.Link>`:

```js
{
  cartItems.length > 0 && (
    <Badge pill bg='success' style={{ marginLeft: '5px' }}>
      {cartItems.reduce((a, c) => a + c.qty, 0)}
    </Badge>
  );
}
```

Now, you should see the number of items in your cart in the header.
