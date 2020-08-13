# Challenge 4: Image and Registry

## Here is what you'll learn

- Tagging
- Image pull and push
- Building your own image
- Image registeries and repositories

In this challange, we're gonna play with Docker Images. A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings. 

## Exercises


**1: Tagging**

Let's pull an image. The command that we use is ```docker pull```. With that command, we're requesting from Docker that we want to download this image from its registry to our machine. 

Type: 
```shell
$ docker pull ubuntu:latest
```
Output will be something like:
```shell
latest: Pulling from library/ubuntu
3ff22d22a855: Pull complete
e7cb79d19722: Pull complete
323d0d660b6a: Pull complete
b7f616834fd0: Pull complete
Digest: sha256:5d1d5407f353843ecf8b16524bc5565aa332e9e6a1297c73a92d3e754b8a636d
Status: Downloaded newer image for ubuntu:latest
docker.io/library/ubuntu:latest
 ```
Now it's time to see which images that we have on our system. For this, we're gonna type:
 
```shell
$ docker image ls
```
Output will be something like:
```shell
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              1e4467b07108        2 weeks ago         73.9MB
 ```

I want you to pay attention to the image id part. Like containers, images also have unique ids. The image ID is a digest, and is a computed SHA256 hash of the image configuration object, which contains the digests of the layers that contribute to the image's filesystem definition. If two different images have the same ids, this means that they're literally the same images with different names. 
Yes it's possible to tag an image with different tags. Let's do that and add another name to this image. 

Type: 
```shell
$ docker image tag ubuntu:latest your_dockerhub_id/day6:ubuntu
```
Now we have added a second name to the same image. Let's check and see.

Type: 
```shell
$ docker image ls
```
Output will be something like:
```shell
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
ubuntu                latest              1e4467b07108        2 weeks ago         73.9MB
ozgurozturknet/day6   ubuntu              1e4467b07108        2 weeks ago         73.9MB
```

We have 2 images stored on our computers. But image ids are same, so these are literally the same image but with different names. 

We have tagged the image with our Docker Hub id and actually this means that this image is stored on Docker Hub (Remember, image names are also indicates where the image is located). But it isn't at the moment. Let's correct this and push this image to its repository. But before that we have to login and authenticate. In this way, Docker Hub knows that we're the right person who can push image to this registry. Let's login first. 

Type: 
```shell
$ docker login
```
Output will be something like:
```shell
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: ozgurozturknet
Password:
Login Succeeded
```

Now we're ready to push. 

Type: 
```shell
$ docker push your_dockerhub_id/day6:ubuntu
```
Output will be something like:
```shell
The push refers to repository [docker.io/ozgurozturknet/day6]
095624243293: Mounted from library/ubuntu
a37e74863e72: Mounted from library/ubuntu
8eeb4a14bcb4: Mounted from library/ubuntu
ce3011290956: Mounted from library/ubuntu
ubuntu: digest: sha256:60f560e52264ed1cb7829a0d59b1ee7740d7580e0eb293aca2d722136edb1e24 size: 11529MB
```
It is fast. Isn't it? Please pay attention to the output. ```Mounted from library/ubuntu```. You know that images consist of multiple layers. And each layer has it's unique id. When we pull or push any image, if the target (registry or your computer) has the layer with that id already stored on it, it doesn't pull or push that layer again. Just checks the file which is already stored on it and mounts that. This is the reason why it was fast. We didn't transfer any file to Docker Hub. Docker Hub detected that these 4 layers are already stored on it, so instead of getting it again, Docker hub just mounted these files to our repository. This is also same on our computer. We have 2 images stored on it. But they are literally the same images with different names. Docker doesn't store multiple files for these 2 images. The image files are stored just once but multiple tags added to the same files.

But what are these layers? Is there any way to see how are they created? Yes and the command that we'll use is ```docker image history```. History subcommand shows us the history of the image and it shows how all layers are created. Let's check this. 

Type: 
```shell
$ docker image history ubuntu:latest
```
Output will be something like:
```shell
1e4467b07108        2 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>           2 weeks ago         /bin/sh -c mkdir -p /run/systemd && echo 'do…   7B
<missing>           2 weeks ago         /bin/sh -c set -xe   && echo '#!/bin/sh' > /…   811B
<missing>           2 weeks ago         /bin/sh -c [ -z "$(apt-get indextargets)" ]     1.01MB
<missing>           2 weeks ago         /bin/sh -c #(nop) ADD file:65a1cc50a9867c153…   72.9MB
```

