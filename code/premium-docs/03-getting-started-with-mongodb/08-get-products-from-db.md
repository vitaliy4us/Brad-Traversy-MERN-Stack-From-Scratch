# Get Products From Database

So right now, we are fetching the products from the server, however, they are just stored in a file. We want to get them from the database. We already have some sample data in there, so let's go ahead and get that data.

## Product Routes

We don't want to keep our product routes in the `server.js` file. So let's create a new folder in the `backend` folder called `routes`. Inside of that folder, create a new file called `productRoutes.js`. This is where we will put all of our product routes.

In order to use the router, we need to import it from express. So let's go ahead and do that.

```js
const express = require('express');
const router = express.Router();
```

Now, let's copy the product routes from `server.js` and paste them into `productRoutes.js`.

```js
app.get('/api/products', (req, res) => {
  res.json(products);
});

app.get('/api/products/:id', (req, res) => {
  const product = products.find((p) => p._id === req.params.id);
  res.json(product);
});
```

There are a few things thay we need to change.

1. We need to change the `app` to `router`.
2. We need to export the router.
3. Since we are connecting the endpoint of `/api/products` to the router, we don't need to include that in the url.

So, it should look like this.

```js
router.get('/', (req, res) => {
  res.json(products);
});

router.get('/:id', (req, res) => {
  const product = products.find((p) => p._id === req.params.id);
  res.json(product);
});

export default router;
```

Let's move the import of the `products.js` file to the top of the file. We need to add `../` since we are now one level deeper in the file structure.

```js
import products from '../data/products.js';
```

We will delete this soon as we will be getting the products from the database.

## Linking Routes

Now that we have our routes in a separate file, we need to link them to our `server.js` file. We can do this by importing the routes and using them.

```js
import productRoutes from './routes/productRoutes.js';
```

```js
app.use('/api/products', productRoutes);
```

Let's test this out by starting the server and going to `http://localhost:5000/api/products` in either your browser, Postman, CURL or any other HTTP client. You should see the products.

## Async Middleware

One thing I want to do before using Mongoose to fetch the products is create a middleware function that will wrap our async functions. This will allow us to handle errors better while using async/await. We could install a package to do this called `express-async-handler` or we could just create our own piece of middleware for this, which is simple and only a couple lines of code.

Create a new folder called `middleware` in the `backend` folder. Inside of that folder, create a new file called `asyncHandler.js`. This is where we will put our async handler.

```js
const asyncHandler = (fn) => (req, res, next) =>
  Promise.resolve(fn(req, res, next)).catch(next);

export default asyncHandler;
```

The function returns a Promise that resolves with the result of calling fn(req, res, next) (i.e., the result of the asynchronous operation). If the Promise is rejected (i.e., the asynchronous operation throws an error), the error is caught and passed to the next middleware function using catch(next). This allows the error to be handled by Express's error-handling middleware.

Now, we can import this into our `productRoutes.js` file along with the `Product` model.

```js
import asyncHandler from '../middleware/asyncHandler.js';
import Product from '../models/productModel.js';
```

Get rid of the import of the `data/products.js` file. We don't need that anymore. Keep the data files though because they are being used with the seeder.

To use the async handler, we need to wrap our async functions in it. So let's go ahead and do that and use the `Product` model and the Mongoose `find()` method to get the products from the database.

```js
router.get(
  '/',
  asyncHandler(async (req, res) => {
    const products = await Product.find({});
    res.json(products);
  })
);
```

We also want to get a single product. So let's go ahead and do that by using the `findById()` method.

```js
router.get(
  '/:id',
  asyncHandler(async (req, res) => {
    const product = await Product.findById(req.params.id);
    if (product) {
      res.json(product);
    }
    res.status(404).json({ message: 'Product not found' });
  })
);
```

Let's test this out by starting the server and going to `http://localhost:5000/api/products`. You should see the products.

Now copy one of the ids and go to `http://localhost:5000/api/products/<id>`. You should see the product with that id.

Right now, if you go to a product that doesn't exist, you will get a mongoose error. We will fix that and create our own error handler soon.

At leat for now, we are able to get the products from the database.
