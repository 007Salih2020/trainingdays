# Challenge 1: Docker 101

## Here is what you'll learn
- Docker Cli
- Creating and running docker containers
- Listing Docker containers and other objects
- Docker object names and ids
- Inspecting container details

In this challenge we start to learn "How to use Docker CLI?" and after that we'll run our first Docker Container. By the end of the challenge, we have learned very basics of Docker containers, how to create-run-stop-delete-inspect-list them.

## Exercises


**1: First let's check the current status of the Docker.** 

Open your terminal and type: 
```shell
$ docker version
```
Output will be something like:
```shell
Client: Docker Engine - Community
 Version:           19.03.8
 API version:       1.40
 Go version:        go1.12.17
 Git commit:        afacb8b7f0
 Built:             Wed Mar 11 01:25:46 2020
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.8
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.12.17
  Git commit:       afacb8b
  Built:            Wed Mar 11 01:29:16 2020
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          v1.2.13
  GitCommit:        7ad184331fa3e55e52b890ea95e65ba581ae3429
 runc:
  Version:          1.0.0-rc10
  GitCommit:        dc9208a3303feef5b3839f4323d9beb36df0a9dd
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683
  ```
  If you've got similar output, this means that your Docker installation is successful and your cli client can communicate with Docker daemon without any problem. This output also shows us Docker's installed cli and daemon versions and their details. In case of you get ``` Error response from daemon: open \\.\pipe\docker_engine_linux: The system cannot find the file specified. ``` this means that your cli client can't connect to the daemon and probably the reason is that Docker daemon didn't started properly. Just try to start the daemon process and retry again.  
***
**2: It's time to get more information about our Docker installation.**

Type:
```shell
$ docker info
```
Output will be something like:

```shell
Client:
 Debug Mode: false

Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 0
 Server Version: 19.03.8
 Storage Driver: overlay2
  Backing Filesystem: <unknown>
  Supports d_type: true
  Native Overlay Diff: true
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 […]
 ```

 This command displays system wide information regarding the Docker installation. Information displayed includes the kernel version, number of containers and images. At the moment, we don't have any running, paused or stopped containers on our system. Also we don't have any container images pulled. This will change in a few minutes. But before running our first Docker container, let's learn "how to use Docker cli?"
***
**3: Let's see "How to become a Docker Cli master?"**

Type:
```shell
$ docker
```
Output will be something like:
```shell
Management Commands:
  app*        Docker Application (Docker Inc., v0.8.0)
  builder     Manage builds
  buildx*     Build with BuildKit (Docker Inc., v0.3.1-tp-docker)
  […]

Commands:
  attach      Attach local standard input, output, and error streams to a running container
  build       Build an image from a Dockerfile
  commit      Create a new image from a container's changes
  […]
```

Docker cli is fairly simple to figure out how to use. You just type ```docker``` and after that  type the command that you want to execute + options that you want to use. For example, simple Docker command to run a container is ```docker run hello-world```. This will create a container by hello-world Docker image. ```docker run --name azuredevcol hello-world``` this will create another container using the same image but this time container will have a name "azuredevcol". You see, it's quite simple. But it got complicated day after day. Docker had roughly 40 top-level solo commands like run, inspect, build, attach etc. While these commands worked very well but they had a few issues and Docker decided to solve these issues by introducing the management commands. 

Docker started to group the commands logically into management commands with Docker Cli version 1.13. Each group represents a single Docker object or ability. For example all container related Docker commands grouped together under container management command. Going back to the previous example, simple Docker command to run a container was ```docker run hello-world```. Now it's ```docker container run hello-world```. Docker cli is backward-compatible so it still supports old way of cli but management command approach is the future. Long story short, Docker cli syntax is fairly simple. Just type ```docker``` and after that type the management command of the object that you want to play, like ```image```, ```container```, ```volume``` and after that type the command that you want to execute, like ```run```, ```stop```, ```pull```, ```inspect``` + options that you want to use. Couple of examples:

```shell 
$ docker container run --name container1 ubuntu ping 10.0.0.1

$ docker image pull mysql:5.7

$ docker volume create my_first_volume

$ docker network inspect bridge
```
But how do we know which commands we can use and which options do we have? Simple. Just by asking for help to Docker Cli. Let's say that we want to create our first Docker container but we don't know which commands and options that we can use. We can ask this to Docker cli simply using ```--help``` option. 

