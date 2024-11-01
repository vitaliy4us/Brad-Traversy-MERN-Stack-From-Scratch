# Deploying To Render

We are now going to deploy our application. There are many different services that you could use to deploy a MERN application. You could use something like Linode or Digital Oceanm which are cloud services that give you a dedicated Linux instance and you can proceed to set up everything from scratch, however that can be quite difficult. We are going to use a service called Render, which is a service that allows you to deploy your application quickly with just about no configuration. All you have to do is connect your GitHub repository and Render will automatically deploy your application for you.

## Preperation

There are a couple things that we need to do to prepare for deployment. First, open up your `package.json` file that is in the root of your project (NOT the frontend). We are going to add a `build` script.

Add the following script:

```
 "build": "npm install && npm install --prefix frontend && npm run build --prefix frontend"
```

This script will install the dependencies for the backend and frontend and then build the frontend.

Let's test the build script. Open a terminal and navigate to the root of your project. Run the following command:

```
npm run build
```

Now you should have a folder called `build` in your frontend folder. This is the folder that will be served by the backend. We already have that set up in our `server.js` file. Here is the code in case you have not added it to your `backend/server.js` file:

```js
if (process.env.NODE_ENV === 'production') {
  app.use(express.static(path.join(__dirname, '/frontend/build')));

  app.get('*', (req, res) =>
    res.sendFile(path.resolve(__dirname, 'frontend', 'build', 'index.html'))
  );
} else {
  app.get('/', (req, res) => {
    res.send('API is running....');
  });
}
```

Let's try it out by editing the `.env` file in the root of your project. Change the `NODE_ENV` variable to `production`. Now run the following command:

```
npm start
```

This is what Render will run once deployed. This is not running the React dev server. It is running the Express server, which points to the build folder. If you go to `localhost:5000` you should see your application. This is your production build.

Swich the `NODE_ENV` variable back to `development` in your `.env` file. Once deployed, the NODE_ENV variable will be set to `production` automatically.

## .gitignore File

The .gitignore file is a file that tells git which files to ignore. We already have a bunch of stuff here, but there are a couple more things to add.

We don't want our build folder to be commited. The reason for that is because Render will run the build script for us. So we don't need to commit the build folder. Add the following line to your `.gitignore` file:

```bash
frontend/build
```

We also don't want the images in the `uploads` folder to be commited. So add the following line to your `.gitignore` file:

```bash
uploads/*png
uploads/*jpg
```

## Push to GitHub

You need to push your final code to GitHub if you haven't already done so. If you have not, make sure that you have a GitHub accountm create your SSH keys and then in your GitHub dashboard, click `New Repository` and follow the instructions to create a new repository.

Open your terminal and initialize a new git repo with `git init`

Copy the `git remote add origin` command that is in your GitHub dashboard and paste it into your terminal. This will add your GitHub repo as a remote to your local repo.

You can commit your files using the integrated VS Code git tool or you can use the terminal. If you are using the terminal, you can use the following commands:

```bash
git add .
git commit -m "Initial commit"
```

Now you can push your code to GitHub with the following command:

```bash
git push -u origin master
```

## Render

You can log into Render using your GitHub account. Once you are logged in, click `New+` and then click `New Web Service`. Then select the GitHub repo with your project.

Add a name to your project.

For the `Build Command` option, enter `npm run build`. This is the command that we added to our `package.json` file.

For the start command, enter `npm start`. This will run the API and the production build.

Now click `Advanced`.

## Environment Variables

In development, we have all of our env variables in a `.env` file. In production, we need to add them to Render. Click on the `Add Environment Variable` button.

Here, you want to enter the key and value for the following env variables:

```
MONGO_URI
PAYPAL_CLIENT_ID
JWT_SECRET
PAGINATION_LIMIT
```

Just copy what you have in your `.env` file and paste it into Render.
Sometimes people will use a different database for production. We are just going to use the same one. So we will have the same data that we have in development.

Now click on `Create Web Service` to save.

Now your project will be deployed and not only that, but everytime you push to the main branch of your GitHub repo, Render will automatically deploy your application. It's as easy as that.

You now have a deployed MERN ecommerce application. You can go to the URL that Render gives you and you should see your application.
