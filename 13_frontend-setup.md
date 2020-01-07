# Step 13: Front end setup and planning

Alright, if you've made it this far, kudos! You covered a lot of ground. Now you're going to build the front end to the list project. 

For the purpose of this exercise, we're going to stick to plain HTML5. We won't be using any express templating engine or any of the slick new front end frameworks like Vue.js or React.js. Maybe one day I'll make a version w/ vue.js (or link to another project that showcases the setup with those frameworks). For now, you're going to do this with the raw, vanilla HTML/JavaScript.

## A note on Styling

For the purpose of this tutorial I won't be covering the css styling. You're more than welcome to have a look at all this, but in general the focus here is the mechanics of setting up this project rather than the layout and styling. 

I will be following the BEM style convention of CSS class naming to the best of my ability.

You can find the complete stylesheet here: 
[Link to CSS stylesheet for the frontend]()

## Roadmap

Before you begin, take the time to make some comments in your HTML to define the sections of your `index.html` page. 

Here is a commented version of the html:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="ie=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />

    <title>List Project</title>

    <link rel="stylesheet" href="css/main.css" />
    <link rel="icon" href="images/favicon.png" />
  </head>

  <body>
    <div id="app">
    <!-- 
      * Section: Navigation 
      * includes any URLs to other pages
    -->

    
    <!-- 
      * Section: header 
      * includes the h1 and subtitle
    -->


    
    <!-- 
      * Section: main 
      * this is where your posts will go
    -->


    </div>
    <!-- JS Scripts -->
    <script src="js/main.js"></script>
  </body>
</html>
```

Notice we wrap everything in a div with an `id="app"`.

## Adding a nav section

Let's create a `<nav>` section to contain your site's navigation. 

```html
<!-- 
  * Section: Navigation 
  * includes any URLs to other pages
-->
<nav class="nav">
  <ul class="nav__list nav__list--left">
    <li class="nav__list__item"> <a class="link" href="/">Home</a></li>
  </ul>
  <ul class="nav__list nav__list--right">
    <li class="nav__list__item"> <a class="link" href="/login">login</a></li>
    <li class="nav__list__item"> <a class="link" href="/signup">signup</a></li>
  </ul>
</nav>
```

Our navigation will have 3 routes:
* home
* login
* signup

You will have to create the login and signup pages. We will get to this in the next section. For now, those URLs won't go anywhere. For now, let's add in our header section. 

## Adding a header section


```html
<!-- 
  * Section: header 
  * includes the h1 and subtitle
-->
<header class="header">
  <h1 class="header__title">The List Project</h1>
  <h2 class="header__subtitle">Behold! Links from across the web!</h2>
</header>
```

## Adding the main view: part 1 - a form to submit new links



## Adding the main view: part 2 - a section to render the links