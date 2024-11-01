# Page Titles

Page titles are important for SEO and other reasons. Right now, every page of our website has the same title. I want the product pages to have the product name in the title.

We are going to use a package called `react-helmet-async` to set the page title. This is a fork of the `react-helmet` package that is compatible with React 16.8.0 and above.

Let's install it. Make sure that you are in the frontend:

```bash
cd frontend
npm install react-helmet-async
```

## Add Provider

Just like we do with Redux, we need to wrap our application with a provider. We will do this in `frontend/src/index.js`:

```js
import { HelmetProvider } from 'react-helmet-async';

root.render(
  <React.StrictMode>
    <HelmetProvider>
      <Provider store={store}>
        <PayPalScriptProvider deferLoading={true}>
          <RouterProvider router={router} />
        </PayPalScriptProvider>
      </Provider>
    </HelmetProvider>
  </React.StrictMode>
);
```

## Meta Component

We will create a new component to handle meta data. Create a new file in `frontend/src/components` called `Meta.jsx`:

```js
import React from 'react';

import { Helmet } from 'react-helmet-async';

const Meta = ({ title, description, keywords }) => {
  return (
    <Helmet>
      <title>{title}</title>
      <meta name='description' content={description} />
      <meta name='keyword' content={keywords} />
    </Helmet>
  );
};

Meta.defaultProps = {
  title: 'Welcome To ProShop',
  description: 'We sell the best products for cheap',
  keywords: 'electronics, buy electronics, cheap electroincs',
};

export default Meta;
```

Now we have a component that we can use to add page titles, etc. We have some default values for the meta data.

Open the `frontend/src/screens/HomeScreen.jsx` file and add the `Meta` component:

```js
import Meta from '../components/Meta';
```

Add it right above the `h1`

```js
  <Meta />
  <h1>Latest Products</h1>
```

We will keep the defaults here.

## Product Screen

Open the `frontend/src/screens/ProductScreen.jsx` file and add the `Meta` component:

```js
import Meta from '../components/Meta';
```

Add it right under the opening `<>` and add the title set to the product name:

```js
<Meta title={product.name} />
```

Now if you go to a product page, you will see the name in the title. You can also add the description and keywords prop, but keep in mind, this is a single page app and the meta data will not be indexed by search engines. It is more for user experience.
