# Port mapping

## Practice
Run the following command to run an instance of Apache httpd - a very popular
web server - with the following command

* docker container run -p 3000:80 -d --name webserver httpd

You can then verify the server is running by visiting [localhost:3000](localhost:3000)
or sending an http request to that address with a tool such as curl (Note: depending on how your docker instance
is setup, the IP address might something other than localhost, such as if you are
running docker on a vm)

Stop and remove the container
* docker container stop webserver
* docker container rm webserver

And then run it on a different port

* docker container run -p 3001:80 -d --name webserver httpd

verify the server is running by visiting [localhost:3001](localhost:3001)
(Note: you could actually have left the first container running, if you give the 
second container a different name. In this case you will have two seperate servers
listening on two different ports. This can be very useful in practice)


## Application
Another popular webserver is nginx. Try running and nginx container in the background,
mapping it to a port, and accessing the server.

There is usually more flexibility in choosing the host port than choosing the container port.
For example both httpd and nginx ran on port 80 inside the container, as that is the traditional
port for a webserver. Shiny on the other hand typically uses port 3838, so it would be reasonable
to expect it to run on that port. Try pulling the quantumobject/docker-shiny image, running it
and accessing it on the browser.

Hint: There is no shame in reading the docs to figure out how to run an image! You can find
the docs for the shiny image [here](quantumobject/docker-shiny)

# Mounting a volume for persistent data

## Practice
 docker run -it -v /data --name container1 busybox

Create a new docker volume with the following command

* docker volume create examplevolume

Then create a new container that uses this volume

* docker container run -it -v examplevolume:/dockervolume --name volumecontainer1 busybox

Enter the location of the volume, create a file. 
* cd /dockervolume
* touch file1.txt

Keep this container running in its own terminal. Then, let's launch a 2nd container that
will also use this volume

* docker container run -it -v examplevolume:/dockervolume --name volumecontainer2 busybox

Verify you can see the data written by the first container
* cd /dockervolume
* ls

You should see file1.txt from the other container

An important thing to note here is that both of these containers are using the same volume at the same time.
Now try creating a file in the 2nd container and verify it exists in the 1st container.

## Application
In addition to containers being able to share volumes, it's important to know that volumes have their own
lifecycle that is seperate from containers. This means we can destroy a container, but the volume will
remain until explicitly killed. This can be a useful technique for managing persistent data.

Try creating a container with a named volume. Writing some data to it, stop the container, and then
destroy the container with the `docker container rm` command. Then start up a new container pointing
to the volume to verify the data still exists

# Bind mounting a local directory
Note: This only works if your docker engine is on the same machine as the docker cli. If you are
using something like docker-machine this won't work as expected

## Practice
It is also useful to be able to mount local directories as volume. For example, we might want
to mount our current working directory into a container. Before we begin create a new directory,
cd into it, and place some files on your host system
* mkdir temp
* cd temp
* touch hello.txt
 
Then, run a new container that mounts this
* docker container run -it -v $(pwd):/dockervolume busybox
* ls

You should see the hello.txt

Create a new file and exit the container
* touch hello2.txt
* exit

And you should be able to see the new hello2.txt file locally
* ls

## Application
With the aid of mounting local directories to docker containers, we can quickly and easily
create an apache webserver with local html files. Create and cd into a new directory. Inside
there create a file called index.html with the contents `<p>Hello world</p>` Then use the 
httpd:2.4 image to serve this file.

Hints
* map port 8080 on your machine to port 80 on the container
* You must mount the directory to /usr/local/apahce2/htdocs/ for this to work


### Answer remove later
 * docker run -dit --name my-apache-app -p 8080:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd:2.4
