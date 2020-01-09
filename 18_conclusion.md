# Conclusion & Next steps

Hey! You've made it! You built this whole application. Bravo. You should be proud to have gone through all of these steps and made this thing. It's not a small task!

If you've made it here, then I hope you've been able to get a feeling for all the components of doing authentication/authorization using JWT and cookies. 

## Recap:

Here's a recap of the journey we just took together:
* We installed all the dependencies to make this web application work.
* We created a scaffold of our web application and used as best-of-practices as we could to stay organized by putting files into the folders where they are relevant
* We configured our `config.js` file to take care of all of the variables we might need through the project and hid our important and secret variables in our `.env` file
* We set up our node application using express.js
* We defined the posts schema for mongodb
* We defined the posts API routes using express
* We defined the user schema for mongodb and added in those helper functions to help handle validation and password hashing.
* We set up our emailer client
* We set up our `auth` middleware that checks to see if we're getting an `auth_token` cookie from a client request.
* We set up our users API routes
* We set up middleware to check if a client request is coming from the authorized user using `isOwner` and added the `auth` and `isOwner` middleware to the relevant posts API routes.
* We setup our front end and organized our functionality into different JavaScript classes to stay organized :) 
* We built a full stack web application to collect links with our friends from across the internet.

## What we didn't do / challenges for you:

I will be straight up and acknowledge that the application still needs a lot of refinement and cleaning up. Some things on my list:
* showing the user if they are logged-in or not. 
* only showing the `edit/delete` buttons for the users that are authorized to `edit/delete` that specific feature.
* cleaning up the UI
* polishing up things with nice css animations, hover effects, etc. 
* deploy the application to some service. 

## Where you can deploy this application:
* my go-tos are:
  * Heroku
  * Glitch

These should work pretty much right out of the box. So if you put your project up on one of those sites, it should work like cake!

## Acknowledgements

* ITP at NYU for their support 
* The tutorial which this is based off of:https://medium.com/swlh/jwt-authentication-authorization-in-nodejs-express-mongodb-rest-apis-2019-ad14ec818122
* The tutorials by Tania Rascia: https://www.taniarascia.com/full-stack-cookies-localstorage-react-express/
* The OpenSource community
* Tachyons.io for color inspiration
* Discoteca Synthetica for the music that was played while making this. https://soundcloud.com/studio_malo/discoteca-synthetica
* And you!

