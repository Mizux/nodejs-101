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

## Nodejs Project Build
To run this node project, as usual:
```sh
node server.js
```

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


# Environment Setup
First things first, let's setting up a native development environment on various GNU/Linux distro or using a docker container.  

You'll need:
- **nodejs**,
- **npm** and
- **yarn**.

note: Look at `ci/docker/<distro>/Dockerfile` for install script.