Type:
```shell
$ docker container --help
```
Output will be something like:
```shell
Usage:  docker container COMMAND

Manage containers

Commands:
  attach      Attach local standard input, output, and error streams to a running container
  commit      Create a new image from a container's changes
  cp          Copy files/folders between a container and the local filesystem
  […]
  rm          Remove one or more containers
  run         Run a command in a new container
  start       Start one or more stopped containers
  […]

Run 'docker container COMMAND --help' for more information on a command.
```
```--help``` option listed all the sub-commands under the container management command. Now we know which commands do we have and what are they used for. We can even go further and ask for help for the sub-commands. For example. 

Type:
```shell
$ docker container run --help
```
Output will be something like:
```shell
Usage:  docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]

Run a command in a new container

Options:
      --add-host list                  Add a custom host-to-IP mapping
                                       (host:ip)
  -a, --attach list                    Attach to STDIN, STDOUT or STDERR
      --blkio-weight uint16            Block IO (relative weight),
                                       between 10 and 1000, or 0 to
                                       disable (default 0)
      --blkio-weight-device list       Block IO weight (relative device
                                       weight) (default [])
[…]
```               

We have learned how can we use Docker Cli and how can we get help when we stuck. We're now ready to play with Docker. 

***
**4: It's time to run our first container**

In a few seconds, we'll create our first Docker container. We'll create our container from Docker image tagged hello-world. Let's first check if this image is already available on our system. 

Type:
```shell
$ docker image ls
```
Output will be something like:

```shell
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
```
List is empty. We don't have any Docker image locally. Let's pull first image to the system. 

Type:

```shell
$ docker image pull hello-world
```
Output will be something like:
```shell
Using default tag: latest
latest: Pulling from library/hello-world
0e03bdcc26d7: Pull complete
Digest: sha256:49a1c8800c94df04e9658809b006fd8a686cab8028d33cfba2cc049724254202
Status: Downloaded newer image for hello-world:latest
docker.io/library/hello-world:latest
```
Docker started to do its magic. We requested from Docker that "hey please find the image called ```hello-world``` and pull it into this system from wherever it is located." Docker did that. Docker pulled the image from it's default "Image Registry" which is called Docker Hub. By default, if you don't specify the image registry url that your image is located "i.e. mcr.microsoft.com/mcr/hello-world", Docker assumes that it's stored at Docker Hub "docker.io/library/hello-world". We also wanted to pull image called "hello-world" but as can be seen from the output Docker pulled the image called  "Status: Downloaded newer image for **hello-world:latest**". What's that **:latest**?
An image name is made up of slash-separated name components and tags, optionally prefixed by a registry hostname. Registry hostname represents that in which registry that image is located. Slash-separated name components represent the repository of the image in that image registry. Last part, which is called tag represents the version of the image. It's meta-data you can use to distinguish versions of your Docker images. **:latest** is the default tag used by Docker. If it's not tagged otherwise, images are tagged as latest by default. And if you don't specify the tag while working with docker image, docker always assumes that you're pointing the image tagged as  :latest. 

Let's type ```docker image ls``` one more time. 
```shell
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        6 months ago        13.3kB
```

hello-world:latest image has been pulled into our system. Now we're ready to create and run our first Docker container. 

Type:

```shell
$ docker container run hello-world
```
Output will be something like:
```shell
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

Congratulations. You have created your first Docker container just now. But what has actually happened behind the scene?

You typed the command ```docker container run hello-world``` and instructed to Docker daemon that you wanted to create a new container by the image called hello-world and run a command inside that container. 
- Docker cli took your command and connected to Docker daemon over its rest api and passed that command. 
- Docker daemon checked if the image called ```hello-world:latest``` is stored locally or not. If it couldn't find the image, it would start to pull that from its registry. But we already pulled the image a few min ago so it didn't. 
- Docker created a new container from that image. Assigned a random id to that container object. Also it assigned a random name too because we didn't specify any name for this container. After that the container has been created. But it's just created. Docker didn't start the container. "btw couple of other things have also happened like r/w layer, networks etc. but we'll come to these details later"
- Actually ```docker container run``` command means that we want to run a command inside a new container. If don't specified which command to run, Docker runs the default command instructed on the image. In our case, we didn't specify any command to run inside to container. Therefore Docker started the container that it created and ran the default command instructed on the image which is ```/hello```. "hello" is a console application. When you run that, it shows the "Hello from Docker! This message shows ..." message on the console and exits. 
- Unless otherwise stated, Docker attaches to the container's shell and shows the output of that shell "stdout, stderr"  on your console. That was the case for us too. So we saw the output of "hello" application on our terminal. 
- **Golden Rule:** Each Docker image has a default application-command instructed on the image to run when a container is created from that image. You can overwrite this command and point another command to run when the container is created. Docker starts a container and execute that command and also starts to monitor the process which has triggered by this command. "in our case, it's 'hello' console application". This application is always the PID1.  Docker monitors the PID1. If PID1 continues to run, container runs. When PID1 killed or stopped, container exits. In our case, hello-world is a console application. When we Docker ran it, it created a message and closed. It isn't something like long running service etc. When Docker detected that the PID1 is not running any more, it closed the container too.  

Let's turn back to terminal type:
```shell
$ docker container ls
```
Output will be something like:
```shell
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
````

