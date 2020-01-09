# Step 6: Posts Routes

In this step we are going to set up our routes to our posts. These are the posts that your users will post to share with the list-project community you're building.


## Define the posts route:

Now that our posts data model is ready, lets create the CRUD operations and API endpoints to interact with this data.

In your `/routes` directory, create a file called `/routes/posts.js`:

```js
const express = require('express');
const api = express.Router();
const db = require('../models/post.js');

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

## Test your posts API endpoints
Let's test to see if your endpoints are ready to rock:

### POST
First we need to add something to our database:

```sh
curl -X POST -H "Content-Type: application/json" -d '{"title": "test", "description": "testing testing 123", "link": "https://itp.nyu.edu"}' http://localhost:3030/api/v1/posts
```

This should return:

```json
{"title":"test","description":"testing testing 123","_id":"5e13ab1c263ec41ca339c7e3","link":"https://itp.nyu.edu","created_at":"2020-01-06T21:48:12.048Z","updated_at":"2020-01-06T21:48:12.048Z","__v":0}
```
note: your `_id` and `timestamps` will be different.

### GET

```sh
curl http://localhost:3030/api/v1/posts
```

This should return:

```json
[{"title":"test","description":"testing testing 123","_id":"5e13ab1c263ec41ca339c7e3","link":"https://itp.nyu.edu","created_at":"2020-01-06T21:48:12.048Z","updated_at":"2020-01-06T21:48:12.048Z","__v":0}]
```
Note: this returns an array!

### GET /:id

```sh
curl http://localhost:3030/api/v1/posts/5e13ab1c263ec41ca339c7e3
```

this should return:

```json
{"title":"test","description":"testing testing 123","_id":"5e13ab1c263ec41ca339c7e3","link":"https://itp.nyu.edu","created_at":"2020-01-06T21:48:12.048Z","updated_at":"2020-01-06T21:48:12.048Z","__v":0}%
```

### PUT

```sh
curl -X PUT -H "Content-Type: application/json" -d '{"title": "NYU ITP Program", "description": "Learn to make anything at ITP.", "link": "https://itp.nyu.edu"}' http://localhost:3030/api/v1/posts/5e13ab1c263ec41ca339c7e3
```

This should return:

```json
{"data":{"title":"NYU ITP Program","description":"Learn to make anything at ITP.","_id":"5e13ab1c263ec41ca339c7e3","link":"https://itp.nyu.edu","created_at":"2020-01-06T21:48:12.048Z","updated_at":"2020-01-06T21:52:53.570Z","__v":0},"status":"success"}
```


### DELETE

```sh
curl -X DELETE http://localhost:3030/api/v1/posts/5e13ab1c263ec41ca339c7e3
```

If you run the above, this will delete the document. 


## Add and Commit your changes

```
git add .
git commit -m "adds posts routes"
```

***
***
***
## Rest Stop:

At this point you've:
* done a lot of stuff
* set up a data model for the posts
* set up the CRUD routes for the posts
* added the routes to your server

***
***
***

Notice how our `POST`, `PUT`, `DELETE` routes can be performed by anyone. In a usual scenario, we only want to user who created the document to be able to edit or delete data. Also, we only want people who are authorized to create data to do so (i.e. the people with user accounts). The next steps will be the most challenging, but it is alas time to get our authentication and authorization sorted for our project!

Continue onto the next step:
* ↳ [Step 8: CRUD users schema](/08_user-schema.md)




