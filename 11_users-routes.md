# Step 11: Defining the Users routes

Here we are going to add the API endpoints that will allow users to:
*  register
*  login,
*  logout
*  logout of all devices (tokens)
*  request a password reset
*  reset a password with a new password
*  get their own user info

This is a roadmap of what we're going to be doing here:

Create the file `routes/users.js`. 

In `routes/users.js`
```js
/**
* Step 11.1: read in required modules
**/


/**
* Step 11.2: registration route
**/


/**
* Step 11.3: login route
**/


/**
* Step 11.4: logout route
**/


/**
* Step 11.5: logout all route
**/


/**
* Step 11.6: forgot password route
**/


/**
* Step 11.7: reset_password route
**/


/**
* Step 11.8: get user info
**/

```

## Step 11.1: read in required modules

Read in your node modules, middleware, and user model to be used in your API endpoints.

```js
/**
* Step 11.1: read in required modules
**/
// for the routes
const express = require('express');
const api = express.Router();
const User = require('../models/user.js');
const auth = require('../middleware/auth');
const scrub = require('../middleware/scrub'); // TODO: this will scrub out the password whenever user info is sent with requests - we'll come back to this.
// for the emailer
const crypto = require('crypto');
const bcrypt = require('bcryptjs');
const config = require('../config');
const emailer = require('../helpers/emailer');
```

## Step 11.2: registration route

This is your registration route. When users signup to your app, they will be sending a POST request with:
* username
* password
* email address

It is extremely important that your service uses `HTTPS` otherwise your users can (and will likely) be compromised by bad actors. We will revisit this later, but know that there are platforms-as-a-service that by default will add a layer of HTTPS onto your applications. 

```js
/**
* Step 11.2: registration route
* POST /users
* create a new user
**/
api.post('/register', async (req, res) => {
  try {

    const user = new User(req.body)

    await user.save();
    const token = await user.generateAuthToken();

    // Set a cookie
    res.cookie('auth_token', token, {
      maxAge: 60 * 60 * 1000, // 1 hour
      httpOnly: true,
      secure: false, // true,
      sameSite: false,
    })

    res.status(201).send({
      user,
      token
    });

  } catch (err) {
    res.status(400).send(err);
  }
});
```

In this code snippet, you can see where you use the `.generateAuthToken()` to create a `JWT` to add to your user and to send to the client as a cookie.

## Step 11.3: login route

This is your login route. When users of your service want to log on to your app, they will send their email and password to this route. 

Again you can see an auth token is generated and sent to the client when a log-in occurs. 

```js
/**
* Step 11.3: login route
 * Allow the user to login
 * with the email and password
**/
api.post('/login', async (req, res) => {
  try {
    const {
      email,
      password
    } = req.body;

    const user = await User.findByCredentials(email, password);

    if (!user) {
      return res.status(401).send({
        error: 'login failed! check your credentials'
      })
    }

    const token = await user.generateAuthToken();

    // Set a cookie
    res.cookie('auth_token', token, {
      maxAge: 60 * 60 * 1000, // 1 hour
      httpOnly: true,
      secure: false, // true,
      sameSite: false,
    })

    res.send({
      user: user,
      token
    });

  } catch (err) {
    res.status(400).send(err);
  }
})

```


***
***
***
## Rest Stop:

At this point you've:
* created the registration route
* created the login route

NOTE: that both of these routes are NOT authenticated routes. This makes sense because in order to create an account, you won't have credentials yet, and if you want to log-in, you won't have an auth token yet in your client. The next routes will take the `auth` middleware.

***
***
***

## Step 11.4: logout route

If you can log-in, you'll need to be able to log out. Notice here that the `auth` middleware is passed as an argument to `api.post()`. 

You can think of it like:
* a request comes in to `/me/logout` ==> 
* the data and cookie coming in with that request pass through the `auth` middleware ==>
* if it is successful, continue onto the async callback function that is defined afterward.

```js
/**
* Step 11.4: logout route
**/
api.post('/me/logout', auth, async (req, res) => {
  try {
    req.user.tokens = req.user.tokens.filter(token => {
      return token.token !== req.token;
    })

    await req.user.save();
    res.send({status:'success', message:"logout complete"});
  } catch (err) {
    res.status(500).send(err);
  }
})
```

