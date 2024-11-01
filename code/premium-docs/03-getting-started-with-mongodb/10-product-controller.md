# Product Controller

Express is a very un-opinionated framework. We can continue to write our code within the route files, but it is best practice to separate our code into controllers. This will help keep our code clean and organized.

Let's create a new folder called `controllers` and create a new file called `productController.js` inside of it.

Let's cut the imports of the `asyncHandler` and the Product model in the `productRoutes.js` file and paste them into the `productController.js` file.

```js
import asyncHandler from '../middleware/asyncHandler.js';
import Product from '../models/productModel.js';
```

Now we will create a function called `getProducts` and a functioncalled `getProductById` and add the code from the `productRoutes.js` file into the functions. It should look like this:

```js
// @desc    Fetch all products
// @route   GET /api/products
// @access  Public
const getProducts = asyncHandler(async (req, res) => {
  const products = await Product.find({});
  res.json(products);
});

// @desc    Fetch single product
// @route   GET /api/products/:id
// @access  Public
const getProductById = asyncHandler(async (req, res) => {
  const product = await Product.findById(req.params.id);
  if (product) {
    return res.json(product);
  }
  res.status(404);
  throw new Error('Resource not found');
});

export { getProducts, getProductById };
```

The comments are optional, but it is something that I like to do. It helps me keep track of what each function does as well as what the route is and what the access is.

Now let's import the functions into the `productRoutes.js` file and add them to the routes.

```js
import {
  getProducts,
  getProductById,
} from '../controllers/productController.js';
```

Then add the functions to the routes.

```js
router.route('/').get(getProducts);
router.route('/:id').get(getProductById);
```

Now, check to see if everything is working by fetching the products and fetching a single product.

Now, all of our code is separated into different files. This will help keep our code clean and organized.
