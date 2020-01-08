# Step 15: add view routes

Let's add in the routes that will send the HTML for the following routes:
* /login
* /signup
* /reset_password

## Add route references to your server

We can do this by heading over to `index.js` on the server and adding in the URL references to where those views will be rendered.

```js
/**
* Step 6: set the path to your index.html file
**/
app.get("/", (req, res) => {
  res.sendFile('/')
})

// login
app.get("/login", (req, res) => {
  res.sendFile(publicPath + '/login.html')
})
// signup
app.get("/signup", (req, res) => {
  res.sendFile(publicPath + '/signup.html')
})
// reset_password
app.get("/reset_password", (req, res) => {
  res.sendFile(publicPath + '/reset_password.html')
})
```

## Add the `.html` files to your `/public` directory:

Now you can create 3 files in `/public`:
* `login.html`
  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="ie=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />

    <title>List Project - Log in</title>
    <link rel="icon" href="images/favicon.png" />
  </head>

  <body>
    <script src="js/Login.js"></script>
    <script>
      // we're going to code directly here
    </script>
  </body>
  </html>
  ```
* `signup.html`
  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="utf-8" />
      <meta http-equiv="x-ua-compatible" content="ie=edge" />
      <meta name="viewport" content="width=device-width, initial-scale=1" />

      <title>List Project - Sign up </title>
      <link rel="icon" href="images/favicon.png" />
    </head>

    <body>
      <script src="js/Signup.js"></script>
      <script>
        // we're going to code directly here
      </script>
    </body>
  </html>
  ```
  
* `reset_password.html`
  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="utf-8" />
      <meta http-equiv="x-ua-compatible" content="ie=edge" />
      <meta name="viewport" content="width=device-width, initial-scale=1" />

      <title>List Project - reset password</title>
      <link rel="icon" href="images/favicon.png" />
    </head>

    <body>
      <script src="js/ResetPassword.js"></script>
      <script>
        // we're going to code directly here
      </script>
    </body>
  </html>
  ```

And whenever you navigate to `/login`, `/signup`, or `reset_password`, you will be sent to those respective html pages. 