Hooray, now your users can logout!

## Step 11.5: logout of all route

This is kind of cool. If you want to logout of all devices, you can revoke all the tokens in that user that exist. Maybe this should just be the default, but I figured why not have both?!

Same idea as the above, the `auth` middleware is present before the request can continue. 

```js
/**
* Step 11.5: logout all route
**/
api.post('/me/logoutall', auth, async (req, res) => {
  try {
    req.user.tokens.splice(0, req.user.tokens.length);
    await req.user.save();
    res.send({status:'success', message:"logout complete"});
  } catch (err) {
    res.status(500).send(err);
  }
})
```

***
***
***
## Rest Stop:

At this point you've:
* create a logout route for a single token
* created a logout route for all tokens of a user

Note: both of these took the auth middleware

***
***
***


## Step 11.X: Forgot password route

Oh! Yay we can now user our fancy emailer helper module here. 

What is going to happen is:
* your user will forget their password
* they will freak out, but lucky for them you've made this fancy API endpoint to start the password reset process.
* you user will send their email address to `/auth/forget_password`
* you'll create a new `reset_password_token` and store that in the user document. 
* you'll send them that `reset_password_token` with a URL that takes them to a page with a form that will allow them to reset their password.


```js
/**
* Step 11.6: forgot password route
**/
api.post('/auth/forgot_password', async (req, res) => {
  try {

    const user = await User.findOne({
      "email": req.body.email
    });

    if (!user) {
      throw new Error('no user found by that email address')
    }

    const token = await crypto.randomBytes(20).toString('hex');

    // add the reset password token and expiry date
    await User.findByIdAndUpdate({
      _id: user._id
    }, {
      reset_password_token: token,
      reset_password_expires: Date.now() + 86400000
    });

    const emailData = {
      to: user.email,
      from: config.MAILER.EMAIL,
      subject: '[list project] Forgot Password Request',
      html: `
        <div>
        <h1>Password reset for list project</h1>
        <p>
          url: http://localhost:3030/reset_password?token=${token}
          name: ${user.username}
        </p>
        </div>
      `
    }

    await emailer.sendMail(emailData);

    return res.json({status:'success', message:'Kindly check your email for further instructions'})

  } catch (err) {

    throw new Error('error in password reset')

  }
})
```

Notice how you can create your email as an HTML string and send that within a JSON object to your `emailer.sendMail()`. 

Once the email has been sent, you can send a JSON response indicating for the user to check their email address.

NOTE: make sure the `.env` variables for your emailer information are valid otherwise none of this will work.


## Step 11.7:  Reset password route

So your user forgets their password, requests the password reset, and now wants to reset their password with something new that they will remember or something they will rememeber to store in their password manager. Great. The next step will allow your users to submit a new password.

In this step you will:
* submit the reset_password_token as a property of `req.body` called `token` as well as the `newPassword` and the `verifyPassword` which should match the `newPassword`. 
* If the `newPassword` and `verifyPassword` match, then you can create a new password hash and replace the old password that had been forgotten and last, remove the reset_password_token and reset_password_token_expires properties in the user document.

```js
/**
* Step 11.7: reset_password route
**/
api.post('/auth/reset_password', async (req, res) =>{
  try{

    const user = await User.findOne({
      reset_password_token: req.body.token,
      reset_password_expires: {
        $gt: Date.now()
      }
    });

    if(!user){
      throw new Error('no matching token found!')
    }

    if(req.body.newPassword === req.body.verifyPassword){
      user.password = req.body.newPassword
      user.reset_password_token = undefined;
      user.reset_password_token_expires = undefined;

      await user.save()
      // TODO: need to send this back to the user
      await user.generateAuthToken();


      const emailData = {
        to: user.email,
        from: config.MAILER.EMAIL,
        subject: '[List Project] Password Reset Confirmation',
        html:`
          <div>
            <h1>Password Reset Confirmation</h1>
            <p>You've successfully reset your password.</p>
          </div>
        `
      }

      await emailer.sendMail(emailData)

      return res.json({status:'success', message:'password reset successful!'})
    } else {
      throw new Error('passwords do not match')
    }


  } catch(err){
    throw new Error(err)
  }

})
```
If a user makes a request to this route, you'll send them a nice email confirming that their password has been nicely reset. How nice of you!