```docker container ls``` or short ```docker ps``` command list the running container on our system. At the moment, we don't have any running container on our system so list is empty. But if we type with ```-a``` option we can list all both running and stopped containers. Let's do that and type ```docker ps -a```

Output will be something like:
```shell
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
93a266670eb8        hello-world         "/hello"            56 minutes ago      Exited (0) 56 minutes ago                       amazing_wu
```
Now we saw our container on the list. ID and name sections are different on your system than this output. When Docker containers are created, the system automatically assigns a universally unique identifier (UUID) number to each container. Also that is the case for each Docker object "image, container, volume etc.". Each Docker object has a universally unique identifier. Those are 64 character SHA-256 IDs. Docker commands truncate them to 12 characters and that's the reason why we just saw the first 12 characters of the ID. In addition to that, Docker assigns names to the containers. If we don't specify the name with ```--name``` option, Docker generates a random name using an open source list of adjectives and known figures in science and IT world. 
We can use those IDs and names interchangeable when we call the container and we'll see the examples in a few minutes. 
Other sections on that list are Image, Command, Created, Status and Ports. Image is the image that used to create this container. Command is the command that has executed in the container when we started. Created is the timestamp of the creation time. Status is the current status of the container and in our case it's "Exited" which means container is stopped. Ports section show us the exposed ports from that container but it's empty at the moment because we didn't expose any port. 

Let's continue to play with this container. We created the container, it ran the "hello" application, application closed so the container too. Let's start that container again. We will use container id as a reference in this. 

```shell
$ docker container start 93a # just first a few characters of the container id would be enough
```
Output will be something like:
```shell
93a
```
This time it didn't show us the output. Instead of that, Docker returned the container id that we typed. When we used the ```docker container run``` command our console attached to the container's shell and we saw the output. But with ```docker container start``` this doesn't happen. ```docker container start``` starts the stopped container and reruns the default command. In our case, it started the "hello" console application again. "hello" console application recreated the message and send it to the "stdout" stream of the console's shell. We didn't attach to that shell so we didn't see the output. But output is there. We can see any generated out put of the container's console by ```docker logs``` command. Let's do that but this time let's use the container's name instead of the id. 

```shell
$ docker logs amazing_wu #name of the container
```
Output will be something like:
```shell
Hello from Docker!
This message shows that your installation appears to be working correctly.
[…]
Hello from Docker!
This message shows that your installation appears to be working correctly.
[…]
```
We saw 2 messages. By default, ```docker logs``` shows the command’s output just as it would appear if you ran the command interactively in a terminal. UNIX and Linux commands typically open three I/O streams when they run, called STDIN, STDOUT, and STDERR. STDIN is the command’s input stream, which may include input from the keyboard or input from another command. STDOUT is usually a command’s normal output, and STDERR is typically used to output error messages. By default, ```docker logs``` shows the STDOUT and STDERR streams of the container since it was created. Our container wasn't killed. When we first ran the container, it was stopped automatically when it's PID1 stopped. We started the container one more time with ```docker container start``` command again. Process restarted, generated the message and stopped again. Container was stopped too. But it wasn't killed. So we can still see all the STDOUT and STDERR messages since the creation of the container. But now it's time to kill "or delete" the container. 

Type:
```shell
$ docker container rm 93a # or container name
```
Output will be something like:
```shell
93a
```
If you type ```docker ps -a``` now, output should be empty. 

**5: Detach container**

