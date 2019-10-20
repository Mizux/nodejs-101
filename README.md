| Linux                                           |
|-------------------------------------------------|
| [![Build Status][travis_status]][travis_builds] |

[travis_status]: https://travis-ci.org/Mizux/nodejs-101.svg?branch=master
[travis_builds]: https://travis-ci.org/Mizux/nodejs-101

# Introduction
My repository about FOSS web development using the Travis-CI infrastructure for
 testing.

### Project directory layout
Thus the project layout is as follow:

* [package.json](package.json) Dependencies list consumed by [npm](https://www.npmjs.com/) and [yarn](https://yarnpkg.com/lang/en/).
* [index.js](index.js) entry point of the nodejs server.
* [ci](ci) Top-level directory for Makefile/docker Continous Integration testing.

## CI: Makefile/Docker testing
To test the build, there is a Makefile in [ci](ci) folder using
docker containers to test on various distro.

To get the help simply type:
```sh
cd ci
make
```
note: you can also use
```sh
make --directory=ci
```

For example to test the server inside an ubuntu container:
```sh
make --directory=ci test_ubuntu
```

# License
Apache 2. See the LICENSE file for details.

# Project

## Step 1: Environment Setup
First, setup a native development environment on various GNU/Linux distro
or using the official [node:alpine](https://hub.docker.com/_/node/) docker image.  

You'll need:
- **nodejs**,
- **npm** and
- **yarn**.

note: Look at [ci/docker/<you_distro>/Dockerfile](ci/docker) for this step.

## Step 2: Create a new project

Simply run the following command and fill the form:
```sh
yarn init
```

You should have something like this
```sh
$ yarn init
yarn init v1.19.1
question name (nodejs-101): 
question version (1.0.0): 
question description: nodejs test
question entry point (index.js): 
question repository url (git@github.com:Mizux/nodejs-101.git): 
question author (Mizux Seiha <mizux.dev@gmail.com>): 
question license (MIT): Apache-2.0
question private: 
success Saved package.json
Done in 78.63s.
```

## Step 3: Add Dependencies
Let's add [express](http://expressjs.com/) as dependency using:
```sh
yarn add express
```

## Step 4: Install Dependencies
Let's install them using:
```sh
yarn install
```

## Step 5: Launch the nodejs server
First we will add a [script](https://yarnpkg.com/en/docs/package-json#toc-scripts) to package.json by adding:
```json
{
  "scripts": {
    "start": "node index.js"
  }
}
```

To run this nodejs server simply use:
```sh
yarn start
```