Output shows us how these layers have been created. Which commands have been executed and these layers have been created as a result of these commands. This also shows us how an image is created. 

Docker can build images automatically by reading the instructions from a Dockerfile. A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image. Using docker build users can create an automated build that executes several command-line instructions in succession. Each instruction that changes anything creates a new layer. The Docker daemon runs the instructions in the Dockerfile one-by-one, committing the result of each instruction to a new image if necessary, before finally outputting the ID of your new image. It's now time to create our first image. 

***
**2: Building the first image**

First, we're gonna clone this Github repository.

Type: 
```shell
$ git clone https://github.com/azuredevcollege/trainingdays.git
```
Output will be something like:
```shell
Cloning into 'trainingdays'...
remote: Enumerating objects: 42, done.
remote: Counting objects: 100% (42/42), done.
remote: Compressing objects: 100% (31/31), done.
remote: Total 1899 (delta 26), reused 25 (delta 11), pack-reused 1857 eceiving objects:  97% (1843/1899), 57.20 MiB | 11.92 MiB/s
Receiving objects: 100% (1899/1899), 63.35 MiB | 11.26 MiB/s, done.
Resolving deltas: 100% (747/747), done.
Updating files: 100% (1396/1396), done.
```

Repo has been cloned. It's time to enter the ```/trainingdays/day6/apps/first_docker_image```. cd to that folder and list all the files. 


Type: 
```shell
$ cd /trainingdays/day6/apps/first_docker_image
$ ls -l
```
Output will be something like:
```shell
total 8
-rw-r--r-- 1 ozozturk ozozturk  90 Jun 12 11:56 Dockerfile
-rw-r--r-- 1 ozozturk ozozturk 211 Jun 12 11:21 index.html
```

There are 2 files in the folder. ```index.html``` is a simple html file that has been created by us. We want to build an image that includes and serves that image. To build an image, we use ```Dockerfile```. A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image. We have one in this folder. Let's check what's in it. 

Type: 
```shell
$ cat Dockerfile
```
Output will be something like:
```shell
FROM nginx:latest
COPY index.html /usr/share/nginx/html
CMD ["nginx", "-g", "daemon off;"]
```

This is one of the simplest Dockerfile. When we type ```docker image build .``` in the folder that any Dockerfile presents, Docker starts to run instructions in that Dockerfile in order. A Dockerfile must begin with a FROM instruction. The FROM instruction specifies the Parent Image from which you are building. ```FROM``` basically means that "hey Docker, download that image and execute the next instructions on top of that image". It's the base image that we build our image on top of. In our case, it is ```nginx:latest```. We're building our image on top of that image. 

The second instruction in that Dockerfile is ```COPY```. The COPY instruction copies new files or directories from "source" and adds them to the filesystem of the container at the path "destination". In our case, we're instructing Docker to copy the ```index.html``` file on the current folder to the ```/usr/share/nginx/html``` folder inside the image. ```/usr/share/nginx/html``` is the folder where Nginx stores the websites that it serves. Thus when Nginx daemon runs, it serves our website. 

The third and last instruction is CMD. The main purpose of a CMD is to provide defaults for an executing container. In short, it defines the command to execute when you run a container from that image. There can only be one CMD instruction in a Dockerfile. If you list more than one CMD then only the last CMD will take effect. In our case, we want Docker to start nginx daemon when we create a container. 

Ok, it's time to create our first image. We checked the Dockerfile and learned what it includes. Now we can build the first image.

Type: 
```shell
$ docker image build -t your_dockerhub_id/firstimage:latest .
```
Output will be something like:
```shell
Sending build context to Docker daemon  3.072kB
Step 1/3 : FROM nginx:latest
 ---> 08393e824c32
Step 2/3 : COPY index.html /usr/share/nginx/html
 ---> 14fb48dc6eea
Step 3/3 : CMD ["nginx", "-g", "daemon off;"]
 ---> Running in 772fbfd4dba3
Removing intermediate container 772fbfd4dba3
 ---> 560570bf44e5
Successfully built 560570bf44e5
Successfully tagged ozgurozturknet/firstimage:latest
```
Congrats. We have built our first image. Let's try if it's working as expect. Let's create a container and see. 


Type: 
```shell
$ docker container run --rm -d -p 80:80 --name test_container your_dockerhub_id/firstimage:latest
```
Output will be something like:
```shell
25e7fe3f3e57dd2eab07bf672501dde69d81eb156347607e0b378757b41d859b
```
Open a browser and visit http://127.0.0.1 You would see a page like that. 

