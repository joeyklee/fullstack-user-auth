# Stop 2: Setup

We are going to start from null, zero, nada, nothing, a blank canvas onto which we will make our masterpiece. 

Fire up your terminal window and and make a new project folder:

```sh
cd  ~/your/preferred/parent/directory
mkdir list-project
```

Change into the `/list-project` directory:

```sh
cd list-project
```

Now we will create our application directory structure. It is going to look like this:

```
README.md
config.js
index.js
package.json
.env
.gitignore
/db
/helpers
/middleware
/models
/public
/routes
/node_modules
```

where:
* README.md
  * *FILE*: Our readme file that explains how to set up your project, documentation, etc
* config.js:
  * *FILE*: This is where  
* index.js:
  * *FILE*: where our main node.js/express server code will live
* package.json:
  * *FILE*: This is where our node application information is stored.
* .env:
  * *FILE*: This is where we will hide our secret application information.
* .gitignore:
  * *FILE*: This is where we will let `git` know which files and folders to ignore for version control.
* /db
  * *DIRECTORY*: the directory that has the information regarding our mongodb database  
* /helpers
  * *DIRECTORY*: the directory where any handy helper functions will live
* /middleware
  * *DIRECTORY*: the directory where our express middleware will live
* /models
  * *DIRECTORY*: the directory where our mongodb data model schemas will live
* /public
  * *DIRECTORY*: the directory where our client side code will live
* /routes
  * *DIRECTORY*: the directory where our API routes for our `users` and `links`.
* /node_modules
  * *DIRECTORY*: the directory where npm will install all our application dependencies

For short, you can do the following:

```sh
# initialize the project as a git repo
git init

# create a package.json and initialize with all defaults
npm init -y

# create the files
touch README.md config.js index.js .env .gitignore

# create the directories
mkdir db helpers middleware models public routes
```

![Screenshot of directory structure after running the above commands](assets/setup_dir-structure.png)


## install node dependencies

Our last step in our setup is going to be installing the necessary node modules depedencies that our application will rely on. We will be installing the following:

* **general**:
  * dotenv
  * express
* **database**:
  * mongodb
  * mongoose
* **authorization/authentication**:
  * bcryptjs
  * cookie-parser
  * cors
  * jsonwebtoken
  * validator
* **email validation**:
  * nodemailer

We can install them by doing `npm install`:

```sh
npm install bcryptjs cookie-parser cors dotenv express jsonwebtoken mongodb mongoose nodemailer validator
```

You can check your `package.json` to see that the `depencies` property has now been updated.

Now make sure to add these to git:

```sh
git add .
git commit -m "adds project scaffold"
```

***
***
***
## Rest Stop:

At this point we've:
* scaffolded out our application with the main files in our root directory and main folders which will help us organize our project files.
* used `npm install` to install our project dependencies.

***
***
***


Now that we've scaffolded our project directory and installed our project dependencies, it is time to start filling in our project.

Continue onto the next step:
* ↳ [Step 3: Configure the config](tutorial/03_configure-the-config.md)