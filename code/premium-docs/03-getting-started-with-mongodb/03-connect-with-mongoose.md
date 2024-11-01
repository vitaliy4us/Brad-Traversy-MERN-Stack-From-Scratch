# Connect With Mongoose

Now that we have a database in the cloud, we need to connect to it from our application. We will use [Mongoose](https://mongoosejs.com/) to connect to our database.

## Install Mongoose

First, we need to install Mongoose. Open up your terminal and run the following command:

```bash
npm i mongoose
```

## Connect to MongoDB

We are going to create a new folder in the backend called `config` and a new file called `db.js` inside of it. This file will contain the code to connect to our database.

```js
import mongoose from 'mongoose';

const connectDB = async () => {
  try {
    const conn = await mongoose.connect(process.env.MONGO_URI);
    console.log(`MongoDB Connected: ${conn.connection.host}`);
  } catch (error) {
    console.error(`Error: ${error.message}`);
    process.exit(1);
  }
};

export default connectDB;
```

The method `mongoose.connect()` returns a promise, so we need to use `async/await` to wait for the promise to resolve. We are also going to use `try/catch` to handle any errors that might occur. If there is an error, we are going to log the error message and exit the process. If successful, we are going to log the host name of the database.

## Calling connectDB

In your `server.js` file, import the `connectDB` function and call it.

```js
import connectDB from './config/db.js';

connectDB();
```

Restart your server and you should see the following message in your terminal:

```
Server running in development mode on port 5000
MongoDB Connected: cluster0-shard-00-00.lznwfv0.mongodb.net
```

If you do not see this, read the console error and make sure thay your string is correct, your user is created and has permission, and you added your IP address to the whitelist.
