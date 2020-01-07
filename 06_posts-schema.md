# Step 6: Create your `posts` schema

## Define the posts schema

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
    required: false // TODO: change to true!
  },
  "createdBy_id": {
    type: String,
    required: false // TODO: change to true!
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
