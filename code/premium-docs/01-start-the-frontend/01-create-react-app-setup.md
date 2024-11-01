# Create React App Setup

Let's generate a new application with [Create React App](https://facebook.github.io/create-react-app/). Another popular option is [Vite](https://vitejs.dev/). It is faster than Create React App, but it is much newer so it is not used as much at this time, so we will use Create React App for this course.

First, create a new folder called `proshop` and open VS Code or whatever editor you prefer.

```bash
mkdir proshop
cd proshop
code .
```

Then, run the following command to create a new React application in `proshop/frontend`:

```bash
npx create-react-app frontend
```

This will generate our React application in a folder called `frontend`. Let's go into that folder.

```bash
cd frontend
```

## CRA Clean Up

We have some files and some code to clean up.

Delete the following files:

- `src/App.css`
- `src/App.test.js`
- `src/logo.svg`

Remove all CSS from `src/index.css`

In this `App.js` folder, remove everything and replace it with the following code:

```jsx
const App = () => {
  return (
    <>
      <h1>Welcome To ProShop</h1>
    </>
  );
};

export default App;
```

Let's also change the title of the page in `public/index.html`:

```html
<title>Welcome to ProShop!</title>
```

Now, run the dev server from the `frontend` folder:

```bash
npm start
```

Open [http://localhost:3000](http://localhost:3000) to view it in the browser. You should only see the text "Welcome To ProShop".

## Prepare Git & GitHub

### Remove Frontend .git Folder

We will have a git repository for our entire project, so we don't need one inside our `frontend` folder. Let's remove the `.git` folder:

```bash
rm -rf .git
```

### Move the .gitignore File

Move the `.gitignore` file from the `frontend` folder to the `proshop` folder root:

You can click and drag or run the following command from the `frontend` folder:

```bash
mv .gitignore ../
```

We want to add a couple things to the `.gitignore` file. Open it in your editor and add the following lines:

```bash
# Ignore all node_modules folders
node_modules

# Ignore the .env file
.env
```

### Create a Git Repository

Create a new Git repository in the `proshop` root folder:

```bash
cd ..
git init
```

## GitHub Setup

You should have a GitHub account already. If not, create one [here](https://github.com/). Create a new repository called `proshop` or whatever you would like.

### Add Remote Repo

Add the remote repository to your local repository. You can do this in VS Code by clicking the `...` button and then `Remote->Add Remote`. Or, you can run the following commands:

```bash
git remote add origin git@github.com:YOURUSERNAME/proshop.git
git branch -M main
git push -u origin main
```

## First Commit

Let's commit our changes. You can either run the following commands or use the Git tab in VS Code.

```bash
git add .
git commit -m "Create react app setup"
```

Now we have a brand new React application with a clean folder structure and a Git repository.
