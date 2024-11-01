# Modeling Our Data

MongoDB is a document database, which means it stores data in JSON-like documents. Unlike relational databases, where you have to strictly define your data on the database level, with MongoDB, we don't have to do that, but we do want to do some modeling at the application level to make sure that our data is consistent and valid.

We are going to create a schema and a model for our products using Mongoose.

We are just going to get this out of the way and model all of our data including users, products, reviews and orders. We will be using the same approach for all of our data.

## Modeling Products

Create a new folder called `models` in the `backend` folder and create a file called `productModel.js` in it.

## Create a Schema

A schema is a blueprint for our data. It defines the structure of our data and the rules that it has to follow. We can use schemas to validate our data and make sure that it is consistent.

Add the following code:

```js
import mongoose from 'mongoose';

const productSchema = mongoose.Schema(
  {
    user: {
      type: mongoose.Schema.Types.ObjectId,
      required: true,
      ref: 'User',
    },
    name: {
      type: String,
      required: true,
    },
    image: {
      type: String,
      required: true,
    },
    brand: {
      type: String,
      required: true,
    },
    category: {
      type: String,
      required: true,
    },
    description: {
      type: String,
      required: true,
    },
    reviews: [reviewSchema],
    rating: {
      type: Number,
      required: true,
      default: 0,
    },
    numReviews: {
      type: Number,
      required: true,
      default: 0,
    },
    price: {
      type: Number,
      required: true,
      default: 0,
    },
    countInStock: {
      type: Number,
      required: true,
      default: 0,
    },
  },
  {
    timestamps: true,
  }
);

const Product = mongoose.model('Product', productSchema);

export default Product;
```

We are using the `mongoose.Schema` method to create a schema. We are passing in an object that defines the structure of our data. We are also passing in an object with the `timestamps` option set to `true`. This will automatically add the `createdAt` and `updatedAt` fields to our schema.

I just want to mention that the `_id` field is automatically added to our schema. It is the primary key for our data. We don't have to define it.

We set a user field, which will be the user that created the product. We are going to use the `mongoose.Schema.Types.ObjectId` type for this field. This will allow us to reference a user from the `User` model. We are also setting the `ref` option to `User`, which will tell Mongoose that this field references the `User` model.

You can see all of the fields like `name`, `image`, `brand`, `category` etc. These are the fields that we want to have in our product. We are also defining the type of each field. For example, the `name` field is a string, the `price` field is a number, etc.

For reviews, we are going to create a separate schema and then add it to the product schema. Reviews will be its own array of review objects. It will also have a relationship with the `User` model, so we will need to reference it.

Let's create the reviews schema right above the product schema:

```js
const reviewSchema = mongoose.Schema(
  {
    name: { type: String, required: true },
    rating: { type: Number, required: true },
    comment: { type: String, required: true },
    user: {
      type: mongoose.Schema.Types.ObjectId,
      required: true,
      ref: 'User',
    },
  },
  {
    timestamps: true,
  }
);
```

## Modeling Users

So we have products and reviews modeled and we have a user field pointing to the `User` model. So let's create that next.

Create a new file called `userModel.js` in the `models` folder and add the following code:

```js
import mongoose from 'mongoose';

const userSchema = mongoose.Schema(
  {
    name: {
      type: String,
      required: true,
    },
    email: {
      type: String,
      required: true,
      unique: true,
    },
    password: {
      type: String,
      required: true,
    },
    isAdmin: {
      type: Boolean,
      required: true,
      default: false,
    },
  },
  {
    timestamps: true,
  }
);

const User = mongoose.model('User', userSchema);

export default User;
```

Users will have a name, email, password and an `isAdmin` field. We are also setting the `timestamps` option to `true` so that we can get the `createdAt` and `updatedAt` fields.

## Modeling Orders

Now we will do the orders model. Create a new file called `orderModel.js` in the `models` folder and add the following code:

```js
import mongoose from 'mongoose';

const orderSchema = mongoose.Schema(
  {
    user: {
      type: mongoose.Schema.Types.ObjectId,
      required: true,
      ref: 'User',
    },
    orderItems: [
      {
        name: { type: String, required: true },
        qty: { type: Number, required: true },
        image: { type: String, required: true },
        price: { type: Number, required: true },
        product: {
          type: mongoose.Schema.Types.ObjectId,
          required: true,
          ref: 'Product',
        },
      },
    ],
    shippingAddress: {
      address: { type: String, required: true },
      city: { type: String, required: true },
      postalCode: { type: String, required: true },
      country: { type: String, required: true },
    },
    paymentMethod: {
      type: String,
      required: true,
    },
    paymentResult: {
      id: { type: String },
      status: { type: String },
      update_time: { type: String },
      email_address: { type: String },
    },
    itemsPrice: {
      type: Number,
      required: true,
      default: 0.0,
    },
    taxPrice: {
      type: Number,
      required: true,
      default: 0.0,
    },
    shippingPrice: {
      type: Number,
      required: true,
      default: 0.0,
    },
    totalPrice: {
      type: Number,
      required: true,
      default: 0.0,
    },
    isPaid: {
      type: Boolean,
      required: true,
      default: false,
    },
    paidAt: {
      type: Date,
    },
    isDelivered: {
      type: Boolean,
      required: true,
      default: false,
    },
    deliveredAt: {
      type: Date,
    },
  },
  {
    timestamps: true,
  }
);

const Order = mongoose.model('Order', orderSchema);

export default Order;
```

The order schema is quite large. It includes all of the fields that you would expect an order to have such as the user, order items, shipping address, payment method, payment result, price, etc. We will get more into how this data is used later on.
