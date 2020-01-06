
## Stop 1: Pre-Setup & Installation

The following instructions are for `macOS` which is the operating system I'm running. If you're a windows user, I'd love your contribution on how to setup these dependencies ❤.

### Install Homebrew

```sh
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

### Install Node.js with NVM

```sh
# use homebrew to install nvm
brew install nvm

# make a directory for nvm
mkdir ~/.nvm/
```

open your `.bash_profile` or create a file in your `~/.bash_profile` and include the following:

```txt
export NVM_DIR=~/.nvm
source $(brew --prefix nvm)/nvm.sh
```

Check to see if nvm is working:

```sh
nvm ls
```

This will list a which node versions are currently installed. If none are installed then you'll see a bunch of `N/A`s.

Now install node version `v10.15.0` which is what this tutorial was built on:

```sh
nvm install 10.15.0
```

And now you can set it to default to that version:

```sh
nvm alias default 10.15.0
```

via: https://www.chrisjmendez.com/2018/02/07/install/

NOTE: At the time of this writing, I would avoid node versions less thatn`10.0` since many services are beginning to deprecate their support for those versions.

### Install MongoDB

```sh
brew install mongodb
```

If this is your first time installing mongodb, you'll need to create directory for your database and also make sure to give your machine the proper read/write permissions. Do this by:

```sh
# go to your root
cd 
# make a directory called data/db
mkdir -p /data/db

# use `sudo chown` to set the permissions
sudo chown -R `id -un` /data/db
```

And voila! You can now start your mongodb by running:
```sh
mongod
```

and stopping mongo by pressing the following in the same terminal window: `ctl` + `c` 

If you'd like to have visual interface for the mongodb I can recommend:
* Robo3T: https://robomongo.org/download

***
***
***

## Rest Stop: 
So far we have:
* Installed all of our major dependencies:
  * node.js
  * mongodb

If these are not installed, please make sure to take care before proceeding.

***
***
***