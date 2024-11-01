# What Is Redux and Redux Toolkit?

Redux is a popular JavaScript library for managing application state. It's often used with React, but it can be used with any other UI library slash framework. There are many other state management libraries available, but Redux is one of the most popular and well-supported. It has also been around for a long time, so there is a lot of documentation and examples available.

I will admit that Redux can be a bit confusing at first. It's a tool that takes some practice to use effectively. However, once you get the hang of it, it can make building complex applications much easier. In this course, we'll be using Redux Toolkit, which is a package that includes several tools to help make working with Redux easier, especially working with backend APIs and making requests.

## State

Before we dive into Redux, let's talk about state.

### Component State

There is component level state. Let's take a collapsable menu for example. You may have a piece of state in the component called "isOpen", which can be set to true or false. That kind of state is what the `useState` hook is for.

### App or Global State

App-level state is data that is available site-wide. Usually, anything that you fetch from the backend like products and users are app-level state. There are many different ways to manage this. React does have the built in `context api`, however for larger applications, it is common to use a 3rd party state manager and Redux is one that is common and has been around for years.

State management refers to the way that we manage the data that our application needs to keep track of. 

For example, if we were building a simple to-do list application, we might need to keep track of the items that the user has added to the list. We might also need to keep track of whether or not each item is completed. We might also need to keep track of which filter the user has selected (all, active, or completed). All of this information is what we call "state".

If we were building this application without any state management, we may have something like this:

```js
let items = [];
let filter = 'all';
```

This is a very simple example, but it shows the basic idea. We have two variables that we use to keep track of the state of our application. We can add items to the list by pushing them onto the `items` array, and we can update the filter by setting the `filter` variable. This works, but it has some problems.

It's very easy to accidentally update the state in the wrong place. If we're not careful, we might end up with code that looks like this:

```js
function addItem(text) {
  items.push({ text, completed: false });
}
```

This code looks fine, but it is updating the `items` array directly, which means that any other code that is reading from that array might not see the new item. This can lead to bugs that are very hard to track down.

Redux solves this by forcing us to update the state in a very specific way. We can't just update the state directly. Instead, we have to dispatch an action, which is an object that describes what state change we want to make. The Redux store will then run a reducer function to figure out how to update the state based on the action. This makes it much harder to accidentally update the state in the wrong place.

A lot of these terms like "action" and "reducer" might sound a bit confusing right now, but don't worry. We'll talk about them in more detail later in this course.

In summary, the state is held at the app level and is updated through actions dispatched to reducer functions. The updated state is then sent down to all components that depend on it, ensuring that all parts of the app have access to the most up-to-date information. Rather than directly changing the existing state, a new copy is created and updated to prevent any issues with components not updating properly.

## Redux Toolkit

Redux Toolkit is a package that includes several tools to help make working with Redux easier. It includes functions to create store setup logic, generate action creators and reducers, and more. It also includes a package called Redux Thunk, which is a middleware that lets us write async logic that interacts with the Redux store. We need that because we'll be fetching data from our backend API.

## Redux DevTools

You're also going to want to install the Redux DevTools extension in your browser. Either the [Chrome extension](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=en) or the [Firefox addon](https://addons.mozilla.org/en-US/firefox/addon/reduxdevtools/). This will allow you to see everything that has to do with Redux including your state, actions and more. 

