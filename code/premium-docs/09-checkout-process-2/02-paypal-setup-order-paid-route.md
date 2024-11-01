# PayPal Account Setup & Order Paid Route

Now that we can see the order page when we checkout, we need to add the ability to pay for the order using PayPal.

## Order Paid Route

We are going to start with the backend in the `updateOrderToPaid` function in the `orderController.js` file. We need to add the following code:

```js
const updateOrderToPaid = asyncHandler(async (req, res) => {
  const order = await Order.findById(req.params.id);

  if (order) {
    order.isPaid = true;
    order.paidAt = Date.now();
    order.paymentResult = {
      id: req.body.id,
      status: req.body.status,
      update_time: req.body.update_time,
      email_address: req.body.payer.email_address,
    };

    const updatedOrder = await order.save();

    res.json(updatedOrder);
  } else {
    res.status(404);
    throw new Error('Order not found');
  }
});
```

We are getting the order from the database and then updating the `isPaid` property to `true`, the `paidAt` property to the current date and time, and the `paymentResult` property to the `id`, `status`, `update_time`, and `email_address` properties from the request body.

We are then saving the order to the database and sending the updated order back to the frontend.

## PayPal API Setup

Now we will start to implement the PayPal integration.

Go to the [PayPal Developer Dashboard](https://developer.paypal.com/developer/applications/) and log in with your PayPal account.

When in development, we are going to use something called a sandbox account. This is a test account that we can use to test our PayPal integration without actually using real money.

If you click on `Testing Tools->Sandbox Accounts`, you should see a default personal and a default business sandbox account. If you don't, then create a new personal and business account. Again, this is not a real PayPal account with access to real money.

<img src="./images/paypal1.png" width="500">

Now, click on `Apps & Credentials` and create a new app.

Give it a name, select "Merchant" as the type and select your business sandbox account. Then click `Create App`.

Copy your client ID and open the `.env` file in the root folder. Add your client ID to the `PAYPAL_CLIENT_ID` environment variable.

```js
PAYPAL_CLIENT_ID = YOUR_CLIENT_ID;
```

You can keep the rest of the PayPal stuff as the default options.

## Add PayPal Server Route

Now, we are going to add the PayPal route in our backend. Open up the `server.js` file and add the following code under where we have the routes mapped to the route files:

```js
app.get('/api/config/paypal', (req, res) =>
  res.send({ clientId: process.env.PAYPAL_CLIENT_ID })
);
```

In the next video, we will jump back into the frontend and implement the PayPal integration.