<img src="./img/firstimage.png">

Stop the container and it'll be deleted.

Type: 
```shell
$ docker container stop test_container
```
Output will be something like:
```shell
test_container
```


***
**3: Building a nodejs image**

This time we're gonna build a nodejs app image. Enter the ```/trainingdays/day6/apps/nodejs``` folder and list all the files. 


Type: 
```shell
$ cd /trainingdays/day6/apps/nodejs
$ ls -l
```
Output will be something like:
```shell
total 12
-rw-r--r-- 1 ozozturk ozozturk 292 Aug 12 20:27 Dockerfile
-rw-r--r-- 1 ozozturk ozozturk 288 Aug 12 20:14 package.json
-rw-r--r-- 1 ozozturk ozozturk 273 Aug 12 20:15 server.js
```

This time we have 3 files. First one is ```package.json```. If you work with JavaScript, or you've ever interacted with a JavaScript project, Node.js or a frontend project, you surely met the package.json file. The package.json file is kind of a manifest for your project. It can do a lot of things, but in our case it's specially important because it defines the depencies that we'll install with npm. Our simple node application is running on top of Express framework and we need that framework to be installed to run our simple Javscript webapp. ```Server.js``` is the second file and it's our main Javascript application. It's a simple Hello World web app. And the third one is the usual suspect. Dockerfile. Let's take a look at it. 

Type: 
```shell
$ cat Dockerfile
```
Output will be something like:
```shell
# source: https://nodejs.org/en/docs/guides/nodejs-docker-webapp/
FROM node:12

# Create app directory
WORKDIR /usr/src/app

# Copy source files
COPY package.json .
COPY server.js .

# Install app dependencies
RUN npm install

# Exposing the port 8080
EXPOSE 8080

CMD [ "node", "server.js" ]
```

I want you to pay attention to 2 things. First, as you can see, we can add comments to Dockerfile. Any line starting with ```#``` is treated as a comment and not processed. Second, we have 3 new instructions, ```WORKDIR``` , ```RUN``` and ```EXPOSE```. The default working directory for running binaries within a container is the root directory (/), but the you can set a different default with the Dockerfile WORKDIR command. It's kind of cd'ing to that folder. And any command that you execute after that will be executed in that folder. If the folder is not in the image, Docker creates tht folder. 

The RUN instruction will execute any commands in a new layer on top of the current image and commit the results. The resulting committed image will be used for the next step in the Dockerfile. When we want to execute anything, we use this instruction. 

The EXPOSE instruction informs Docker that the container listens on the specified network ports at runtime. You can specify whether the port listens on TCP or UDP, and the default is TCP if the protocol is not specified. The EXPOSE instruction does not actually publish the port. It functions as a type of documentation between the person who builds the image and the person who runs the container, about which ports are intended to be published. To actually publish the port when running the container, we use the -p flag on docker run.

Now it's time to build the image. 

Type: 
```shell
$ docker image build -t your_dockerhub_id/node:latest .
```
Output will be something like:
```shell
Sending build context to Docker daemon  4.096kB
Step 1/7 : FROM node:12
 ---> cfcf3e70099d
Step 2/7 : WORKDIR /usr/src/app
 ---> Running in 0784285bb528
Removing intermediate container 0784285bb528
 ---> 548e712ed3ef
Step 3/7 : COPY package.json .
 ---> 3f85bf98e435
Step 4/7 : COPY server.js .
 ---> beb25f72bb6a
Step 5/7 : RUN npm install
 ---> Running in e3eed8b0c46d
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN docker_web_app@1.0.0 No repository field.
npm WARN docker_web_app@1.0.0 No license field.

added 50 packages from 37 contributors and audited 50 packages in 2.167s
found 0 vulnerabilities

Removing intermediate container e3eed8b0c46d
 ---> 8d3082c10a9e
Step 6/7 : EXPOSE 8080
 ---> Running in 0e6bd014ac73
Removing intermediate container 0e6bd014ac73
 ---> 5143985531f3
Step 7/7 : CMD [ "node", "server.js" ]
 ---> Running in 152317b9eafe
Removing intermediate container 152317b9eafe
 ---> a832145edf14
Successfully built a832145edf14
Successfully tagged ozgurozturknet/node:latest
```

Image has been built successfully. But I want you to run the same command one more time. 

