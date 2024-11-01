# Environment Variables

Environment variables are a way to store configuration in the environment outside of your application. They are essential for safely storing things like API keys, database passwords, and other sensitive data. We will need an environment variable to store our MongoDB connection string. We can also store things like the port number for our backend server.

## Create a .env File

Create a `.env` file in the root folder. This file will be used to store environment variables.

Right now, we will just add in our `PORT` variable. We will add the MongoDB connection string later.

```js
PORT = 8000;
```

I am setting it to 8000 just to test it out. I will put it back to 5000 later.

## DotENV

In production, we can set environment variables on the server. However, in development, we need to set them in our code. We can do this by using the `dotenv` package. This package will read the `.env` file and add the variables to `process.env`.

Install the `dotenv` package and make sure that you are in the root directory and not the `frontend`:

```bash
npm install dotenv
```

In order for this to work, you need to bring in the `dotenv` package in your `server.js` file and call the `config` function:

In the `server.js` file, add the following at the top of the file:

```js
import dotenv from 'dotenv';
dotenv.config();
```

We already have a `port` variable in our `server.js` file that looks to the environment for the port number. Anything in your `.env` file can be accessed with `process.env.VARIABLE_NAME`.

So the following line will check the environment for a `PORT` variable. If it doesn't find one, it will default to 5000:

```js
const port = process.env.PORT || 5000;
```

Restart your server and it should be running on port 8000.

If it still starts on `5000`, make sure that the line `const port = process.env.PORT || 5000;` is under the `dotenv.config()` line.

I am going to set my `PORT` back to `5000` in my `.env` file and restart my server.

## Setting the Node Environment

We can also set the `NODE_ENV` variable in our `.env` file. This will tell our application whether we are in development or production. We can use this to change the behavior of our application depending on the environment.

Add the following to your `.env` file:

```js
NODE_ENV = development;
```

This is optional, but I want my application to log out the environment that it is running in. I am going to add the following to my `server.js` file:

```js
console.log(`Server running in ${process.env.NODE_ENV} mode on port ${port}`);
```

Restart your server and you should see the following message in your terminal:

```
Server running in development mode on port 5000
```

## Setting Environment Variables on Your Computer

You can set environment variables on your computer. This is useful if you are working on a project with other people and you don't want to share your `.env` file. You can set the environment variables on your computer and they will be available to your application.

You don't need to do this now, but it is good to know how to do it.

To set an environment variable on your computer, you need to open up your terminal and run the following command:

```bash
export VARIABLE_NAME=VARIABLE_VALUE
```

For example, if I wanted to set the `PORT` variable to `3000`, I would run the following command:

```bash
export PORT=3000
```

On Windows, you need to use the `set` command:

```bash
set PORT=3000
```

To unset an environment variable, you can use the `unset` command:

```bash
unset PORT
```

Your system variables take precedence over the variables in your `.env` file. So if you set the `PORT` variable on your computer, it will override the `PORT` variable in your `.env` file.
