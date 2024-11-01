# Rating Component

In this lesson, we will add the rating component, which will display the product rating in the `Product` component.

Create a new component in the `src/components` folder called `Rating.js`.

Here we will return a div with 5 possible stars. We will use 3 different icons. One for a full star, a half star, and an empty star. We will use the `react-icons` package to import these icons.

As props, we will take in a value, which will be the rating and the text, which will be the number of reviews. Then we will set the `className` to be a conditional based on the rating.

```js
import { FaStar, FaStarHalfAlt, FaRegStar } from 'react-icons/fa';

const Rating = ({ value, text }) => {
  return (
    <div className='rating'>
      <span>
        {value >= 1 ? (
          <FaStar />
        ) : value >= 0.5 ? (
          <FaStarHalfAlt />
        ) : (
          <FaRegStar />
        )}
      </span>
      <span>
        {value >= 2 ? (
          <FaStar />
        ) : value >= 1.5 ? (
          <FaStarHalfAlt />
        ) : (
          <FaRegStar />
        )}
      </span>
      <span>
        {value >= 3 ? (
          <FaStar />
        ) : value >= 2.5 ? (
          <FaStarHalfAlt />
        ) : (
          <FaRegStar />
        )}
      </span>
      <span>
        {value >= 4 ? (
          <FaStar />
        ) : value >= 3.5 ? (
          <FaStarHalfAlt />
        ) : (
          <FaRegStar />
        )}
      </span>
      <span>
        {value >= 5 ? (
          <FaStar />
        ) : value >= 4.5 ? (
          <FaStarHalfAlt />
        ) : (
          <FaRegStar />
        )}
      </span>
      <span className='rating-text'>{text && text}</span>
    </div>
  );
};

export default Rating;
```

We are also going to add a tiny bit of CSS in the `index.css` file to make the stars yellow and add some margin and padding.

```css
.rating span {
  margin: 0.1rem;
}

.rating svg {
  color: #f8e825;
}

.rating-text {
  font-size: 0.8rem;
  font-weight: 600;
  padding-left: 0.5rem;
}
```

Now you should see your star ratings based on the data in the `products.js` file.

## Product Card Height

Right now, the product components can be different heights depending on the length of the title. I want them to be the same no matter what. So let's make it so that the title will always be one line and we'll add an ellipsis if it's too long.

Add a class to the title in `Product.js` called `product-title`.

```js
<Card.Title as='div' className='product-title'>
  <strong>{product.name}</strong>
</Card.Title>
```

Then add the following CSS to `index.css`.

```css
.product-title {
  height: 2.5em; /* Set a fixed height */
  overflow: hidden; /* Hide overflow content */
  text-overflow: ellipsis; /* Add ellipsis for long text */
  white-space: nowrap; /* Prevent wrapping */
}
```

## Add To Product Component

Bring in the Rating component to `components/Product.jsx` and add it right above the price:

```js
import Rating from './Rating';

 <Card.Text as='div'>
    <Rating value={product.rating} text={`${product.numReviews} reviews`} />
</Card.Text>
```

Now, your product cards should look like this:

<img src="./images/product-cards.png" width="600">
