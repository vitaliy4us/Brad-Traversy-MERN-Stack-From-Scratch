# Upload Product Image - Backend

We can now add and edit products. But we still need to add the functionality to upload product images. We will start out with the backend code.

We are going to use a package called [Multer](https://github.com/expressjs/multer) to upload images. Make sure that you are in the root when you install it and NOT in the frontend.

Install Multer with the following command:

```bash
npm install multer
```

## Create Upload Folder

We need a place to store images. In the root of the project (NOT in backend or frontend), create a folder called `uploads`. This is where the images will be stored.

By default, an empty folder will not be pushed to Github, so you will need to create a `.gitkeep` file in the folder. This will tell Github to keep the folder.

## Upload Route

We need a backend route to upload the image. We are actually going to create a new route file for uploads. So create a new file called `uploadRoutes.js` in the `backend/routes` folder.

Add the following code:

```js
import path from 'path';
import express from 'express';
import multer from 'multer';
const router = express.Router();

const storage = multer.diskStorage({
  destination(req, file, cb) {
    cb(null, 'uploads/');
  },
  filename(req, file, cb) {
    cb(
      null,
      `${file.fieldname}-${Date.now()}${path.extname(file.originalname)}`
    );
  },
});

function checkFileType(file, cb) {
  console.log(file);
  const filetypes = /jpg|jpeg|png/;
  const extname = filetypes.test(path.extname(file.originalname).toLowerCase());
  const mimetype = filetypes.test(file.mimetype);

  if (extname && mimetype) {
    return cb(null, true);
  } else {
    cb({ message: 'Images only!' });
  }
}

const upload = multer({
  storage,
});

router.post('/', upload.single('image'), (req, res) => {
  res.send({
    message: 'Image uploaded successfully',
    image: `/${req.file.path}`,
  });
});

export default router;
```

There are a few options that you can use for files with Multer. We are going to use `diskStorage` to store the file on the server. We are also going to use `single` to upload a single file. If you wanted multiple files, you would use `array` or `fields`.

We are setting the destination to `uploads/` and the filename to `fieldname-timestamp.ext`. The `fieldname` is the name of the field in the form. We are going to use `image` as the field name.

We are also using a function called `checkFileType` to check the file type. We are only allowing `jpg`, `jpeg`, and `png` files.

We then create a post route for `/api/uploads` and use the `upload.single` middleware to upload a single file. We are using `image` as the field name. We then send the image path back to the frontend.

Now we need to hook that route up, so open your `server.js` file and import the file and add the following code with the rest of the routes:

```js
import uploadRoutes from './routes/uploadRoutes.js';

// ..

app.use('/api/upload', uploadRoutes);
```

## Make Upload Folder Public

In express, we can define a folder as public. This means that we can access the files in that folder from the browser. We need to do this for the `uploads` folder.

In the `server.js` file, add the following code:

```js
import path from 'path';

// ..

const __dirname = path.resolve();
app.use('/uploads', express.static(path.join(__dirname, '/uploads')));
```

## Set Index Route

While, we are here, we should take care of the index route. Right now, we just have a response that says `API is running...`. We should send the frontend instead.

Replace this:

```js
app.get('/', (req, res) => {
  res.send('API is running...');
});
```

With this:

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

What we are doing here is checking if the environment is production. If it is, we are going to serve the frontend. If it is not, we are just going to serve the simple response.

We are setting it to `frontend/build` because that is where the frontend will be built to when we deploy and run `npm run build`. We will get to all that later.

Now that we have the backend setup for uploading images, we can move on to the frontend.
