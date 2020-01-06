# Step 6: Posts Routes

In this step we are going to set up our routes to our posts. These are the posts that your users will post to share with the list-project community you're building.

## Define the posts data model

Whenever you're working with a database, the first thing you'll do is to set up the data model. 

Let's start by setting up the data model for the posts data we expect to collect.

Navigate over to `/models` and **create**posts a file called `posts.js`. 

In `/models/posts.js` we are going to add our CRUD operations:

```js
const mongoose = require('mongoose');
const Schema = mongoose.Schema;
const postSchema = new Schema({
  "link": {
    type: String,
    required: false
  },
  "title":{
    type: String,
    required: false,
    default:"Fresh link"
  },
  "description":{
    type: String,
    required: false,
    default: "Fresh link description"
  },
  "createdBy_username": {
    type: String,
    required: true
  },
  "createdBy_id": {
    type: String,
    required: true
  }
}, {
  timestamps: {
    createdAt: 'created_at',
    updatedAt: 'updated_at'
  }
});

const db = mongoose.model('posts', postSchema)
module.exports = db;

```

In our model we have the following properties:
* link
* title
* description
* createdBy_username
* createdBy_id


## Define the posts route:

Now that our posts data model is ready, lets create the CRUD operations and API endpoints to interact with this data.

In your `/routes` directory, create a file called `/routes/posts.js`:

```js
const express = require('express');
const api = express.Router();
const db = require('../models/posts.js');

/** 
* TODO: Authentication middleware - We will defined these later and then uncomment them!!! Just note that we will come back to these one our authentication has been set up and defined.
**/
// const auth = require('../middleware/auth');
// const isOwner = require('../middleware/isOwner');

// --- Does not require authentication ---
// GET

// GET:id

// --- Requires authentication ---
// POST

// PUT

// DELETE

```

Notice how the comments indicate routes that will require authentication and routes that do not. For now, we can just create all the routes and then add in `middleware` that will handle this authentication layer later. For now, let's fill in these routes. 

In our `routes/posts.js`:

```js
// --- Does not require authentication ---
// GET
api.get('/', async(req, res, next) => {
  try{
    const data = await db.find();
    res.json(data);
  } catch(err){
    next(err);
  }
});

// GET:id
api.get('/:id', async(req, res, next) => {
  try{
    const id = req.params.id;
    const data = await db.findById(id);
    res.json(data);
  } catch(err){
    next(err);
  }
});

// --- Requires authentication ---
// POST
api.post('/', async (req, res, next) => {
try {
    const formattedData = {
      ...req.body,
      // createdBy_username: req.user.username,
      // createdBy_id: req.user._id,
    }

    const newData = await db.create(formattedData)
    res.json(newData);
  } catch (err) {
    next(err)
  }
})

// PUT
api.put('/:id', async (req, res, next) => {
  try {
    
    const id = req.params.id;
    const updateCmd = {
      $set: req.body
    };

    const updatedData = await db.findByIdAndUpdate({_id:id}, updateCmd, {new:true});
    res.json({data:updatedData, status:'success'});
  } catch (err) {
    next(err);
  }
})


// DELETE
api.delete('/:id', async (req, res, next) => {
  try {
    const id = req.params.id;

    await db.findByIdAndRemove({_id:id});
    
    res.json({
      status:'success',
      id:id,
      message: 'successfully deleted feature'
    });

  } catch (error) {
    next(err);
  }
})

module.exports = api;

```

Whew! Now that we've got out `posts` routes defined we can set our `posts` routes in our server `index.js`.

In `./index.js`:
```js
/**
* Step 8: Set your posts routes
**/
const postRoutes = require('./routes/posts')
app.use('/api/v1/posts', postRoutes)
```

What this is saying is: "at the following route: `/api/v1/posts`, you will find the API endpoints -- `GET`, `POST`, `PUT`, `DELETE` -- that users can interface with. Basically the route `/api/v1/posts` is prepended to the endpoints defined in the `./routes/posts` file.

