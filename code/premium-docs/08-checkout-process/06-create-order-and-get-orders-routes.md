# Create Order & Get Orders Routes

In this lesson, we will add the functionality to create an order, get the current user's orders, and get an order by id.

First, let's work on the `addOrderItems` in `backend/controllers/orderController.js`.

This request will take quite a bit of data. We need the following:

- orderItems
- user
- shippingAddress
- paymentMethod
- itemsPrice
- taxPrice
- shippingPrice
- totalPrice

Let's add the code for this controller function:

```js
const {
  orderItems,
  shippingAddress,
  paymentMethod,
  itemsPrice,
  taxPrice,
  shippingPrice,
  totalPrice,
} = req.body;

if (orderItems && orderItems.length === 0) {
  res.status(400);
  throw new Error('No order items');
} else {
  const order = new Order({
    orderItems: orderItems.map((x) => ({
      ...x,
      product: x._id,
      _id: undefined,
    })),
    user: req.user._id,
    shippingAddress,
    paymentMethod,
    itemsPrice,
    taxPrice,
    shippingPrice,
    totalPrice,
  });

  const createdOrder = await order.save();

  res.status(201).json(createdOrder);
}
```

First, we are getting all of the data from the request body. Then, we are checking to see if there are any order items. If there are no order items, we are throwing an error. If there are order items, we are creating a new order and saving it to the database.

For the `orderItems` property, we are mapping over the order items and creating a new object for each order item. We are using the spread operator to copy all of the properties from the order item. We are also setting the `product` property to the `_id` property of the order item. This is because in the `Order` model, the `product` property is a reference to the product id.

The user is coming from the auth middleware. We are setting the `user` property to the `_id` property of the user.

## Get My Orders

Now in the `getMyOrders` function, add the following:

```js
const getMyOrders = asyncHandler(async (req, res) => {
  const orders = await Order.find({ user: req.user._id });
  res.json(orders);
});
```

This is simple, we are getting all orders using the user id from the auth middleware.

## Get Order by ID

Now in the `getOrderById` function, add the following:

```js
const getOrderById = asyncHandler(async (req, res) => {
  const order = await Order.findById(req.params.id).populate(
    'user',
    'name email'
  );

  if (order) {
    res.json(order);
  } else {
    res.status(404);
    throw new Error('Order not found');
  }
});
```

We are using the `populate` method to get the user's name and email. We are also checking to see if the order exists. If it does, we are returning the order. If it doesn't, we are throwing an error.

In the next couple lessons, we will handle the frontend functionality for creating an order.
