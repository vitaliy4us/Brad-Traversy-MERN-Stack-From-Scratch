# Delete Products

We can add, edit and upload images for products. Now we need the ability to delete products.

## Backend Route & Controller Function

First step is to add the backend functionality. Let's go into the `backend/controllers/productController.js` file and add a new function called `deleteProduct` and export it:

```js
// @desc    Delete a product
// @route   DELETE /api/products/:id
// @access  Private/Admin
const deleteProduct = asyncHandler(async (req, res) => {
  const product = await Product.findById(req.params.id);

  if (product) {
    await Product.deleteOne({ _id: product._id });
    res.json({ message: 'Product removed' });
  } else {
    res.status(404);
    throw new Error('Product not found');
  }
});

export {
  getProducts,
  getProductById,
  createProduct,
  updateProduct,
  deleteProduct,
};
```

Now, add the route in the `backend/routes/productRoutes.js` file:

```js
router
  .route('/:id')
  .get(getProductById)
  .put(protect, admin, updateProduct)
  .delete(protect, admin, deleteProduct);
```

## Frontend

Go into the frontend `src/slices/productsApiSlice.js` file and add the delete handler and export it:

```js
deleteProduct: builder.mutation({
  query: (productId) => ({
    url: `${PRODUCTS_URL}/${productId}`,
    method: 'DELETE',
  }),
}),
```

```js
export const {
  useGetProductsQuery,
  useGetProductDetailsQuery,
  useCreateProductMutation,
  useUpdateProductMutation,
  useUploadProductImageMutation,
  useDeleteProductMutation,
} = productsApiSlice;
```

## Product List Screen

Now open the `frontend/src/screens/admin/ProductListScreen.js` file and add import the mutation:

```js
import {
  useGetProductsQuery,
  useDeleteProductMutation,
  useCreateProductMutation,
} from '../../slices/productsApiSlice';
```

Initialize the mutation:

```js
const [deleteProduct, { isLoading: loadingDelete }] =
  useDeleteProductMutation();
```

Add the delete handler:

```js
const deleteHandler = async (id) => {
  if (window.confirm('Are you sure')) {
    try {
      await deleteProduct(id);
      refetch();
    } catch (err) {
      toast.error(err?.data?.message || err.error);
    }
  }
};
```

We are using the `refetch` function to refetch the products list after deleting a product.

Now, just add the loadingDelete right under the `loadingCreate` in the JSX. It should look like this:

```jsx
{
  loadingCreate && <Loader />;
}
{
  loadingDelete && <Loader />;
}
```