```shell
Sending build context to Docker daemon  4.096kB
Step 1/7 : FROM node:12
 ---> cfcf3e70099d
Step 2/7 : WORKDIR /usr/src/app
 ---> Using cache
 ---> 3d04799d4f2a
Step 3/7 : COPY package.json .
 ---> Using cache
 ---> a348cb47ad20
Step 4/7 : COPY server.js .
 ---> Using cache
 ---> 5affbe5fce86
Step 5/7 : RUN npm install
 ---> Using cache
 ---> ad11b36a3604
Step 6/7 : EXPOSE 8080
 ---> Using cache
 ---> 57b485f4eef1
Step 7/7 : CMD [ "node", "server.js" ]
 ---> Using cache
 ---> fb0586534394
Successfully built fb0586534394
Successfully tagged ozgurozturknet/node:latest
```

Something strange has happened. We built the image, after that we didn't change any source file and run the docker image build command second time. We recevied lots of ``` ---> Using cache``` outputs this time. What does that mean? 

When building an image, Docker steps through the instructions in your Dockerfile, executing each in the order specified. As each instruction is examined, Docker looks for an existing image in its cache that it can reuse, rather than creating a new (duplicate) image. If you don't change any source file or didn't change anything in the Dockerfile, this means that nothing has changed, so Docker doesn't run the insturction again and again and uses the cached instruction. That makes the build process fast. Let's simulate that and see what happens if we change something. Open the ```server.js``` file with a text editor, go to line 12 and change the ```Hello World``` with another message something like ```build cache test```. Save the file and rerun the ```docker image build -t your_dockerhub_id/node:latest .``` command one more time. 

```shell
Sending build context to Docker daemon  4.096kB
Step 1/7 : FROM node:12
 ---> cfcf3e70099d
Step 2/7 : WORKDIR /usr/src/app
 ---> Using cache
 ---> 3d04799d4f2a
Step 3/7 : COPY package.json .
 ---> Using cache
 ---> a348cb47ad20
Step 4/7 : COPY server.js .
 ---> 800aa5cd76d2
Step 5/7 : RUN npm install
 ---> Running in 20f6c9a2c4e0
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN docker_web_app@1.0.0 No repository field.
npm WARN docker_web_app@1.0.0 No license field.

added 50 packages from 37 contributors and audited 50 packages in 2.521s
found 0 vulnerabilities

Removing intermediate container 20f6c9a2c4e0
 ---> eac206f9957e
Step 6/7 : EXPOSE 8080
 ---> Running in 3084d34a448f
Removing intermediate container 3084d34a448f
 ---> 1e4c0a700aa6
Step 7/7 : CMD [ "node", "server.js" ]
 ---> Running in b0fae2027731
Removing intermediate container b0fae2027731
 ---> 729b13c3276f
Successfully built 729b13c3276f
Successfully tagged ozgurozturknet/node:latest
```

Docker started to build the image again. First step, nothing changed, used the cache. Second step, nothing changed, used the cache. Third step, nothing changed, used the cache. But fourth step, we wanted to copy server.js file that has been changed. Old layer that docker has cached before is invalid now. So docker started to execute that instruction and created a new layer and didn't use the cached version. And each instruction after that has been executed again and docker didn't use the cache. Because something has changed and the rest of the layers have changed too. So docker can't use the cache for them too. That's kind of important thing to know. Because an image is built during the final stage of the build process, you can minimize image layers by leveraging build cache.

For example, if your build contains several layers, like this, you can order them from the less frequently changed (to ensure the build cache is reusable) to the more frequently changed. If we move  ```COPY server.js .``` from 4. step to anywhere after the ```RUN npm install``` instruction, this means that, we can change anything in this file and Docker will not run npm install each time when we build the image. 
(Visit https://docs.docker.com/develop/develop-images/dockerfile_best-practices/  for Dockerfile best practices)

It's time to create a container from that image and see if it's working properly. 

Type: 
```shell
$ docker container run --rm -d -p 80:8080 --name node_container your_dockerhub_id/node:latest
```
Output will be something like:
```shell
09a4d1789cdab19a0f3af9c66a4f6e68503e5b8a1f46d6c512e88b10c5e70011
```

Open a browser and visit http://127.0.0.1 You would see a page with a Hello World! message. 

Stop the container and it'll be deleted.

Type: 
```shell
$ docker container stop node_container
```
Output will be something like:
```shell
node_container
```

***
**4: Multi-stage build**

Let's imagine that we're java developers and working on a new shiny project called App1 (Do you remember our old friend :)). We wrote the code and now it is a good time to check our source code. It's located at ```/trainingdays/day6/apps/java``` folder. cd to that folder and list all the files. 

