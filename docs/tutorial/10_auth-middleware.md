# Step 10: Auth middleware

Whether or not you're familiar with express.js, you should know that express.js's power lies in the use of `middleware`. For this project, you have used middleware for a number of things like handling `CORS` or telling your app to handle data in `json`. 

In this section you're going to create your own middleware that allows you to check if an `authentication token` has been sent with a request. If an authentication token is sent in the header of a user's request, then for those routes requiring authentication, your app will allow the request to continue, otherwise an error will be thrown.

## Create your auth middleware

Your auth middleware will check the request for a property called `auth_token` that is in the request's cookies. 

Cookies are a decent way of handling storing things like auth tokens (better not to store these tokens in localStorage as this is a security risk!), therefore your will opt for this option for taking care of authenticated status between your client and server.

Create a file called `middleware/auth.js`. 

In your `middleware/auth.js`:

```js
const jwt = require('jsonwebtoken');
const User = require('../models/user');
const config = require('../config');

const auth = async (req, res, next) => {

  try{

    let token;

    if(req.cookies.auth_token ){
      token = req.cookies.auth_token;
    } else if(req.header('Authorization')) {
      token = req.header('Authorization').replace('Bearer ', '');  
    } else{
      throw new Error('no token given')
    }


    const data = jwt.verify(token, config.JWT_KEY)
    const user = await User.findOne({_id: data._id, 'tokens.token':token})

    if(!user){
      throw new Error({error: 'error logging in'})
    }

    req.user = user;
    req.token = token;

    next();
  } catch(err){
    res.status(401).send({ error: 'Not authorized to access this resource' })
  }
}

module.exports = auth;
```

As mentioned above, this middleware takes a request, checks to see if a cookie is present with a property `auth_token` and goes through the necessary steps to check your users collection to see if the token matches any of the tokens of existing users. 

## Add and Commit your changes

```
git add .
git commit -m "adds auth middleware"
```

***
***
***
## Rest Stop:

At this point you've:
* created auth middleware that will allow you to create routes requiring authentication before proceeding.

***
***
***

Continue onto the next step:
* ↳ [Step 11: defining users routes](tutorial/11_users-routes.md)
