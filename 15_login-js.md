# Step 15: Login.js

Now that your users can sign up, they must also be able to log in to your application.

```js
class Login {
  constructor() {
    this.loginForm = document.querySelector('.login__form');
    this.forgotForm = document.querySelector('.forgot__form');
    this.logoutButton = document.querySelector('#logout-button');
    this.loginURL = 'http://localhost:3030/api/v1/users/login';
    this.logoutURL = 'http://localhost:3030/api/v1/users//me/logoutall';
    this.forgotURL = 'http://localhost:3030/api/v1/users/auth/forgot_password';

    this.init();
  }

  init() {
    this.loginForm.addEventListener('submit', async e => {
      e.preventDefault();
      await this.submitLogin();
    });

    this.forgotForm.addEventListener('submit', async e => {
      e.preventDefault();
      await this.submitForgotPassword();
    });

    this.logoutButton.addEventListener('click', async e => {
      e.preventDefault();
      await this.submitLogout();
    });
  }

  /**
   * submit signup
   */
  async submitLogin() {
    try {
      const userData = {
        email: this.loginForm.email.value,
        password: this.loginForm.password.value
      };
      const options = {
        method: 'POST',
        headers: {
          Accept: 'application/json',
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(userData)
      };

      let data = await fetch(this.loginURL, options);
      data = await data.json();
      // after signup up send the user to the home page
      alert('login successful!');
      window.location.href = '/';
    } catch (error) {
      alert(error);
    }
  }

  /**
   * logout
   */
  async submitLogout() {
    try {
      const options = {
        method: 'POST',
        credentials: "same-origin",
      };

      let data = await fetch(this.logoutURL, options);
      data = await data.json();
      // after signup up send the user to the home page
      alert('logout successful!');
      window.location.href = '/';
    } catch (error) {
      alert(error);
    }
  }

  /**
   * submit forgot password
   */
  async submitForgotPassword() {
    try {
      const userData = {
        email: this.forgotForm.email.value
      };
      const options = {
        method: 'POST',
        headers: {
          Accept: 'application/json',
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(userData)
      };

      let data = await fetch(this.forgotURL, options);
      data = await data.json();
      // after signup up send the user to the home page
      alert(
        'An email was sent to your inbox. Please check your inbox and spam folder to reset your password.'
      );
      // window.location.href = '/';
    } catch (error) {
      console.log(error);
      alert(error);
    }
  }
}



```


## Add and Commit your changes

```
git add .
git commit -m "adds Login.js"
```

***
***
***
## Rest Stop:

At this point you've:
* added the javascript functionality to allow users to login to your service and submit a password reset if they forget their password.

A few notes:
* It would be nice to be able to show the username of the current user that is authenticated. 

***
***
***


While we've added the ability to log in to your application, we now need to make sure to create the functionality and the view to allow users to reset their passwords. 

Continue onto the next step:
* ↳ [Step 16: the password reset](/16_password-reset.md)

