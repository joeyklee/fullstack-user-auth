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
    

    <!-- TODO -->

    </div>
    <!-- JS Scripts -->
    <script src="js/main.js"></script>
  </body>
</html>
```

Notice we wrap everything in a div with an `id="app"`.

