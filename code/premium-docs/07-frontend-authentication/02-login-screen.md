# Login Screen

Now that we have our `usersApiSlice` set up, we can use it to build a login screen.

For now, we'll just render a simple form with a username and password field, and a submit button. We'll add the logic to actually log in a user in the next lesson.

## Form Container

Since we will have a couple forms that are very similar, we'll create a `FormContainer` component to handle the styling of the form. This will make it easier to keep the styling consistent across all of our forms.

Create a new file called `FormContainer.js` in the `frontend/src/components` folder. Add the following to the file:

```js
import { Container, Row, Col } from 'react-bootstrap';

const FormContainer = ({ children }) => {
  return (
    <Container>
      <Row className='justify-content-md-center'>
        <Col xs={12} md={6}>
          {children}
        </Col>
      </Row>
    </Container>
  );
};

export default FormContainer;
```

Now we have a component that we can use to wrap our forms.

Create a new file called `LoginScreen.jsx` in the `frontend/src/screens` folder. Add the following to the file:

```js
import { useState } from 'react';
import { Link } from 'react-router-dom';
import { Form, Button, Row, Col } from 'react-bootstrap';
import FormContainer from '../components/FormContainer';

const LoginScreen = () => {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const submitHandler = (e) => {
    e.preventDefault();
    console.log('submitHandler');
  };

  return (
    <FormContainer>
      <h1>Sign In</h1>

      <Form onSubmit={submitHandler}>
        <Form.Group className='my-3' controlId='email'>
          <Form.Label>Email Address</Form.Label>
          <Form.Control
            type='email'
            placeholder='Enter email'
            value={email}
            onChange={(e) => setEmail(e.target.value)}
          ></Form.Control>
        </Form.Group>

        <Form.Group className='my-3' controlId='password'>
          <Form.Label>Password</Form.Label>
          <Form.Control
            type='password'
            placeholder='Enter password'
            value={password}
            onChange={(e) => setPassword(e.target.value)}
          ></Form.Control>
        </Form.Group>

        <Button type='submit' variant='primary' className='mt-2'>
          Sign In
        </Button>
      </Form>

      <Row className='py-3'>
        <Col>
          New Customer? <Link to=''>Register</Link>
        </Col>
      </Row>
    </FormContainer>
  );
};

export default LoginScreen;
```

This code is pretty simple, we are just constructing a login form using the `FormContainer` component we created earlier. We are also using the `useState` hook to keep track of the email and password values.

## Add To Router

Open up the `index.js` file in the `frontend/src` folder. Add the following to the file:

```js
import LoginScreen from './screens/LoginScreen';
```

```js
<Route path='/' element={<App />}>
  // ... other routes
  <Route path='/login' element={<LoginScreen />} />
</Route>
```

Click on the `Sign In` link in the header to see the login screen.
