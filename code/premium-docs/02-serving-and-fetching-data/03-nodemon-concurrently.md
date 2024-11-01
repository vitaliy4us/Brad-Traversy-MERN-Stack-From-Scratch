# Nodemon & Concurrently

Right now, we are able to run our server with `npm start` but we have to manually restart the server every time we make a change. We can use a package called `nodemon` to automatically restart the server when we make a change. We can also use a package called `concurrently` to run both the frontend and the backend at the same time.

Let's install both of these packages as dev dependencies:

```bash
npm install -D nodemon concurrently
```

## Nodemon

Now we can add a new script to our `package.json` file:

```json
"scripts": {
  "start": "node backend/server.js",
  "server": "nodemon backend/server.js"
},
```

Now we can run `npm run server` to start the server using Nodemon. So we will not have to manually restart the server every time we make a change.

## Concurrently

I want to be able to run `npm run dev` and have both our React dev server run as well as the backend. We can do that with `concurrently`. Let's add a new script to our `package.json` file that will run the client/react .

```json
"scripts": {
  "start": "node backend/server.js",
  "server": "nodemon backend/server.js",
  "client": "npm start --prefix frontend",
},
```

Adding `--prefix frontend` will run the client side `start` script.

Now we have a script to run the client and a script to run the server. We can use `concurrently` to run both of these scripts at the same time. Let's add a new script to our `package.json` file:

```json
"scripts": {
  "start": "node backend/server.js",
  "server": "nodemon backend/server.js",
  "client": "npm start --prefix frontend",
  "dev": "concurrently \"npm run server\" \"npm run client\""
},
```

Now stop your React server if it's still running. Then run `npm run dev` from the root and you should see both the React server and the backend server running.

You can test this by going to https://localhost:3000 to see your frontend and `http://localhost:5000/api/products` to see the products.
