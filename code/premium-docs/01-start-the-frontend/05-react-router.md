# React Router

Now that we have the home screen and the products showing, we need to add the **React Router** so that we can navigate between pages. I'm going to be using version 6.8 of React Router and there was some changes and additions in version 6.4. I'm not going to be using loaders and some of the newer features. I want to keep this as simple as possible and stick to what I know and what most of you know. I'm still learning about some of the newer features at the time of this recording. if you want to refactor and do some things differently, that's absolutely fine.

## Install React Router

To install React Router, run the following command in your `frontend` folder:

```bash
cd frontend
npm install react-router-dom
```

## Create Routes

So we're actually going to create our routes in the `index.js` file.

We need to import a few things from React Router as well as the `HomeScreen` component:

```js
import {
  createBrowserRouter,
  createRoutesFromElements,
  Route,
  RouterProvider,
} from 'react-router-dom';
import HomeScreen from './screens/HomeScreen';
```

Go ahead and add the following block of code under all of the imports:

```js
const router = createBrowserRouter(
  createRoutesFromElements(
    <Route path='/' element={<App />}>
      <Route index={true} path='/' element={<HomeScreen />} />
    </Route>
  )
);
```

This is going to create a route for the home screen. We're going to add more routes later. The `index={true}` is going to make this the default route.

Now, we need to use the `RouterProvider` within `ReactDOM.render`:

```js
root.render(
  <React.StrictMode>
    <RouterProvider router={router} /> // Add this line
  </React.StrictMode>
);
```

Now, we just need to replace the hardcoded `<HomeScreen />` in the `App` component with the React Router `<Outlet />` component. That way, the correct screen will show based on which route we are currently on. You can also remove the HomeScreen import line from the top of the file. It should now look like this:

```js
import { Container } from 'react-bootstrap';
import { Outlet } from 'react-router-dom';
import Header from './components/Header';
import Footer from './components/Footer';

const App = () => {
  return (
    <>
      <Header />
      <main className='py-3'>
        <Container>
          <Outlet />
        </Container>
      </main>
      <Footer />
    </>
  );
};

export default App;
```

## Adding Links

The way that we handle internal links with React Router is different. We don't want to use `<a>` tags because it causes a hard refresh. Instead, we use the `<Link>` component. Let's change the `<a href="">` tags in the `Product` component to use the router `<Link to="">` component. They should now look like this:

```js
<Card.Body>
  <Link to={`/product/${product._id}`}>
    <Card.Title as='div'>
      <strong>{product.name}</strong>
    </Card.Title>
  </Link>

  <Card.Text as='h3'>${product.price}</Card.Text>
</Card.Body>
```

In the Header, we are using React Bootstrap. The `<Nav.Link>` component is actually an `<a>` tag. This is a bit trickier. We can't just change it to a `<Link>` component. So we are going to install a package called `react-router-bootstrap` that will allow us to use the `<LinkContainer>` component. This will allow us to use the `<Link>` component within the `<Nav.Link>` component.

```bash
npm install react-router-bootstrap
```

Now import it into the header component

```js
import { LinkContainer } from 'react-router-bootstrap';
```

Add it around where we need links and get rid of the `href` attribute:

```js
const Header = () => {
  return (
    <header>
      <Navbar bg='dark' variant='dark' expand='lg' collapseOnSelect>
        <Container>
          <LinkContainer to='/'>
            <Navbar.Brand>ProShop</Navbar.Brand>
          </LinkContainer>
          <Navbar.Toggle aria-controls='basic-navbar-nav' />
          <Navbar.Collapse id='basic-navbar-nav'>
            <Nav className='ms-auto'>
              <LinkContainer to='/cart'>
                <Nav.Link>
                  <FaShoppingCart /> Cart
                </Nav.Link>
              </LinkContainer>
              <LinkContainer to='/login'>
                <Nav.Link href='/login'>
                  <FaUser /> Sign In
                </Nav.Link>
              </LinkContainer>
            </Nav>
          </Navbar.Collapse>
        </Container>
      </Navbar>
    </header>
  );
};
```
