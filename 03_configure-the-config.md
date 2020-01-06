# Step 3: Configure your Config

In this step we're going to take a second to configure our `config.js` file. This will give us some clues to the types of considerations you will need to make with regards to important variables that store things like your server's `IPADDRESS`, the `PORT` number your project is being served on, the `email` address you're using for email validation, etc. 

Let's take a look.

## Adding variables to the config.js file

Add the following code to your `config.js` file:
```js
// requiring dotenv will set all of the .env variables and make them available at process.env.X
require('dotenv').config();
// we use the module.exports pattern to export all of these variables which will then be available as `config.X`
module.exports = {
    PORT: process.env.PORT || 3030,
    JWT_KEY: process.env.JWT_KEY,
    MONGODB_URI: process.env.MONGODB_URI || 'mongodb://localhost:27017/list-project',
    MAILER:{
        EMAIL: process.env.MAILER_EMAIL || 'test@example.com', 
        PASSWORD:process.env.MAILER_PASSWORD || 'secret',
        SERVICE: process.env.MAILER_SERVICE || 'Gmail',
        DEFAULT_ADDRESS: process.env.MAILER_ADDRESS || 'First Last <test@example.com>'
    }
}
```

What is happening here is that we're saying: 
> Here's all these variables we will be using in our project. If we define any of these in our `.env` file, use the given values (e.g. process.env.PORT). If we do not define any variables there, then default to those values given after the `||`. 

Now make sure to add these to git:

```sh
git add .
git commit -m "adds config file"
```

***
***
***
## Rest Stop:

At this point we've:
* added all of our configuration values into the `config.js` file.

***
***
***

Now that we've configured our `config.js` file, we can set our environment variables in the `.env` file. This is good practice so that you do not accidentally check in your important credentials that you don't want others to know (e.g. your email address and password etc).

Continue onto the next step:
* ↳ [Step 4: Setting the environment variables](/04_setting-env.md)