We're gonna create another container from the same hello-world image. But this time we'll create it detached. By default Docker container starts in the foreground mode. Like the one that we created above. In the foreground mode, Docker starts the default process in the container and attaches your console to the process’s STDIN, STDOUT and STDERR streams. But when we do that,  you can not access your console anymore. You just see the output generated by container on your screen. To avoid this we can start a container in the background mode which is also called detached and the option that gives you this ability is ```-d```. Running a container in the foreground or background doesn't change it's behavior. This is just about if we want to attach to the containers streams or not. Let's create the container in the background and also this time let's define its name too. 

Type:
```shell
$ docker container run -d --name azuredevcol hello-world
```
Output will be something like:
```shell
12fe24372acd0fed5f2063d35faaae13f2cb2e5f7d60f94ff86bec55ba272b1a
```

This time, instead of getting the message generated by hello app, Docker returned the container id back. Literally same thing has happened. A new container has been created using hello-world image, container has been started, default application inside the container started, it did its thing, app stopped so container stopped too. If you type ```docker logs azuredevcol``` , you can see the logs generated by the application running inside the container. Now let's create another container but we'll use another image ```httpd``` which is the official Apache HTTP Server Docker image. 


Type:
```shell
$ docker container run -d --name webserver httpd
```
Output will be something like:
```shell
Unable to find image 'httpd:latest' locally
latest: Pulling from library/httpd
6ec8c9369e08: Pull complete
819d6e0b29e7: Pull complete
6a237d0d4aa4: Pull complete
cd9a987eec32: Pull complete
fdec8f3f8485: Pull complete
Digest: sha256:2a9ae199b5efc3e818cdb41c790638fc043ffe1aba6bc61ada28ab6356d044c6
Status: Downloaded newer image for httpd:latest
83d3a7cb8bd571d9f35688d80a4c676b3fe88f297d2170c5ea78d1c87dcd31aa
```

We requested from Docker that, create a new container by using httpd image, start it detached and gave it the name "webserver". Docker took our command, checked the local image store and couldn't find the image called httpd. It started to pull the image from Docker Hub. As you can see from the output, it completed this pulling process in 5 steps. Docker pulled 5 different layers. We'll come to this layer thing later but for now just notice that this operation was handled as multiple steps. At the end, Docker combined these layers and save them to the local store and returned us back the id of the image. After that Docker create the container and ran it and returned us back the container id. And also it gave us our console back because we started the container in the detached mode.


Type:
```shell
$ docker ps
```
Output will be something like:
```shell
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS               NAMES
83d3a7cb8bd5        httpd               "httpd-foreground"   12 seconds ago      Up 12 seconds       80/tcp              webserver
```

As you can see from the output, status of the container is "Up" and the main process that is running inside the container is "httpd-foreground". httpd-foreground is Apache's web server daemon. It's not like the one shot hello-app. It's a service, a daemon. When you start it, it continues to run until it crashes or explicitly stopped or killed. It's running at the moment so our container too. Remember the Golden Rule. If the first process inside the container runs, container runs too. If it stops, container stops too. Let's try to delete this container. 

Type:
```shell
$ docker container rm webserver
```
Output will be something like:
```shell
Error response from daemon: You cannot remove a running container 83d3a7cb8bd571d9f35688d80a4c676b3fe88f297d2170c5ea78d1c87dcd31aa. Stop the container before attempting removal or force remove
```
As message says, you can't delete a running container. Either you have to stop the container first or you can force the deletion with ```-f``` option. Type ```docker container rm -f webserver``` to delete the container. 

**6: Running another application inside the container**

As mentioned before "Each Docker image has a default application-command instructed on the image to run when a container is created from that image. You can overwrite this command and point another command to run when the container is created". Let's try that one. Again we'll create a container from httpd image but this time we'll create it to run another application instead of the default "httpd-foreground".  

Type:
```shell
$ docker container run httpd date
```
Output will be something like:
```shell
Tue May 28 20:46:22 UTC 2020
```
We requested from Docker to create a new container from ```httpd``` image and run ```date``` command instead of the default "httpd-foreground". 

Type:
```shell
$ docker ps -a
```
Output will be something like:
```shell
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
42227d141918        httpd               "date"              4 seconds ago       Exited (0) 2 seconds ago                       sad_yonath
```

As you can see, Docker did that and created a new container and ran the date application in it. You can also execute-run other commands-applications inside the running containers too. ```docker container exec``` command does that. Let's try that. First we're gonna create a running container. 

