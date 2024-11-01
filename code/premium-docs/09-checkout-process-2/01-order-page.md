# Get Order Details

So up to this point, we have created the order model, the order controller, and the order routes. We have also created the `createOrder` and `getMyOrders` functions in the order controller. Now, we are going to fetch the order details and create the order screen.

Let's start in the `ordersApiSlice.js` file. We are going to create a new slice for the order details. Add the following:

```js
  getOrderDetails: builder.query({
      query: (id) => ({
        url: `${ORDERS_URL}/${id}`,
      }),
      keepUnusedDataFor: 5,
    }),
```

Export it:

```js
export const { useCreateOrderMutation, useGetOrderDetailsQuery } =
  ordersApiSlice;
```

We are simply using the get single order route that we created in the order routes file. We will cache the data for 5 requests.

## Start Order Screen

Now, we want to start to create the order screen. We will start by creating the order screen component. Create a new file in the `src/screens` folder called `OrderScreen.js`. Add the following:

```js
import { Link, useParams } from 'react-router-dom';
import { Row, Col, ListGroup, Image, Card } from 'react-bootstrap';
import Message from '../components/Message';
import Loader from '../components/Loader';
import { useGetOrderDetailsQuery } from '../slices/ordersApiSlice';

const OrderScreen = () => {
  const { id: orderId } = useParams();

  const {
    data: order,
    refetch,
    isLoading,
    error,
  } = useGetOrderDetailsQuery(orderId);

  return isLoading ? (
    <Loader />
  ) : error ? (
    <Message variant='danger'>{error}</Message>
  ) : (
    <>
      <h1>Order {order._id}</h1>
      <Row>
        <Col md={8}>Column 1</Col>
        <Col md={4}>Column 2</Col>
      </Row>
    </>
  );
};

export default OrderScreen;
```

Here, we are using the `useGetOrderDetailsQuery` hook to fetch the order details as well as the loading and error state. `refetch` is something we will use in a later lesson. We are also using the `useParams` hook to get the order id from the URL.

Let's work on the first column. Add the following in place of `Column 1`:

```js
<ListGroup variant='flush'>
  <ListGroup.Item>
    <h2>Shipping</h2>
    <p>
      <strong>Name: </strong> {order.user.name}
    </p>
    <p>
      <strong>Email: </strong>{' '}
      <a href={`mailto:${order.user.email}`}>{order.user.email}</a>
    </p>
    <p>
      <strong>Address:</strong>
      {order.shippingAddress.address}, {order.shippingAddress.city} {
        order.shippingAddress.postalCode
      }, {order.shippingAddress.country}
    </p>
    {order.isDelivered ? (
      <Message variant='success'>Delivered on {order.deliveredAt}</Message>
    ) : (
      <Message variant='danger'>Not Delivered</Message>
    )}
  </ListGroup.Item>

  <ListGroup.Item>
    <h2>Payment Method</h2>
    <p>
      <strong>Method: </strong>
      {order.paymentMethod}
    </p>
    {order.isPaid ? (
      <Message variant='success'>Paid on {order.paidAt}</Message>
    ) : (
      <Message variant='danger'>Not Paid</Message>
    )}
  </ListGroup.Item>

  <ListGroup.Item>
    <h2>Order Items</h2>
    {order.orderItems.length === 0 ? (
      <Message>Order is empty</Message>
    ) : (
      <ListGroup variant='flush'>
        {order.orderItems.map((item, index) => (
          <ListGroup.Item key={index}>
            <Row>
              <Col md={1}>
                <Image src={item.image} alt={item.name} fluid rounded />
              </Col>
              <Col>
                <Link to={`/product/${item.product}`}>{item.name}</Link>
              </Col>
              <Col md={4}>
                {item.qty} x ${item.price} = ${item.qty * item.price}
              </Col>
            </Row>
          </ListGroup.Item>
        ))}
      </ListGroup>
    )}
  </ListGroup.Item>
</ListGroup>
```

We are displaying some basic order info as well as the status of `isPaid` and `isDelivered`. We are also displaying the order items.

Now, fill in the second column with the following:

```js
<Card>
  <ListGroup variant='flush'>
    <ListGroup.Item>
      <h2>Order Summary</h2>
    </ListGroup.Item>
    <ListGroup.Item>
      <Row>
        <Col>Items</Col>
        <Col>${order.itemsPrice}</Col>
      </Row>
    </ListGroup.Item>
    <ListGroup.Item>
      <Row>
        <Col>Shipping</Col>
        <Col>${order.shippingPrice}</Col>
      </Row>
    </ListGroup.Item>
    <ListGroup.Item>
      <Row>
        <Col>Tax</Col>
        <Col>${order.taxPrice}</Col>
      </Row>
    </ListGroup.Item>
    <ListGroup.Item>
      <Row>
        <Col>Total</Col>
        <Col>${order.totalPrice}</Col>
      </Row>
    </ListGroup.Item>
    {/* PAY ORDER PLACEHOLDER */}
    {/* {MARK AS DELIVERED PLACEHOLDER} */}
  </ListGroup>
</Card>
```

Here, we add some pricing info and a placeholder for the 'pay order' button and the admin's 'mark as delivered' button.
