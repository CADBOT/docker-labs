# Docker Commit
Docker commit is an easy way to create new images from existing ones. While for
images you plan on maintaining, it usually makes sense to create the image from
a dockerfile, there are some scenarios docker commit really makes sense.

## Practice
One of my typical use cases is installing development tools in an image I'm
creating for debugging purposes. Most images are minimal by design and won't
include typical tools such as curl. Let's create a new ubuntu image that has
curl installed

* docker container run -it --name commitme ubuntu bash
* apt-get update
* apt-get install curl
* exit
* docker commit commitme ubuntuwithcurl
* docker container run -it ubuntuwithcurl bash
* curl

## Application
While there is an offical Docker hub image for python, it might not be on the
operating system of your choice. Try creating a container from ubuntu - or the
base OS of our choice - and installing Python on it. Then use docker commit
to create a new image

# Dockerfiles

## Practice
Let's create an Ubuntu image with curl built in, but this time let's do so
using a Dockerfile. Create a file called Dockerfile with the following contents

```
FROM ubuntu:16.04
RUN apt-get update
RUN apt-get install -y curl
```
Then build and tag an image using the Dockerfile
* docker image build -t ubuntuwithcurl2 .
* docker container run -it ubuntuwithcurl2 bash
* curl

## Application
Try recreating the image with Python built in, but this time using a Dockerfile
instead

# Image hierachies
Given that images can inhereit from other images, let's build some new images
based on the ones we have created

## Practice
Another common networking tool is traceroute. Let's build a new image from
ubuntuwithcurl that also has traceroute

Start by creating the following Dockerfile
```
FROM ubuntuwithcurl
RUN apt-get install -y traceroute
```
Then build an image from it and run it
* docker build -t ubuntuwithcurlandtrace .
* docker container run -it ubuntuwithcurlandtrace .
* traceroute

# Advanced Dockerfile commands

## Practice CMD
Lets build a ruby container that will drop you into an
interactive ruby session of no container is specifed First,
create the following Dockerfile
```
FROM ubuntu:16.04
RUN apt-get update
RUN apt-get install -y ruby
CMD irb
```
* docker image build -t ubuntuwithruby .
* docker container run -it ubuntuwithruby
* 2 + 2
* ctrl d

## Application
Try modifying your python dockerfile to automatically drop you
in to a python terminal session when no command is specified

# Dockerizing an application
## Practice
Let's begin by dockerizng a node.js application. The steps involved
are installing node.js inside the Dockerfile, adding our source code,
installing our source code's dependencies, and starting the application

Create the following Dockerfile
```
FROM ubuntu:16.04
RUN apt-get update
RUN apt-get install -y nodejs npm
RUN mkdir /www
WORKDIR /www
COPY . /www
RUN npm install
EXPOSE 8080
CMD nodejs index.js
```
Then copy all the node.js app source code - index.js package.json file -
into the same directory as the Dockerfile and run the command

* docker image build -t nodeapp .

Run in the container, port map it, and try to get a response

* docker container run -p 8080:8080 -d nodeapp
* curl http://localhost:8080

# Base images with onbuild
## Practice

Instead of dockerizing the node app directly as we did in the prior lab, let's
create a base image that does the work we want in every image, and we will
create an image that uses it as a base, only applies configuration. In this
case we will make base image that installs the dependencies, and the
command for starting the app - via the ONBUILD instruction - and specify
the port for the app in a different Dockerfile

Creat the following Dockerfile

```
FROM ubuntu:16.04
RUN apt-get update
RUN apt-get install -y nodejs npm
RUN mkdir /www
WORKDIR /www
ONBUILD COPY . /www
ONBUILD RUN npm install
ONBUILD CMD nodejs index.js
```

Then build an image from it

* docker image build -t nodetemplate .

Then grab the files from the nodeapp2 directory, and put them
in the same directory as the following Dockerfile

```
FROM nodetemplate
ENV PORT 8080
EXPOSE 8080
```

And build an image from it
* docker image build -t nodeapp2 .

Run in the container, port map it, and try to get a response

* docker container run -p 8080:8080 -d nodeapp2
* curl http://localhost:8080

# Image args
## Practice

In our last example we could change the app from running on port 8080 to
3000, but that would require changing the Dockerfile. Let's use the args

We will reuse are nodetemplate image, but let's create a new nodeapp3 image with
the following dockerfile

```
FROM nodetemplate
ARG PORT=3000
ENV PORT=${PORT}
```

If we build an image like usual it will set to a default of port 3000

* docker image build -t nodeapp3 .
* docker container run -p 3000:3000 -d nodeapp
* curl http://localhost:3000

But if we specify an arg during the build command, we can change which
port the image will be built with

* docker image build --build-arg PORT=3001 -t nodeapp4 .
* docker container run -p 3001:3001 -d nodeapp
* curl http://localhost:3001
