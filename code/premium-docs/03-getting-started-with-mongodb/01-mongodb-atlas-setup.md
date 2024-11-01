# MongoDB Atlas Setup

In this lesson, we will setup our MongoDB database using MongoDB Atlas. This is the cloud version of the database and is what is recommended for production applications. This is great because we don't have to install and configure a database, which can be a pain.

## Create a MongoDB Atlas Account

Go to [https://www.mongodb.com/cloud/atlas](https://www.mongodb.com/cloud/atlas) and click on the `Start Free` button. Go through the steps to create an account and confirm your email address.

Once you have created your account, log in and and create a database/cluster. It may ask you to create an organization and a project. If so, do that first and name it whatever you want.

Then you should be asked to create a database with a screen that looks like this:

<img src="./images/mongo1.png" width="600">

Then you will see a screen asking which plan you want.

<img src="./images/mongo2.png" width="600">

Chose the free `M0` plan. Leave AWS as the provider. You can change the cluster name and region if you want. Then hit "create".

Now you will be prompted to create a database user. This is the user that we will use to connect to the database. Be sure to click the "Create User" button.

<img src="./images/mongo3.png" width="600">

Scroll down and click on "Add My IP Address". This will allow you to connect to the database from your computer. Then click on "Finish & Close"

Now you have a deployed database in the cloud.

<img src="./images/mongo4.png" width="600">

## Create a Database

We have a cluster, now let's create the actual database that will store our collections. Click on "Browse Collections" and then "Add My Own Data".

Enter a database name. I will use "proshop". Then add a collection named "products".

You can manage data from here, but I wouldn't recommend it. Instead, we can use [MongoDB Compass](https://www.mongodb.com/products/compass). We will set that up in the next lesson.

## Connection String

Now we need to get the connection string for our database. Click on the "Connect" button and then "Connect Your Application".

Copy the connection string:

<img src="./images/mongo5.png" width="600">

Now, open your `.env` file and add the following line:

```
MONGO_URI=<your connection string>
```

There are a couple things to change in the connection string. First, replace `<password>` with the password you created for the database user. Second, you need to add your database name. I used the name "proshop" so I will add that to the end of the connection string. It should look like this:

```
mongodb+srv://brad123:brad123@cluster0.lznwfv0.mongodb.net/proshop?retryWrites=true&w=majority
```

So my `.env` file will look like this:

```
NODE_ENV=development
PORT=5000
MONGO_URI=mongodb+srv://brad123:brad123@cluster0.lznwfv0.mongodb.net/proshop?retryWrites=true&w=majority
```

Remember to add the `.env` file to your `.gitignore` file if you have not done so. Otherwise, you will be sharing your database password with the world.

What I usually do with my public repos is, in addition to my `.env`, I create a `.env.example` file that looks like this:

```
PORT=5000
MONGO_URI=YOUR_MONGO_URI
```

Then I will add the `.env.example` file to my repo. This way, people can see what environment variables they need to set up.

Now, we are ready to connect. Before we do that, in the next lesson, we will install MongoDB Compass, which is a GUI for MongoDB.
