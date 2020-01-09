# Step 16: Reset Password view

In this step, we're going to do the following:
1. Add the HTML view for our `/reset_password` view as a **seperate** `.html` file in our public directory.
2. Add the view route to our `index.js` on our server.
3. Add the functionality to handle the reset_password on the client.

## Step 16.1: Adding `reset_password.html` to `/public`

So far we've added our login and signup to the `index.html`, but we want to have a separate view for resetting a user's password. 

Add `reset_password.html` to to `/public` directory.

In `/public/reset_password.html`, add the following:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="ie=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />

    <title>List Project - reset password</title>
    <link rel="stylesheet" href="css/main.css" />
    <link rel="icon" href="images/favicon.png" />
  </head>

  <body>
    <div class="reset">
      <section class="reset__section">
        <h2 class="reset__section__title">Reset Password</h2>
        <form class="form reset__form">
          <fieldset class="form__section">
            <legend class="form__section__title">email</legend>
            <input class="form__input" type="email" name="email" placeholder="e.g. youremail@mail.com">
          </fieldset>
          <fieldset class="form__section">
            <legend class="form__section__title">new password</legend>
            <input class="form__input" type="password" name="newpassword" placeholder="e.g. super_secret_password">
          </fieldset>
          <fieldset class="form__section">
            <legend class="form__section__title">verify password</legend>
            <input class="form__input" type="password" name="verifypassword" placeholder="e.g. super_secret_password">
          </fieldset>
          <input class="form__input--button--pink" type="submit" value="reset">
        </form>
      </section>
      <a class="reset__link" href="/">back home ↩</a>
    </div>
    <!-- Scripts -->
    <script src="js/ResetPassword.js"></script>
    <script>
      // we're going to code directly here
      window.addEventListener('DOMContentLoaded', function(){
        const resetPassword = new ResetPassword();
      });
    </script>
  </body>
</html>
```

Notice that we've added at the bottom the `scripts` for `ResetPassword.js` and initialized the functionality by instantiating the class. 

Let's add the `ResetPassword.js` class now.

## Step 16.2: ResetPassword.js

Like the `Login.js` and `Signup.js`, the `ResetPassword.js` will follow a very similar structure. 


In `public/js/ResetPassword.js`:

```js
class ResetPassword {
  constructor() {
    this.resetForm = document.querySelector('.reset__form');
    this.resetURL = 'http://localhost:3030/api/v1/users/auth/reset_password';

    this.init();
  }

  init() {
    this.resetForm.addEventListener('submit', async e => {
      e.preventDefault();
      await this.submitReset();
    });
  }

  /**
   * submit signup
   */
  async submitReset() {
    try {
      const queryParams = new URLSearchParams(window.location.search);
      const reset_token = queryParams.get('token');
      const userData = {
        email: this.resetForm.email.value,
        newPassword: this.resetForm.newpassword.value,
        verifyPassword: this.resetForm.verifypassword.value,
        token: reset_token
      };
      const options = {
        method: 'POST',
        headers: {
          Accept: 'application/json',
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(userData)
      };

      let data = await fetch(this.resetURL, options);
      data = await data.json();
      // after signup up send the user to the home page
      alert(data.message);
      window.location.href = '/';
    } catch (error) {
      alert(error);
    }
  }
}

```

One notable aspect here is how we get the `token` from the query parameter of the browser window that was attached in the forgot_password email.

Now that we've added `ResetPassword.js`, the last thing to do is add the view endpoint to our `index.js` on the server.

## Step 16.3: add the view route to `/reset_password` in `index.js`

Head over to the `index.js` file. Under the code block where you defined the route `app.get('/', ...)` add the following:

```js
// reset_password
app.get("/reset_password", (req, res) => {
  res.sendFile(publicPath + '/reset_password.html')
})
```

That section will look like:

```js
/**
* Step 6: set the path to your index.html file
**/
app.get("/", (req, res) => {
  res.sendFile('/')
})

// reset_password
app.get("/reset_password", (req, res) => {
  res.sendFile(publicPath + '/reset_password.html')
})
```

Make sure to save all your files so these are updated and now you should have a working view route if you go to:
`http://localhost:3030/reset_password`

![Screenshot of reset password page](/assets/16_reset-page.png)



## Add and Commit your changes

```
git add .
git commit -m "adds reset password functionality"
```

***
***
***
## Rest Stop:

At this point you've:
* added the views and javascript functionality to allow users to reset their password after they've submitted a password reset request.

***
***
***

Only 2 more sections to go! In the next section we are going to add the functionality to handle rendering, adding, editing, and deleting our posts. 

Continue onto the next step:
* ↳ [Step 17: the Posts class](/16_posts-js.md)