<!-- ## Step 11.8:  Getting user info and scrubbing data

In case we want to build a user profile page, you can add in a api route for `/me`:

```js
/**
* Step 11.8: get user info
**/
api.get('/me', auth, scrub, async (req, res) => {
  try{
    res.json({status:'success', ...res.locals.cleanUser});
  } catch(err){
    throw new Error(err)
  }
})
```

Notice we have in our GET request, some middleware that calls `scrub`. This is middleware that removes sensitive information such as the email and the hashed password of the user. 

Let's create the middleware `middleware/scrub.js`.

In `middleware/scrub.js`:

```js
const scrub = async (req, res, next) => {

  try{
    if(req.user){
      const cleanUser = {
        username: req.user.username,
        _id: req.user._id,
      };
      
      res.locals.cleanUser = cleanUser;
    }
    next();
  } catch(err){
    next(err);
  }
}

module.exports = scrub;
```

This will return a "clean user" profile info. -->



## Testing the user API endpoints:

### /register

```sh
curl -X POST -H "Content-Type: application/json" -d '{"username": "joey", "email": "joeyklee@nyu.edu", "password": "super_secret_password"}' http://localhost:3030/api/v1/users/register
```

This will return:
```JSON
{"user":{"_id":"5e14ae003b084562075177b6","username":"joey","email":"joeyklee@nyu.edu","password":"$2a$08$xnHvlUdmyN89pUY8OP7Lr.f3xFPKiuoCix9zbQP2xb9eRpmpCK2jy","tokens":[{"_id":"5e14ae003b084562075177b7","token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI1ZTE0YWUwMDNiMDg0NTYyMDc1MTc3YjYiLCJpYXQiOjE1Nzg0MTM1Njh9.hnhUSscgffXsAEXNlCn4gAx0rfvy37_GZUTdNTAnkjY"}],"__v":1},"token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI1ZTE0YWUwMDNiMDg0NTYyMDc1MTc3YjYiLCJpYXQiOjE1Nzg0MTM1Njh9.hnhUSscgffXsAEXNlCn4gAx0rfvy37_GZUTdNTAnkjY"}
```

### /login

```sh
curl -X POST -H "Content-Type: application/json" -d '{"email": "joeyklee@nyu.edu", "password": "super_secret_password"}' http://localhost:3030/api/v1/users/login
```

This will return:
```json
{"user":{"_id":"5e14ae003b084562075177b6","username":"joey","email":"joeyklee@nyu.edu","password":"$2a$08$xnHvlUdmyN89pUY8OP7Lr.f3xFPKiuoCix9zbQP2xb9eRpmpCK2jy","tokens":[{"_id":"5e14ae003b084562075177b7","token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI1ZTE0YWUwMDNiMDg0NTYyMDc1MTc3YjYiLCJpYXQiOjE1Nzg0MTM1Njh9.hnhUSscgffXsAEXNlCn4gAx0rfvy37_GZUTdNTAnkjY"},{"_id":"5e14ae723b084562075177b9","token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI1ZTE0YWUwMDNiMDg0NTYyMDc1MTc3YjYiLCJpYXQiOjE1Nzg0MTM2ODJ9.t21NLxeNYbPghNomyEAq9Jn-y6bliqbmmAa_Bm02VBQ"}],"__v":2},"token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI1ZTE0YWUwMDNiMDg0NTYyMDc1MTc3YjYiLCJpYXQiOjE1Nzg0MTM2ODJ9.t21NLxeNYbPghNomyEAq9Jn-y6bliqbmmAa_Bm02VBQ"}
```

### /me/logout

Notice we use the auth token generated at **login** here. 

```sh
curl -X POST -H 'Accept: application/json' -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI1ZTE0YWUwMDNiMDg0NTYyMDc1MTc3YjYiLCJpYXQiOjE1Nzg0MTM2ODJ9.t21NLxeNYbPghNomyEAq9Jn-y6bliqbmmAa_Bm02VBQ" http://localhost:3030/api/v1/users/me/logout
```

