# Step 8: Users Schema

The time has come! Here we will:
* define our users schema
* add some helper functions to our data model that will allow us to do things like validation, generate authentication tokens, has passwords, and find users by the credentials passed in by the user, and so on.


## Step 8.1: Defining the User Schema

Go to the `/models` directory and create `user.js`.

In `/models/user.js`:
```js
const mongoose = require('mongoose');
const config = require('../config');
const validator = require('validator')
const bcrypt = require('bcryptjs')
const jwt = require('jsonwebtoken');
const Schema = mongoose.Schema;

/**
 * Step 8.1: Define the user schema
 * this includes:
 * name
 * email
 * password
 * jwt tokens
 */
const userSchema = new Schema({
  username: {
    type: String,
    required: true,
    trim: true
  },
  email: {
    type: String,
    required: true,
    unique: true,
    lowercase: true,
    validate: value => {
      if (!validator.isEmail(value)) {
        throw new Error({
          error: 'invalid email address'
        })
      }
    }
  },
  password: {
    type: String,
    required: true,
    minLength: 7
  },
  reset_password_token:{
    type:String,
  },
  reset_password_expires:{
    type: Date
  },
  tokens: [{
    token: {
      type: String,
      required: true
    }
  }]
});


/**
 * Step 8.2: generateAuthToken()
 * Add a method to the user schema that says:
 * for this user, create a jwt token
 * add it to it's list of jwts
 * then save that user with that new jwt
 * and return it
**/


/**
 * Step 8.3: Add 'pre' function
 * before saving a new user make sure that 
 * the user password is hashed then continue
 */


/**
 * Step 8.4: findByCredentials()
 * adds a function to the user schema that
 * takes an email and password and then 
 * checks to see if email and password are a match
 */


/**
 * Step 8.5: create the model
 */




```

So far this should look pretty familiar. Like our `posts` model, we're defining the schema for the type of data we're expecting to live in our `users` collection.

You can see a few extra things like the `validate()` function we added to the `email` property or the `reset_password_expires` getting at `Date` type. 

You'll also see that our `tokens` property will be an `array of strings`. How our authentication will work is by checking to see if the `JWT` that is sent from the client matches one living in this `tokens` array.

## Step 8.2: add authenication token generator

In your `models/users.js` file, at step 7.2, add the following:
```js
/**
 * Step 8.2: generateAuthToken()
 * Add a method to the user schema that says:
 * for this user, create a jwt token
 * add it to it's list of jwts
 * then save that user with that new jwt
 * and return it
**/
userSchema.methods.generateAuthToken = async function () {
  // generate an auth token for the user
  const user = this;
  const token = jwt.sign({
    _id: user._id
  }, config.JWT_KEY)
  user.tokens = user.tokens.concat({
    token
  });

  await user.save();
  return token;
}
```

This function is added to the `userSchema` model that allows you to generate an authentication token and append it to the `tokens` array of the `user`. 

## Step 8.3: Always be hashing your passwords

Never ever ever save the raw password of your users in you database. Never. 

To do this, what we do is take a string password and create a hash using `bcrypt`. So when a user creates an account for the first time or decides to change their password, any string they pass in will become hashed before getting stored in your database. 

MongoDB Schemas have a nice functionality that allows you to take care of things "before" you save anything to your database. We use the `.pre()` function to do so. 

```js
/**
 * Step 8.3: Add 'pre' function
 * before saving a new user make sure that 
 * the user password is hashed then continue
 */
userSchema.pre('save', async function (next) {
  const user = this;

  if (user.isModified('password')) {
    user.password = await bcrypt.hash(user.password, 8);
  }

  next();
});
```

## Step 8.4: add method to check for existing user and compare passwords for validity

This last function will allow you to take in an email and password and to check to see if that email and password match one that exists in your database. 

```js
/**
 * Step 8.4: findByCredentials()
 * adds a function to the user schema that
 * takes an email and password and then 
 * checks to see if email and password are a match
 */
userSchema.statics.findByCredentials = async (email, password) => {
  try {
    
    const user = await User.findOne({
      "email": email
    });

    if (!user) {
      throw new Error({
        error: 'invalid login credentials - no email found'
      })
    }

    const isPasswordMatch = await bcrypt.compare(password, user.password);

    if (!isPasswordMatch) {
      throw new Error({
        error: 'invalid login credentials - incorrect password'
      });
    }

    return user;

  } catch (err) {
    throw new Error('error in auth')
  }

}

```

## Step 8.5: create the user model 

Last step, as we did in our `posts` is to create the model:

```js
/**
 * Step 8.5: create the model
 */
const User = mongoose.model('User', userSchema);

module.exports = User;
```

## Add and Commit your changes

```
git add .
git commit -m "adds users schema"
```

***
***
***
## Rest Stop:

At this point you've:
* defined the users schema and added in the necessary functions to help with your authentication and authorization.

***
***
***

Before you move onto defining the routes that will make use of your `users` schema. You will first need to define the `emailer` 

Continue onto the next step:
* ↳ [Step 9: emailer](tutorial/09_emailer.md)