Type:
```shell
$ docker container run -d --name exec_test httpd
```
Output will be something like:
```shell
d0f1c26b80706f47ba4d676e285c771d7ed5c077c961be36dc73091893a784a9
```
Check and confirm that the container is running. 

Type:
```shell
$ docker ps
```
Output will be something like:
```shell
CONTAINER ID        IMAGE               COMMAND              CREATED              STATUS              PORTS               NAMES
d0f1c26b8070        httpd               "httpd-foreground"   About a minute ago   Up About a minute   80/tcp              exec_test
```

Now we have a running container. We can execute new commands inside that container. 


Type:
```shell
$ docker container exec exec_test date
```
Output will be something like:
```shell
Tue May 28 21:16:03 UTC 2020
```

You can run any application-command if the application exists inside the container. Let's delete the container before the next challenge.

Type:
```shell
$ docker container rm -f exec_test
```
Output will be something like:
```shell
exec_test
```


**7: Connect to a Docker container**

As the last exercise of this challenge, we're gonna connect to a running container. Connecting to a running Docker container is helpful when you want to see what is happening inside the container. First let's create a running container. 

Type:
```shell
$ docker container run -d --name azuredevcol httpd
```
Output will be something like:
```shell
3e9574a7ed458ff1d2343404a1c24f3361f18f1dabf52a75da0d0b4030723c82
```
Check and confirm that the container is running. 

Type:
```shell
$ docker ps
```
Output will be something like:
```shell
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS               NAMES
3e9574a7ed45        httpd               "httpd-foreground"   7 seconds ago       Up 7 seconds        80/tcp              azuredevcol
```

Now we have a running container named azuredevcol. We want to connect to this container's shell. Don't confuse. We won't attach to this container's running process' STDOUT and STDERR streams. We want to run a shell inside the container and attach to that shell. To be able to that, we're gonna use ```docker container exec``` command. But this time we need 2 options, which are ```--interactive --tty``` . But we combine and use them as ```-it```. Interactive means that you want to keep the input channel open on the container and tty means that you're creating pseudo terminal. Long story short ```-it``` tells Docker to allocate pseudo-TTY connected to the container’s stdin, creating an interactive shell in the container. 

Type:
```shell
$ docker exec -it azuredevcol sh
```
Output will be something like:
```shell
#
```

Our shell prompt changed to ```#``` and this means that we're connected to container. We executed the ```sh``` command inside the running container with ```-it``` options. This allowed us to connect a running sh instance inside the container. Let's type couple of commands to proof that we're in the container.

```shell
# echo $0
sh
# ls
bin  build  cgi-bin  conf  error  htdocs  icons  include  logs  modules
# date
Tue May 28 21:44:53 UTC 2020
# hostname
3e9574a7ed45
# exit
$
```

You can delete the container by typing ```docker container rm -f azuredevcol```. 
Also ```docker container prune``` command deletes all stopped container as bulk but use it with awareness. 

**8: Inspecting a container's details**

```docker ps``` or ```docker container ls``` commands show us the list of running containers on the system and when you add ```-a``` option you can list both running and stopped containers. But the output of these commands give you very brief details about the containers. When you want to get all the information related to a specific container, you should use ```docker container inspect``` command. Let's try this. First let's create a container. 

Type:
```shell
$ docker container run -d --name inspect_test httpd
```
Output will be something like:
```shell
d4463417f9946f9d2cabfdca5ac81f45b7d2a2f4dc8299b9d36922b8d4b23111
```

with inspect command we can see all the details of this or any container. 

Type:
```shell
$ docker container inspect inspect_test
```
Output will be something like:
```shell
[
    {
        "Id": "d4463417f9946f9d2cabfdca5ac81f45b7d2a2f4dc8299b9d36922b8d4b23111",
        "Created": "2020-07-31T08:38:26.3847773Z",
        "Path": "httpd-foreground",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 1085,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-07-31T08:38:27.3829639Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        […]
        […]
        […]
        "Image": "sha256:9d2a0c6e5b5714303c7b72793311d155b1652d270a785c25b88197069ba78734",

                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

You can delete the container by typing ```docker container rm -f inspect_test```. 
Also ```docker container prune``` command deletes all stopped container as bulk but use it with awareness.

## Wrap up

__Congratulations__ you have completed the Docker 101 challenge. You've just learned the very basics of Docker containers.

*** Reference: https://docs.docker.com