This will return:

```json
{"status":"success","message":"logout complete"}
```

### /me/logoutall

Notice we use the auth token generated at **signup** here. 

```sh
curl -X POST -H 'Accept: application/json' -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI1ZTE0YWUwMDNiMDg0NTYyMDc1MTc3YjYiLCJpYXQiOjE1Nzg0MTM1Njh9.hnhUSscgffXsAEXNlCn4gAx0rfvy37_GZUTdNTAnkjY" http://localhost:3030/api/v1/users/me/logoutall
```

This will return:

```json
{"status":"success","message":"logout complete"}%
```

Your user will now have no login tokens attached to their account. New ones will be generated when they login again. 

### /auth/forgot_password

```sh
curl -X POST -H "Content-Type: application/json" -d '{"email": "joeyklee@nyu.edu"}' http://localhost:3030/api/v1/users/auth/forgot_password
```

If you set up your email environment variables correctly, you'll see:

```json
{"status":"success","message":"Kindly check your email for further instructions"}%
```

And, if you check your inbox you'll see:

![Email with the password reset for the list project screenshot](/assets/11_forgot_password_request.png)

NOTE: if you're using Gmail, you'll need to turn off a security setting that warns about your email being used by another service -- the "Less secure app access": gmail ==> account ==> security ==> less secure app access ==> set: on

### /auth/reset_password

```sh
curl -X POST -H "Content-Type: application/json" -d '{"email": "joeyklee@nyu.edu", "newPassword":"super_secret_password", "verifyPassword":"super_secret_password", "token":"5146095f50481f144c6eb6eb4562a14799e3742f"}' http://localhost:3030/api/v1/users/auth/reset_password
```
This will return:

```json
{"status":"success","message":"password reset successful!"}
```

And another email will fire with a confirmation:

![Password reset confirmation email screenshot](assets/11_password-reset-confirmation.png)

***
***
***
## Rest Stop:

At this point you've:
* created the reset_password route that takes the reset_password_token from the forgot_password request and the new password.

Note: these routes are not (and cannot) be authenticated... remember, your user forgot their password!

***
***
***


## Step 11.8: me, me, me

If your user wants to check their profile or you want to display it, it is handy to have a route for your user to get back their own info. 

More importantly, if you user wants to check out their own profile, you'll want to scrub out any password information. 


```js
/**
* Step 11.8: get user info
**/
api.get('/me', auth, scrub, async (req, res) => {
  try{
    res.json({status:'success', ...res.locals.cleanUser});
  } catch(err){
    throw new Error(err)
  }
  
})
```

Notice we add a **NEW middleware** here called `scrub`. 

Create a new file in your `/middleware` directory called `/middleware/scrub.js`.

In `/middleware/scrub.js`:

```js
const scrub = async (req, res, next) => {

  try{
    if(req.user){
      const cleanUser = {
        username: req.user.username,
        _id: req.user._id,
      };
      
      res.locals.cleanUser = cleanUser;
    }
    next();
  } catch(err){
    next(err);
  }


}

module.exports = scrub;
```
As you can see, we only pass on the `username` and `_id` and not the `email` or `password`. This is great! Protected users for the win!

But, where is the `req.user` and `req.user._id` coming from? Well, remember this is meant to come AFTER the `auth` middleware which will add the `user` data to the request. 

Now if your user requests their profile info, they will just get back their `username` and `_id`. 


## Add your user routes to your `index.js`

As we did with our posts, we will need to add our `user` routes to the main `index.js` file. 

In `index.js`

```js
/**
* Step 9: Set your user routes
**/
const userRoutes = require('./routes/user');
app.use('/api/v1/users', userRoutes);

```

## Add and Commit your changes

```
git add .
git commit -m "adds user routes"
```

***
***
***
## Rest Stop:

At this point you've:
* completed the user auth routes
* completed ALMOST all of the server side considerations. 

***
***
***


The last thing on our agenda is to tie up some loose ends in our `posts` route which we will add the `auth` middleware to our protected routes as well as add a middleware called `isOwner` which checks to see if a user is the owner of that post.


Continue onto the next step:
* ↳ [Step 12: authenticated posts routes](/12_authd-posts-routes.md)

