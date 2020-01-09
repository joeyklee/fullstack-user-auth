# Step 14: Signup.js

First thing's first: Let's create the methods to allow your users to sign up. 

As you can see, we are doing a few things here:
1. We select the `signup__form` using a query selector.
2. We define the route in our API that allows us to `register` (aka sign up)
3. We add an event listener to our form that says, 'when the form is submitted -- when the user presses "submit" -- make a POST request to the `signupURL`". 

```js
class Signup {
  constructor(){
    this.signupForm = document.querySelector('.signup__form');
    this.signupURL = "http://localhost:3030/api/v1/users/register"

    this.init();
  }

  init(){
    this.signupForm.addEventListener('submit', async e =>{
      e.preventDefault();
      await this.submitSignup();
    });
  }

  /**
  * submit signup
  */
  async submitSignup(){
    try {
      const userData = {
        username: this.signupForm.username.value,
        email: this.signupForm.email.value,
        password: this.signupForm.password.value
      }
      const options = {
        method:'POST',
        headers: {
          'Accept': 'application/json',
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(userData)
      }

      let data = await fetch(this.signupURL, options);
      data = await data.json();
      // after signup up send the user to the home page
      alert('signup successful!')
      window.location.href = '/';
    } catch (error) {
      alert(error);
    }
  }
}
```


## Add and Commit your changes

```
git add .
git commit -m "adds Signup.js"
```

***
***
***
## Rest Stop:

At this point you've:
* added the javascript functionality to allow users to signup with your service

A few notes:
* remember: passwords must be greater than 7 characters!

***
***
***


Continue onto the next step:
* ↳ [Step 15: the Login class](tutorial/15_login-js.md)

