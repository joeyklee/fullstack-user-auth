# Step 12: Authenicated and authorized posts 

## Step 12.1: Require() the `auth` and `isOwner` middleware in your posts route

At the top of your `routes/posts.js`:

```js
const express = require('express');
const api = express.Router();
const db = require('../models/post.js');

/** 
* TODO: Authentication middleware - We will defined these later and then uncomment them!!! Just note that we will come back to these one our authentication has been set up and defined.
**/
const auth = require('../middleware/auth');
const isOwner = require('../middleware/isOwner');

```

We haven't created the `isOwner` middleware yet, but we can create that now.

## Step 12.2: Create the `isOwner` middleware

While your `auth` middleware can check to see if the user is authenticated, you'll need another middleware to check if the user is `authorized` to make changes to the data being requested. 

Create a file called `/middleware/isOwner.js`. 

In `/middleware/isOwner.js`:

```js
const Posts = require('../models/post');

const isOwner = async (req, res, next) => {
  try{
    const id = req.params.id; 
    const data = await Posts.findById(id);

    if(String(data.createdBy_id) === String(req.user._id)){
      next();
    } else {
      throw new Error('Sorry, you are not the owner of that resource');
    }
  } catch(err){
    res.status(401).send({ error: 'Not authorized to edit or delete this resource' })
  }
}

module.exports = isOwner;
```

This `isOwner` middleware will get the post id from the `req.params` and then check to see in that post if the `createdBy_id` matches that of the `user._id`. If it is a match, then continue fulfilling the request. If not, the request will throw an error.

Now you can add the `auth` and `isOwner` middlewares to the routes you want to protect.

Those protected routes in `routes/posts.js` should now look like:

```js
// --- Requires authentication ---
// POST
api.post('/', auth, isOwner, async (req, res, next) => {
try {
    const formattedData = {
      ...req.body,
      createdBy_username: req.user.username,
      createdBy_id: req.user._id,
    }

    const newData = await db.create(formattedData)
    res.json(newData);
  } catch (err) {
    next(err)
  }
})

// PUT
api.put('/:id', auth, isOwner, async (req, res, next) => { // ...a bunch of code... })


// DELETE
api.delete('/:id', auth, isOwner, async (req, res, next) => { // ...a bunch of code... })
```

NOTE: the `formattedData` variable now gets the `createdBy_username` and `createdBy_id` properties added to the mix (they were previously commented out).


## Add and Commit your changes

```
git add .
git commit -m "adds authd posts routes"
```

***
***
***
## Rest Stop:

At this point you've:
* completed adding auth to the posts routes
* completed the server side component of the app

***
***
***

## Last considerations and notes for the server side
* CORS whitelisting - in case you need to take care of whitelisting URLS you can take care of that in `index.js`
* In your `routes/users.js` you may have to adjust the `cookies` settings depending on your setup.


Continue onto the next step:
* ↳ [Step 13: front-end 1: the list view](/13_frontend-list-view.md)

