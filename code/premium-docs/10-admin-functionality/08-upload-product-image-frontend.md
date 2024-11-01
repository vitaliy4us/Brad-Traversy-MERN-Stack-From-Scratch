# Upload Product Image - Frontend

We have the backend setup to upload images. Now we need to add the functionality to the frontend.

Open the `frontend/src/slices/productsApiSlices.js` file and add the following mutation:

```js
 uploadProductImage: builder.mutation({
  query: (data) => ({
    url: `/api/upload`,
    method: 'POST',
    body: data,
  }),
}),
```

Export it:

```js
export const {
  useGetProductsQuery,
  useGetProductDetailsQuery,
  useCreateProductMutation,
  useUpdateProductMutation,
  useUploadProductImageMutation,
} = productsApiSlice;
```

## Product Edit Screen

Now open the `frontend/src/screens/admin/ProductEditScreen.js` file and imoport the mutation:

```js
import {
  useGetProductDetailsQuery,
  useUpdateProductMutation,
  useUploadProductImageMutation,
} from '../slices/productsApiSlice';
```

Add the following under the `useUpdateProductMutation` hook:

```js
const [uploadProductImage, { isLoading: loadingUpload }] =
  useUploadProductImageMutation();
```

Define the `uploadFileHandler` function right above the return statement:

```js
const uploadFileHandler = async (e) => {
  const formData = new FormData();
  formData.append('image', e.target.files[0]);
  try {
    const res = await uploadProductImage(formData).unwrap();
    toast.success(res.message);
    setImage(res.image);
  } catch (err) {
    toast.error(err?.data?.message || err.error);
  }
};
```

We are creating a new `FormData` object and appending the image to it. Then we are dispatching the `uploadProductImage` action and unwrapping the response. If the response is successful, we are setting the image in the state and displaying a success message. If there is an error, we are displaying an error message.

Now, we can replace the placeholder for the image upload in the returned JSX:

```js
<Form.Group controlId='image'>
  <Form.Label>Image</Form.Label>
  <Form.Control
    type='text'
    placeholder='Enter image url'
    value={image}
    onChange={(e) => setImage(e.target.value)}
  ></Form.Control>
  <Form.Control
    label='Choose File'
    onChange={uploadFileHandler}
    type='file'
  ></Form.Control>
  {loadingUpload && <Loader />}
</Form.Group>
```

Now you can Choose and image and upload it. It should then show on the product list and product details page.
