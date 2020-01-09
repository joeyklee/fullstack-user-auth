# Step 9: Set up the emailer

Having an emailer set up is an important part of your auth system because it allows you to:
* email people a confirmation that their account has been created.
* email people a temporary token if they forget their password and want to reset it
* confirm with people if they want to delete their account. 
* send notice to your users in case you need to notify them for any reason.

We are going to set up our emailer using the `nodemailer` module. 

For the purpose of this project, set up your `emailer` as a `helper` in the `/helpers` directory. 

Create a file called `emailer.js` in the `/helpers` directory.

In the `emailer.js` file:

```js
const config = require('../config');
const nodemailer = require('nodemailer');

const smtpTransport = nodemailer.createTransport({
  service: config.MAILER.SERVICE,
  auth: {
    user: config.MAILER.EMAIL,
    pass: config.MAILER.PASSWORD
  }
});

module.exports = smtpTransport;
```

Notice how our nodemailer is going to get the `service`, `user`, and `pass` from our `config` file.

## Add and Commit your changes

```
git add .
git commit -m "adds users emailer"
```

***
***
***
## Rest Stop:

At this point you've:
* created a `emailer` that uses SMTP transport using the nodemailer module that uses in the email service defined in the `.env` and config file.

***
***
***

Before we go on to defining and writing our `user` routes, we are are going to make one more quick stop to create a middleware called `middleware/auth.js` that you will be able to add to any of your routes that will allow you to check if an authentication token has been passed in with the request. 

Continue onto the next step:
* ↳ [Step 10: auth middleware](/10_auth-middleware.md)


