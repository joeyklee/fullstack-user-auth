# Fullstack User Registration and Authentication Tutorial w/ Node.js/Express.js, MongoDB, and JWT
Tutorial on user registration and authentication with node.js/express.js, Mongodb, and HTML5

## Pre-requisites
* Level: intermediate - advanced
* Requirements: working/proficient knowledge of node.js, express.js, mongodb, and HTML5. I will be assuming some knowlege of these tools, but will try to make things as clear as possible.
* Adapted from: https://medium.com/swlh/jwt-authentication-authorization-in-nodejs-express-mongodb-rest-apis-2019-ad14ec818122

## About

If you're here, this is probably the situation you've found yourself in -- you're building a web application and you want people to be able to **signup**, **log-in**, and **view**, **add**, **edit** and **delete** data. Also important is that people have different **permissions** to view, add, edit, and delete data/content across your application. 

If this sounds relevant, then keep reading, you're in good company!

This tutorial is about setting up the following:
* On the server side: we will set up **registration** and **authentication**. We will also set up a simple method of handling **permissions** and also allow people to request a **password reset**. This will follow the standard CRUD (create, read, update, delete) style. 
* On the client side: we will set up the HTML and JS to allow people to **register**, **log in**, and perform the CRUD operations on data living in our database. We will use JSON Web Tokens (JWT) and cookies to handle persisting logins.


