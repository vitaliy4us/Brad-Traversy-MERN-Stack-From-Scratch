# MongoDB Compass Setup

This lesson is optional, but I would suggest installing [MongoDB Compass](https://www.mongodb.com/products/compass), which is a GUI for MongoDB. It is a great tool for managing your database.

Go to [https://www.mongodb.com/products/compass](https://www.mongodb.com/products/compass) and download the version for your operating system. Install it and open it.

## Create a Connection

When you open up Compass, you will see a screen that looks like this:

<img src="./images/compass1.png" width="600">

You need to enter your connection string. Go back to your MongoDB Atlas account and click on the "Connect" button. Then click on "Connect using MongoDB Compass".

Copy the connection string and paste it into the box in the Compass app.

You will need to add your password and database name. Mine looks like this:

```
mongodb+srv://brad123:brad123@cluster0.lznwfv0.mongodb.net/proshop
```

Then click "Connect".

On the left, you will see 3 databases, "admin", "local" and "proshop". Click on "proshop" and then "products". You should see a screen that looks like this:

<img src="./images/compass2.png" width="600">

From here you can add, edit and remove documents. Obviously, we don't have anything in here yet, but we will soon.

I will use this occasionally to check or change any data directly if needed.

In the next lesson, we will connect to our database through our application.
