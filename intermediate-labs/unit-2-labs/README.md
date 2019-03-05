# Default Container bridge networking
# Practice
On the default bridge network containers can reach other. Verify by this by pinging one
container from another.

First, start two containers in the background
* docker container run -d --name c1 httpd
* docker container run -d --name c2 httpd

Then learn their ip Addresses via the following command
* docker network inspect

It should look something like this:
```
[
    {
        "Name": "bridge",
        "Id": "d3c04a76a5b97991acf7b3640517ae35e217221976a82b38f582c059511b331f",
        "Created": "2019-02-25T23:51:22.949415098Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },bash --login '/Applications/Docker/Docker Quickstart Terminal.app/Contents/Resources/Scripts/start.sh'

        "Internal": false,
        "Attachable": false,
        "Containers": {
            "61adff34656d132edcbe572218458205661f735786d3170f009020214aed1536": {
                "Name": "c2",
                "EndpointID": "0765f10d221fc982efa011a66cef7a7cfbbdc1a4ddd29256f480a9db036cd35d",
                "MacAddress": "02:42:ac:11:00:06",
                "IPv4Address": "172.17.0.6/16",
                "IPv6Address": ""
            },
            "818176f4d0e1a765623cf682785e923aef0e729c367cb67703fb33e450a57419": {
                "Name": "c1",
                "EndpointID": "a321d87738464142a787274cbb89edfebc71397ad9f4e234c36d19d9bb461424",
                "MacAddress": "02:42:ac:11:00:05",
                "IPv4Address": "172.17.0.5/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]
```
In the "Containers" section of "bridge", look for the c1 and c2 entries and note
their IP addresses. For example in mine

c1: 172.17.0.5
c2: 172.17.0.6

Next enter the c1 container with the exec command, and install ping
* docker exec -it c1 bash
* apt-get update
* apt-get install iputils-ping

Then ping the c2 container by IP address. In my case:
* ping -c 3 172.17.0.6

(if the command gets stuck, exit with ctrl c)

Then try repeating the process by pinging c1 from c2

## Application
A real world application for this would be to allow web services to communicate
with each other. Try repeating this process, but this time by running the nodejs
apps we dockerized, and accessing c1 from c2 (and vice versa) via curl. Hint: Don't
forget to include the right port in the curl command!

# User-defined bridge networks

## Practice
In addition to providing better isolation, default networks provide the ability
to communicate with each other via their names, which is far simpler than
using ip addresses

First create a new network
* docker network create mynetwork

Then create two containers and specify mynetwork as the container network
* docker container run -d --name c3 --network mynetwork httpd
* docker container run -d --name c4 --network mynetwork httpd

Attach to c3, install ping, and ping c4 by name

* docker exec -it c3 bash
* apt-get update
* apt-get install iputils-ping
* ping -c 3 c4

## Application
Repeat the porcess with nodeapp containers, curl, and ports

# Host networking
## Practice
Let's use host networking to access an nginx container on port 80 of locahost
without port mapping

* docker run --rm -d --network host --name mynginx nginx
* curl http://localhost:80
## Application
Try to repeat this process with the httpd container