Type: 
```shell
$ cd /trainingdays/day6/apps/java
$ ls -l
```
Output will be something like:
```shell
total 8
-rw-r--r-- 1 ozozturk ozozturk 135 Jun 12 22:43 Dockerfile
-rw-r--r-- 1 ozozturk ozozturk 154 Jun 12 22:35 app1.java
-rw-r--r-- 1 ozozturk ozozturk 154 Jun 12 22:35 Dockerfile2
```

There are 2 files in it (actually 3 but let's forget the Dockerfile2 for now). ```app1.java``` is the source code of our application. Please pay attention. It is not the application, it is just the source code. It isn't compiled yet. To convert this source code to an application, we have to compile this code. We generally do that on our computers via IDEs. But we can use the power of Docker and compile our application while building the image too. The Dockerfile in this folder is a good example of that practice. Let's check the Dockerfile. 

Type: 
```shell
$ cat Dockerfile
```
Output will be something like:
```shell
FROM mcr.microsoft.com/java/jdk:8-zulu-alpine
COPY . /usr/src/myapp/
WORKDIR /usr/src/myapp
RUN javac app1.java
CMD [ "java" , "app1" ]
```

Again, a simple Dockerfile. We build our image on top of the Java Development Kit image provided by Microsoft. JDK image includes the tools that we use to compile our java code and convert it to a java application. First we copy that source code into image and after that we jump into that folder and run the ```javac app1.java``` command which compiles our application. At the end, we have the CMD instruction that  runs the application whenever we create a container from that image. Let's build the image. 


Type: 
```shell
$ docker image build -t your_dockerhub_id/java:latest .
```
Output will be something like:
```shell
Sending build context to Docker daemon  3.072kB
Step 1/5 : FROM mcr.microsoft.com/java/jdk:8-zulu-alpine
8-zulu-alpine: Pulling from java/jdk
df20fa9351a1: Pull complete
1e7717fd7ab1: Pull complete
Digest: sha256:19712872c6dd8ca02a8f727737372477559f2659aa6294c2dcae050096234224
Status: Downloaded newer image for mcr.microsoft.com/java/jdk:8-zulu-alpine
 ---> b7bb6dd0ee76
Step 2/5 : COPY . /usr/src/myapp/
 ---> ed2f0977e390
Step 3/5 : WORKDIR /usr/src/myapp
 ---> Running in d2333b9e9af3
Removing intermediate container d2333b9e9af3
 ---> 55a151ddf81a
Step 4/5 : RUN javac app1.java
 ---> Running in b8697de33caa
Removing intermediate container b8697de33caa
 ---> 3bfd240a44ff
Step 5/5 : CMD [ "java" , "app1" ]
 ---> Running in 00a6a2a448ab
Removing intermediate container 00a6a2a448ab
 ---> ceab35948e29
Successfully built ceab35948e29
Successfully tagged ozgurozturknet/java:latest
```
Image has been built. Let's test it. 

Type: 
```shell
$ docker container run your_dockerhub_id/java:latest
```
Output will be something like:
```shell
Hello there! I'm App1 Java Console Application
```

Perfect. It works. App1 has been compiled and it runs.  But there seems to be something wrong with that approach. First of all, we built our image on top of the JDK image. It includes lots of tools for development. Like the one that we used to compile our application. But do we really need to send this image to our customers? With all of these development tools? Also our source code is copied to that image too. Maybe that is not something we want. We just wanted to compile our source code and get the application. We want that our customers be able to run this application. Not all the unnessary tools and source code. Also image size is big. Because of this unnessary tools that we don't need to run the application. These tools are needed for development. Not needed to run the application. JRE, java runtime is the thing that we need to run the application. It's just the runtime and much smaller than the jdk.

Instead of sending this image, It would be wise to get this compiled application from that image, copy it to our computer and create another image that includes just this application + runtime, instead of application + source code + development tools. So we need to build another image. To be able to do that, we need to create a second Dockerfile. But eeeh. This is a mess. There should be a simple solution. 
Yes there is a simple solution to handle this and it's called multi-stage build. 

One of the most challenging things about building images is keeping the image size down. Each instruction in the Dockerfile adds a layer to the image, and you need to remember to clean up any artifacts you don’t need before moving on to the next layer. To write a really efficient Dockerfile, you have traditionally needed to employ shell tricks and other logic to keep the layers as small as possible and to ensure that each layer has the artifacts it needs from the previous layer and nothing else. It was actually very common to have one Dockerfile to use for development (which contained everything needed to build your application), and a slimmed-down one to use for production, which only contained your application and exactly what was needed to run it. This has been referred to as the “builder pattern”. But maintaining two Dockerfiles is not ideal. 

With multi-stage builds, you use multiple FROM statements in your Dockerfile. Each FROM instruction can use a different base, and each of them begins a new stage of the build. You can selectively copy artifacts from one stage to another, leaving behind everything you don’t want in the final image. Dockerfile2 is a perfect example of this kind of multi-stage build. Let's check it. 


Type: 
```shell
$ cat Dockerfile2
```
Output will be something like:
```shell
FROM mcr.microsoft.com/java/jdk:8-zulu-alpine AS builder
COPY . /usr/src/myapp/
WORKDIR /usr/src/myapp
RUN javac app1.java

FROM  mcr.microsoft.com/java/jre:8-zulu-alpine
WORKDIR /usr/src/myapp
COPY --from=builder /usr/src/myapp .
CMD ["java", "app1"]
```

As you can see, now we have a Dockerfile with 2 FROM instructions. First part is the same as the original Dockerfile that we have built our application a few minutes ago. There are only 2 differences. First, there is a new section at the end of the FROM instruction. "AS builder" or it could be AS anything, just name it. It indicates that this first section is tagged as builder. We're gonna use this tag in the second section. Second difference is that CMD instruction has been removed, because we don't need it anymore. This "builder" stage is just for compiling the application from its source code. We take our source code, copy it into this jdk image, compile the application and that's it. After that we start to build our actual image with a new FROM instruction. This is the final image that will be created at the end. It's based on JRE image, not the JDK. The COPY --from=builder line copies just the built artifact from the previous stage into this new stage. The JDK and any intermediate artifacts are left behind, and not saved in the final image. The end result is the tiny production image that just includes the application. Not the source code and not the development tools. Let's build this image and see what's going on. 
(We're gonna use ```-f``` option to point this Dockerfile2. We use the -f flag with docker build to point a Dockerfile named other than Dockerfile or not located in the folder that we run the command)

Type: 
```shell
$ docker image build -f Dockerfile2 -t your_dockerhub_id/finaljava:latest .
```
Output will be something like:
```shell
Sending build context to Docker daemon  4.096kB
Step 1/8 : FROM mcr.microsoft.com/java/jdk:8-zulu-alpine AS builder
 ---> b7bb6dd0ee76
Step 2/8 : COPY . /usr/src/myapp/
 ---> 786a33a37acb
Step 3/8 : WORKDIR /usr/src/myapp
 ---> Running in 1145e3882420
Removing intermediate container 1145e3882420
 ---> 5f16de8216fb
Step 4/8 : RUN javac app1.java
 ---> Running in 98375300d10a
Removing intermediate container 98375300d10a
 ---> f632a0c6f49e
Step 5/8 : FROM  mcr.microsoft.com/java/jre:8-zulu-alpine
8-zulu-alpine: Pulling from java/jre
df20fa9351a1: Already exists
b391ad10af71: Pull complete
Digest: sha256:bb7135444a7e78448b0038d26079e6bef78c1c7839333bf9806d6c12e65a1eff
Status: Downloaded newer image for mcr.microsoft.com/java/jre:8-zulu-alpine
 ---> 36c60fc08a2d
Step 6/8 : WORKDIR /usr/src/myapp
 ---> Running in 25809e0fd983
Removing intermediate container 25809e0fd983
 ---> 5b580490d819
Step 7/8 : COPY --from=builder /usr/src/myapp .
 ---> e357def56d05
Step 8/8 : CMD ["java", "app1"]
 ---> Running in 00585394dbfb
Removing intermediate container 00585394dbfb
 ---> 7b7c6b3a7f6a
Successfully built 7b7c6b3a7f6a
Successfully tagged ozgurozturknet/finaljava:latest
```
Final image has been built. It's much smaller than the first one and also just includes the artifacts that we need. 


***
**5: Php contacts app**

This time we're gonna combine what we have learn so far. We will build 2 images. First one is a simple php application. The other one is mysql database. After that we will run these and try the tricks that we have learned so far. First let's check the files and see what we're gonna build. All are located at ```/trainingdays/day6/apps/php``` folder. cd to that folder and list all the files. 

Type: 
```shell
$ cd /trainingdays/day6/apps/php
$ ls -l
```
Output will be something like:
```shell
total 24
-rw-r--r-- 1 ozozturk ozozturk  333 Aug 13 11:54 Dockerfile
-rw-r--r-- 1 ozozturk ozozturk   64 Aug 13 11:36 Dockerfile.mysql
-rw-r--r-- 1 ozozturk ozozturk  112 Aug 13 11:36 createtable.sql
-rw-r--r-- 1 ozozturk ozozturk   81 Aug 13 11:36 env.list
-rw-r--r-- 1 ozozturk ozozturk  107 Aug 13 11:36 envmysql.list
drwxr-xr-x 2 ozozturk ozozturk 4096 Aug 13 11:35 php
```

It's a little bit crowded folder. There are 2 Dockerfiles. First one is the Dockerfile that we'll build the web app image. Second one is the mysql database image. 

There are also 2 enviroment variable files. With env.list, we'll pass connection strings to the web app, so It will know how to connect the database "username, password, databasename etc.". envmysql.list is the enviroment variable file that we'll pass to the mysql container. It'll start and create the database with these parameters. Essentially, we could inject these variables inside the Dockerfiles. Yes it's possible. We can define enviroment variables with ```ENV``` instruction but if we do that, these will hardcoded inside the image. This means that whoever get this image can access these values. Specially this isn't a thing that we want for sensitive data like passwords. Therefore we didn't put these into images. Instead of that, we will pass these while creating the containers. 

createtable.sql is an sql script that will create the table that our php application stores its data. We'll copy this file to the image and when we create a container from that image, a simple table will be created.

php is the folder where our main web app is located. Inside this folder, there are 3 files. It's really simple web app which allows you to record contact details. Kind of primitive crm. Let's have a look at Dockerfiles before building the images. 

Type: 
```shell
$ cat Dockerfile
```
Output will be something like:
```shell
FROM php:7.3-apache
RUN apt-get update -y && apt-get install curl mariadb-client-10.3 -y
RUN docker-php-ext-install mysqli && docker-php-ext-enable mysqli
RUN mkdir /var/www/html/images
RUN chmod 777 /var/www/html/images
COPY ./php/ /var/www/html/
HEALTHCHECK --interval=30s --timeout=3s CMD curl -f http://localhost/ || exit 1
```

We use official php image as our base. Then we install couple of binaries that we need and create a folder where we'll store uploaded images and after that we copy our web app into the image. So far nothing unknown. But now we have a new instruction which is HEALTHCHECK. The HEALTHCHECK instruction tells Docker how to test a container to check that it is still working. This can detect cases such as a web server that is stuck in an infinite loop and unable to handle new connections, even though the server process is still running so the container is up. When a container has a healthcheck specified, it has a health status in addition to its normal status. Therefore we can monitor our container's real status and take action if something goes wrong. In our case, we want from Docker that each container created from that image will start a healthchecking process and continue to check every 30 seconds. If contanier will get a response from http://localhost/, Docker will mark it as healthy, otherwise unhealthy. 

All good but there's one thing strange. We don't have any CMD instruction in this file. So how will the container know which application to run when it starts? Answer is really simple. When you build an image, Docker inherits all settings from the base image. If you spesify anything on your Dockerfile, it overwrites the value of the base image. But if you left it blank, Image uses the value of the base image. In our case, we don't have the CMD instruction, so Docker will inherit this from base image. That's enough for the first Dockerfile. Let's take have a look at the mysql's Dockerfile too. 

Type: 
```shell
$ cat Dockerfile
```
Output will be something like:
```shell
FROM mysql:5.7
COPY createtable.sql /docker-entrypoint-initdb.d
```

This is really short. We're gonna use mysql:5.7 as our base and copy createtable.sql to the /docker-entrypoint-initdb.d folder. When a mysql container is started for the first time, a new database with the specified name will be created and initialized with the provided configuration variables. Furthermore, it will execute files with extensions .sh, .sql and .sql.gz that are found in /docker-entrypoint-initdb.d. So we copy our sql script to this folder and when a container starts from that image, it'll create our table.  (See https://hub.docker.com/_/mysql for details)

It's time to build the images. 

Type: 
```shell
$ docker image build -t your_dockerhub_id\php:v1 .
```
Output will be something like:
```shell
Sending build context to Docker daemon  11.26kB
Step 1/7 : FROM php:7.3-apache
Step 2/7 : RUN apt-get update -y && apt-get install curl mariadb-client-10.3 -y
 ---> Running in dca6dcd061df
[…]
[…]
[…]
Step 6/7 : HEALTHCHECK --interval=30s --timeout=3s CMD curl -f http://localhost/ || exit 1
 ---> Running in d6dc02b8ca50
Removing intermediate container d6dc02b8ca50
 ---> bf3e03646cc0
Step 7/7 : COPY ./php/ /var/www/html/
 ---> 53959f571f38
Successfully built 53959f571f38
Successfully tagged ozgurozturknetphp:v1
```

Php image is ready. Let's build the mysql now. 

Type: 
```shell
$ docker image build -f Dockerfile.mysql -t your_dockerhub_id\mysql:v1 .
```
Output will be something like:
```shell
Sending build context to Docker daemon  11.26kB
Step 1/2 : FROM mysql:5.7
Status: Downloaded newer image for mysql:5.7
 ---> 718a6da099d8
Step 2/2 : COPY createtable.sql /docker-entrypoint-initdb.d
 ---> 2dfc8038fc98
Successfully built 2dfc8038fc98
Successfully tagged ozgurozturknetmysql:v1
```

Done. Images are ready. It's time to run our application but first let's create a new bridge network. Our php web site will access to mysql database by its name. Therefore, these containers must be able to resolve each others name. 

Type: 
```shell
$ docker network create php-mysql-net
```
Output will be something like:
```shell
f3b75a829c3f7a8d5268dbf9dcb884071b3affaed642b3fe6354e78193d054c6
```
Images are ready. Bridge network has been created. We're ready to run the containers. 


Type: 
```shell
$ docker container run -d --name phpapp --network php-mysql-net -p 80:80 --env-file env.list your_dockerhub_id\php:v1
```
Output will be something like:
```shell
c580f355ad836c1021ee5959970bdf53c93de088c701af4110e1dfd8e976a80b
```

Type: 
```shell
$ docker container run -d --name mysqldb --network php-mysql-net --env-file envmysql.list your_dockerhub_id\mysql:v1
```
Output will be something like:
```shell
02d51c5ad53c0f1fd0679a5b0f4b60a742a07eecd9eb2f7eebbb8eb800bed73e
```

Type: 
```shell
$ docker ps
```
Output will be something like:
```shell
CONTAINER ID        IMAGE                    COMMAND                  CREATED              STATUS                        PORTS                 NAMES
02d51c5ad53c        ozgurozturknetmysql:v1   "docker-entrypoint.s…"   53 seconds ago       Up 52 seconds                 3306/tcp, 33060/tcp   mysqldb
c580f355ad83        ozgurozturknetphp:v1     "docker-php-entrypoi…"   About a minute ago   Up About a minute (healthy)   0.0.0.0:80->80/tcp    phpapp
```

Containers are up and running and also phpapp's status is healthy. Let's open a browser and see if the webapp is also working and can connect to mysql database. Visit http://127.0.0.1

<img src="./img/php1.png">

Fill the form and click add.

<img src="./img/php2.png">

If you saw this message, everything is fine. Click View and check your records. 

<img src="./img/php3.png">

Congratulations! You have succesfully built a 2 tier web app and run that. 

Now we can stop and delete the containers. Please don't delete the images for now. We need them on the next challange. 

Type: 
```shell
$ docker container rm -f mysqldb phpapp
```

***
**6: Docker commit**

Dockerfile is not the only way to create an image. We can convert a container to an image too. It can be useful to commit a container’s file changes or settings into a new image. This allows you to debug a container by running an interactive shell, or to export a working dataset to another server. Generally, it is better to use Dockerfiles to manage your images in a documented and maintainable way, but sometimes this type of commit method is also needed. Let's try this. First let's create a container and create a file in it. 

Type: 
```shell
$ docker container run -it --name commit_test busybox sh
```
Output will be something like:
```shell
/ # mkdir /test
/ # cd /test
/test # touch test.txt
/test # echo "hello world" > test.txt
/test # exit
```

Let's assume that we have an important container. We connected to it made lots of changes. Created folders and files, installed something etc. We don't want to loose our efforts and keep this container as an image. So we can move it anywhere. Let's commit this container and convert it to an image. 

Type: 
```shell
$ docker commit commit_test ozgurozturknet/commit:latest
```
Output will be something like:
```shell
sha256:8f5d8a8e42bd9419f6a932c0e70b0700f0618096d6c3f4a06753520fac236ed7
```
Our image is ready. Now if we want, we can push it to our repository and move it to anywhere we want. 


## Wrap up

__Congratulations__ you have completed the Image and Registry challange. You've just learned how to create Docker images and play with them. 

*** Reference: https://docs.docker.com