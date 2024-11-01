# Custom Error Middleware

The next thing that I want to do is create some custom middleware to handle errors better. Spcifically 404 and 500 errors.

Let's start by creating a new file in the `middleware` folder called `errorMiddleware.js`.

Add the following code:

```js
const notFound = (req, res, next) => {
  const error = new Error(`Not Found - ${req.originalUrl}`);
  res.status(404);
  next(error);
};
```

The `notFound` middleware will be called if no other middleware has handled the request. It will create a new error object and set the status code to 404 with a message. It will then pass the error to the next middleware.

Under that function, let's create another function called `errorHandler`:

```js
const errorHandler = (err, req, res, next) => {
  let statusCode = res.statusCode === 200 ? 500 : res.statusCode;
  let message = err.message;

  // If Mongoose not found error, set to 404 and change message
  if (err.name === 'CastError' && err.kind === 'ObjectId') {
    statusCode = 404;
    message = 'Resource not found';
  }

  res.status(statusCode).json({
    message: message,
    stack: process.env.NODE_ENV === 'production' ? null : err.stack,
  });
};
```

This middleware will be called if any other middleware throws an error. It will check the status code and set it to 500 if it is 200. It will then check the error name and kind to see if it is a CastError. If it is, it will set the status code to 404 and the message to `Resource not found`. It will then send the status code, message, and stack trace to the client.

Finally, let's export both of these functions:

```js
export { notFound, errorHandler };
```

Now let's import these functions into our `server.js` file and add them to our middleware stack.

```js
import { notFound, errorHandler } from './middleware/errorMiddleware.js';
```

Then add them to the middleware stack.

```js
app.use(notFound);
app.use(errorHandler);
```

Now, if you visit ANY route that doesn't exist, you will get a 404 error with a message. Try going to `http://localhost:5000/test` and you will see the following:

```json
{
  "message": "Not Found - /test",
  "stack": YOUR STACK TRACE
}
```

## Throw Errors

Now let's throw an error in our `productRoutes.js` file. Add the following code to the `getProductById` function:

```js
if (product) {
  res.json(product);
} else {
  res.status(404);
  throw new Error('Resource not found');
}
```

Now, go to http://localhost:5000/api/products/1 and you will see the following:

```json
{
  "message": "Product not found",
  "stack": YOUR STACK TRACE
}
```

So from now on, when you want to throw an error, you can just use `throw new Error('Your error message')` and it will be handled by the `errorHandler` middleware.
