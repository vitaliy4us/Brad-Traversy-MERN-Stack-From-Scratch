# Paginate Products

We are going to add pagination to our products both on the home screen and the admin products list screen. If you want to add pagination to other screens, you can follow the same steps, but I'll just be adding it to the products for now.

## Backend

In order to add pagination, we need to edit some code in our backend.

Open the `getProducts` function in `productController.js` and replace it with this code:

```js
const getProducts = asyncHandler(async (req, res) => {
  const pageSize = 4;
  const page = Number(req.query.pageNumber) || 1;

  const count = await Product.countDocuments();
  const products = await Product.find()
    .limit(pageSize)
    .skip(pageSize * (page - 1));

  res.json({ products, page, pages: Math.ceil(count / pageSize) });
});
```

First, we are setting the `pageSize` to 4. This means that we want to show 4 products per page. You probably want to change this to something higher, but just for now, so we can test, we want it to be a low number.

Then, we are getting the page number from the query string. If the page number is not specified, we are setting it to 1.

Then, we are getting the total number of products in our database. We are using the `countDocuments` method for this.

Then, we are getting the products from the database. We are using the `find` method to get all the products. We are also using the `limit` method to limit the number of products to 4. We are also using the `skip` method to skip the first 4 products if we are on page 2, or the first 8 products if we are on page 3, and so on.

Finally, we are sending the products, the current page number, and the total number of pages to the frontend.

## Frontend

If you reload the frontend, you will see that it breaks the app. This is because, we are no longer returning only the products from the backend. We are also returning the page number and the total number of pages.

We can fix this by opening the `frontend/screens/HomeScreen.js` and changing this:

```js
const { data: products, isLoading, error } = useGetProductsQuery();
```

To this:

```js
const { data, isLoading, error } = useGetProductsQuery();
```

Because now we are getting the `data` object from the `useGetProductsQuery` hook. This object contains the `products`, `page`, and `pages` properties.

So where we are mapping over the `products` array, we need to change it to `data.products`.

```jsx
data.products.map((product) => (
  // ...
));
```

Now you should see the products on the home screen. You will only see 4 products, because we are only showing 4 products per page.

Let's first open the `frontend/src/slices/productsApiSlice.js` file and replace the `getProducts` function with this code:

```js
getProducts: builder.query({
  query: ({ pageNumber }) => ({
    url: PRODUCTS_URL,
      params: { pageNumber },
  }),
  keepUnusedDataFor: 5,
}),
```

It will once again break, because we are not passing the `pageNumber` parameter to the `getProducts` function.

Back in the `frontend/screens/HomeScreen.js` file, we need to add the `pageNumber` parameter to the `getProducts` function.

```jsx
import { useParams } from 'react-router-dom';

const { pageNumber } = useParams();

const { data, isLoading, error } = useGetProductsQuery({
  pageNumber,
});
```

Now, we are sending the `pageNumber` parameter to the `getProducts` function.

In order to get the pageNumber from the params, we need to add another route. Open the `frontend/src/index.js` file and add this route:
right under the other index route:

```jsx
<Route path='/page/:pageNumber' element={<HomeScreen />} />
```

Now go to `http://localhost:3000/page/2` and you should see the second page of products.

## Admin Products

If you log in as admin and go to the products list, it will also be broken because it uses the same endpoint as the frontend.

Open the `frontend/screens/admin/ProductListScreen.js` file and add this code. You don't need to add the comments:

```jsx
import { useParams } from 'react-router-dom'

 const { pageNumber } = useParams()

// Remove the ': products' from data: products and add pageNumber
 const { data, isLoading, error, refetch } = useGetProductsQuery({
    pageNumber,
  })

// Use data.products.map
data.products.map((product) => (
  // ...
));
```

Now the page should show the first 4 products.

Add the route for admin products with a page number:

```jsx
<Route path='/productlist/:pageNumber' element={<ProductListScreen />} />
```

Make sure it is in the admin routes right under the other product list route.

Now, try to go to `http://localhost:3000/admin/productlist/2` and you should see the second page of products.

In the next lesson, we will create the pagination component to navigate.
