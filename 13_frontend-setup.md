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

```html
<main class="main">
  <!-- Part 1: a form to submit new posts if authenticate -->
  <section class="section__submit">
    <h3 class="section__submit__title">· Add to list ·</h3>
    <form class="link__form">
      <!-- input for link -->
      <fieldset class="link__form__section">
        <legend class="link__form__section__title">link</legend>
        <input class="link__form__section__input link__form__section__input--text" type="text" name="link" placeholder="e.g. https://itp.nyu.edu">
      </fieldset>
      <!-- input for title -->
      <fieldset class="link__form__section">
        <legend class="link__form__section__title">title</legend>
        <input class="link__form__section__input link__form__section__input--text" type="text" name="title" placeholder="e.g. ITP Homepage website">
      </fieldset>
       <!-- input for description -->
       <fieldset class="link__form__section">
        <legend class="link__form__section__title">description</legend>
        <textarea class="link__form__section__input link__form__section__input--textarea" name="description" placeholder="e.g. ITP is a grad school program about the recently possible."></textarea>  
      </fieldset>
      <input class="link__form__section__input link__form__section__input--button" type="submit" value="🚀 share this link">
    </form>
  </section>

  <!-- part 2: list of all the posts -->
  <!-- ... -->
```


## Adding the main view: part 2 - a section to render the links

```html
<!-- part 2: list of all the posts -->
<section class="section__view">
  <h3 class="section__view__title">· all posts ·</h3>
  <ul class="section__view__posts">
    <!-- this is where your posts will be filled in with javascript -->
  </ul>
```

![image of site with the markup as shown](/assets/13_list-without-placeholder.png)

The markup for all of our `li` elements in our `.section__view__posts` will look like the following html markup. If you were to add this in, the site would look like the image below. What we will do is to fill in the text within the `{{}}` using javascript.

```html
<li class="section__view__post">
  <div class="section__view__post__functions">
    <button>edit</button>
    <button>delete</button>
  </div>
  <h4 class="section__view__post__title">
    <a href="{{URL}}" target="_blank" rel="noreferrer" 
      class="section__view__post__link section__view__post__link--large">
      {{title with link}}
    </a>
  </h4>
  <p class="section__view__post__description">
    {{link description}}
  </p>
  <ul class="section__view__post__meta">
    <li class="section__view__post__meta__section section__view__post__meta__username">Submitted by: {{name}}</li>
    <li class="section__view__post__meta__section section__view__post__meta__link">
      <a href="{{URL}}" class="section__view__post__link--small">{{https://itp.nyu.edu}}</a>
    </li>
  </ul>
</li>
```

![image of site with placeholder html markup showing how each data property will be mapped to the HTML](/assets/13_list-with-placeholder.png)

## Add and Commit your changes

```
git add .
git commit -m "adds frontend home setup"
```


***
***
***
## Rest Stop:

At this point you've:
* added in the placeholder html
* added sections for a nav bar, header, post submission form, and a view to render the posts.
  
A few considerations we will have to solve:
* notice the `edit` button. How will me edit our posts? There are elegant ways to handle this (e.g. using something like Model-View-Controller) structure, but we might settle here for a more simple approach. Just something to note!
* currently our `/login` and `/signup` routes do not go anywhere. Also remember we will need a view for `/reset_password`. We will have to add these views in our `/public` directory as well as add in our server `index.js` the URL to those routes. 
* We will address these in the following sections. Stay with me, you're doing great!

***
***
***

Continue onto the next step:
* ↳ [Step 14: the Posts class](/14_posts-class.md)

