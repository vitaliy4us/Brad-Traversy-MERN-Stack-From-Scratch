# Express Server Setup

Now we are going to start on the back-end, which will be our Express server. Right now, I just want to get a simple server up and running.

We will have a `backend` folder with all of our routes, models and controllers but the backend dependencies and `package.json` will be in the root of the project. You can leave the React dev server running. Just open a second terminal in the root and run the following command:

```bash
npm init
```

Go through the questions. You can answer the default for most, but for the entry point, you can use `server.js` because that is what I will use as the server entry point. I am also entering `2.0.0` for the version, since this is the second version of the project and course.

## ES Modules in Node

This is preference, but I want to use ES modules in Node. I like to use ES modules because I think they are easier to read and write and of course, that is what we are using in the frontend.

In order to use ES Modules, we need to open up the `package.json` file and add the following line:

```json
"type": "module",
```

We also need to add a script to our `package.json` file to run our server. Add the following line:

```json
"scripts": {
  "start": "node backend/server.js"
},
```

## Install Express

Now we can install Express. Make sure that you are in the root folder of the project. Run the following command:

```bash
npm install express
```

Create a new folder in the root called `backend`. Then create a new file called `server.js` in the `backend` folder. For now, let's just do a `console.log('Hello World')` in the `server.js` file. Then run the following command from the root of the project:

```bash
npm start
```

You should see `Hello World` in the terminal.

## Dummy Data

Ultimatley, our data will come from a database but for now, we will serve it from the backend, but it will just be stored in a file. So let's copy the `products.js` folder that we have in the frontend and add it to a folder called `data` in the backend.

The reason that I am copying the data file and not just moving it is because it will break the frontend at the moment. We will remove it in a bit.

In the `server.js` file, import the data file and log it to the console.

```js
import products from './data/products.js';

console.log(products);
```

## Setup Express

Now, let's setup a simple Express server:

```js
import express from 'express';
import products from './data/products.js';
const port = process.env.PORT || 5000;

const app = express();

app.get('/', (req, res) => {
  res.send('API is running...');
});

app.listen(port, () => console.log(`Server running on port ${port}`));
```

We initialized Express and listened on a port. I set the port to first check the environment variable for the port and if it is not there, it will use port 5000.

Then we created a simple route for the root (/) that just gives us a message. Routes take in a path and a callback function. The callback function takes in the request and response objects. We can use the response object to send a response back to the client.

Now, open your browser and go to `http://localhost:5000`. You should see the message `API is running...`.

## Products Route

Now, let's add a route to get all of the products. We will use the `products` array that we imported from the data file. Add this line ABOVE the route for the root path:

```js
app.get('/api/products', (req, res) => {
  res.json(products);
});
```

Here we are using the `json` method to send a JSON response. We can also use the `send` method to send a string response.

You will have to restart the server to see the changes. Now, go to `http://localhost:5000/api/products`. You should see the products array.

## Single Product Route

We will also add a route to get a single product. We will use the `id` of the product to get it.

```js
app.get('/api/products/:id', (req, res) => {
  const product = products.find((p) => p._id === req.params.id);
  res.json(product);
});
```

Here we are passing in a parameter to the route. We can access the parameter in the request object with `req.params.id`. We are using the `find` method to find the product with the id that matches the parameter.

You will have to restart the server to see the changes. Now, go to `http://localhost:5000/api/products/1`. You should see the product with the id of 1.
