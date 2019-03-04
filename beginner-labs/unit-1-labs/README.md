# Docker image commands

## Practice:
Try running each of the following commands

* docker image ls
* docker search ubuntu
* docker pull ubuntu
* docker pull httpd
* docker pull nginx
* docker image ls
* docker rmi nginx
* docker image ls

## Application:

* Check to see if there is an httpd docker image
* Download the image to your machine
* Verify that the image is on your system
* Remove the image

# Docker container commands

## Practice:
Try running each of the following commands
* docker container run ubuntu /bin/pwd
* docker container run -it /bin/bash
* ps
* ctrl PQ
* ps
* docker container ls
* docker container run --name example -d httpd
* docker container ls
* docker container ls -a
* docker container stop example
* docker container ls
* docker container rm example

## Application:
* List the contents of the home directory of the ubuntu image in one command
* Run the ubuntu container and launch an interactive shell instance with it in one command
* Exit and stop a container you an in interactive shell with
* Exit a container you have an interactive shell with, but leave it running
* List the processes running inside a container
* Launch an nginx container in the background, verfiy it is running, then remove it.

# Docker exec commands
Sometimes we want to run a one off command - or start an interactive terminal session - with a
running container. If you find yourself doing this to configure a container everytime, then
those operations should probably be in the image. However, running commands on a live container
is very useful for diagnostics and debugging containers.

## Practice
Start an apache container in the background
* docker run -d --name myapache httpd:2.4
Then we can view the contents of the working directory with
* docker container exec myapache ls 
Or start an interactive session with
* docker container exec -it myapache bash
