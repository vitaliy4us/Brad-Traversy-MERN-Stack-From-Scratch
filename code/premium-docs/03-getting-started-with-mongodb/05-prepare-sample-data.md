# Prepare Sample Data

I want to get some sample data into the database for us to work with. We just need products and users at the moment. We'll deal with orders later.

## Products

We can use the data in our `backend/data/products.js` file for the products.

The first thing we need to do is remove the `_id` field from the documents because the id is added automatically by MongoDB. Open the file and remove the `_id` field from each document.

## Users

Let's create another file inside of the `backend/data` folder called `users.js`. We will use this file to store the users.

## Encrypting the Password

When a user registers, obvioulsy we do not want to store their password in plain text. We need to encrypt it. We will use a package called `bcryptjs` to do this. Install it with the following command:

```bash
npm install bcryptjs
```

Now, we can use the `hashSync` function to encrypt the password. Open the `users.js` file and add the following code:

```js
import bcrypt from 'bcryptjs';

const users = [
  {
    name: 'Admin User',
    email: 'admin@email.com',
    password: bcrypt.hashSync('123456', 10),
    isAdmin: true,
  },
  {
    name: 'John Doe',
    email: 'john@email.com',
    password: bcrypt.hashSync('123456', 10),
  },
  {
    name: 'Jane Doe',
    email: 'jane@email.com',
    password: bcrypt.hashSync('123456', 10),
  },
];

export default users;
```

All of the users will have the password "123456" and we will use the `isAdmin` field to determine if the user is an admin or not. Only the first user is an admin.

Now that we have the data, we need to import it into the database. We will do that in the next lesson.
