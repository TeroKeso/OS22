### Create your first image
Now that you have a better understanding of images, it's time to create your own.

#### Docker File
- A Dockerfile is simply a text file that contains the build instructions for an image. It automates the image creation process.
  - It specifies a base image.
  - It includes instructions to install required tools for your app. 
  - It includes intructins to install libraries, dependencies and pacakages. 
  - It then builds your app. 
- It is a step by step set of standard instructions such as `FROM`, `COPY`, `RUN`, `ENV`, `EXPOSE`, `CMD` 
- The commands you write in a Dockerfile are almost identical to their equivalent Linux commands.

- The name of the file is  `Dockerfile` without any extension and the letter **D** is capital. 

- Write only the minimum set of steps that is needed for your app. Avoid unnecessary components. 

#### Dockerfile basic commands 
***FROM***
 - The **FROM** command must be the first line in the Dockerfile. Since images are made up of layers, you can utilize one of those images as the foundation for your own. The **FROM** command defines your **base layer**. 
```  
FROM alpine:3.14 
``` 
> The above line specifies that the base image is going to be alpine:3.14. 

- It accepts the image's name as parameters. You can optionally include the image version and the maintainer's Docker Hub username in the following format:`username/imagename:version`.

***WORKDIR***
- It defines the working directory of a Docker Conainter.
- Any RUN, CMD, COPY or ENTRYPOINT COMMAND will be executed in the specified working directory.

##### Lets specify our working directory in the Docker file. 

```  
FROM alpine:3.14 
WORKDIR /usr/src/app/ 
``` 
***COPY***
- It copies local files or directories into the container.

 COPY `<src>` `<destination>`

 To understant the concept, let's write a simple script called **hello.sh** in your device. We will copy this file to the container and ask to run at the time of building our image.

 ```
 #!/bin/sh
 echo "Hello, World, I am learning to write a Dockerfile!"
 ```
***Now our Docker file looks like this***
```  
FROM alpine:3.14 
WORKDIR /usr/src/app/  
COPY hello.sh .
``` 
***RUN***
- It allows you to install your applications and pacakages you need for your app. 
-  For each RUN command, Docker will run the command then create a new layer of the image. 
- The Docker daemon runs instrcutions in the Docker file one by one. 
- Before running the instructions, the Docker Daemon validates the file and if the syntax is incorrect, it returns an error. 
- Each run instruction is independent and it causes a new image to be created.
- ***Lets update, install nano, add add executable permission to the hello.sh file***

```  
FROM alpine:3.14 
WORKDIR /usr/src/app/  
COPY hello.sh .
RUN apk update
RUN apk add nano 
RUN chmod +x hello.sh 
``` 

***CMD***
- It defines the commands that will run on the Image at start-up. 
- Unlike a RUN, this does not create a new layer for the Image, but simply runs the command. 
- There can only be one CMD per a Dockerfile/Image. 
- If you need to run multiple commands, the best way to do that is to have the CMD run a script. 
- CMD requires that you tell it where to run the command. 
- ***Lets run our simple script at the start-up***

```  
#Start from the alpine image 
FROM alpine:3.14 

#it defines the working directory
WORKDIR /usr/src/app/  

#copies the script file to the container
COPY hello.sh .

#Installs updates
RUN apk update

#Installs nano text editor
RUN apk add nano 

#it adds executable permission to our script hello.sh
RUN chmod +x hello.sh 

#it runs the script at the start-up
CMD ./hello.sh
``` 
***Read More at [Docker File Reference Page](https://docs.docker.com/engine/reference/builder/)***

### Build your first image
Now that our docker file is ready, we will use ***[docker build](https://docs.docker.com/engine/reference/commandline/build/)*** command to build our image. 
- **docker build** command will look for Dockerfile and build as per instructions in the Dockerfile. 
  - Let's create a directory and save the Dockerfile and hello.sh in the directory.
   - Get into the directory with the CD command and exectue the following command. 

> ***Note: Please use IDE such as Visual Studio code to create Dockerfile. If you use notepad, it adds .txt extension to the file and the build won't work.***

 The following command looks for the Dockerfile. The (.) specifies where to build and -t gives it a name.
```
PS D:\dock1> docker build . -t my-first-image
[+] Building 8.8s (11/11) FINISHED
 => [internal] load build definition from Dockerfile                                                                             0.1s
 => => transferring dockerfile: 438B                                                                                             0.0s
 => [internal] load .dockerignore                                                                                                0.0s
 => => transferring context: 2B                                                                                                  0.0s
 => [internal] load metadata for docker.io/library/alpine:3.14  
```
###### Lets check our image and note the size of the image (my-first-image). It is 15.8MB 
```
PS D:\dock1> docker images
REPOSITORY       TAG       IMAGE ID       CREATED          SIZE
my-first-image   latest    2c03d638427a   46 seconds ago   15.8MB
alpine           latest    9c6f07244728   2 weeks ago      5.54MB
hello-world      latest    feb5d9fea6a5   11 months ago    13.3kB
```
### Run your first image
```
PS D:\dock1> docker run my-first-image       
Hello, World, I am learning to write a Dockerfile!
```

***Lets get the interactive terminal from our image and use nano to create a file called new.txt***
 ```
 PS D:\dock1> docker run -it my-first-image /bin/sh
/usr/src/app # ls
hello.sh
/usr/src/app # nano new.txt
/usr/src/app # ls
hello.sh  new.txt
```
### Lets remove the nano from our Dockerfile

```
# Start from the alpine image 
FROM alpine:3.14 

# it defines the working directory
WORKDIR /usr/src/app/  

# copies the script file to the container
COPY hello.sh .

# Installs updates &  it adds executable permission to our script hello.sh (no nano this time)
RUN apk update && \
chmod +x hello.sh 

# it runs the script at the start-up
CMD ./hello.sh
```
##### Now build the image again  as my-first-image2 wihtout nano. Since vi is already available, we don't necessarly need the nano text editor.
```
PS D:\dock1> docker build . -t my-first-image2
[+] Building 3.4s (9/9) FINISHED
 => [internal] load build definition from Dockerfile                                                                             0.0s
 => => transferring dockerfile: 419B                                                                                             0.0s 
 => [internal] load .dockerignore                                                                                                0.0s 
 => => transferring context: 2B                                                                                                  0.0s 
 => [internal] load metadata for docker.io/library/alpine:3.14
 ```

#### Check the size of image

```
PS D:\dock1> docker images
REPOSITORY        TAG       IMAGE ID       CREATED          SIZE
my-first-image2   latest    54ffbbbdf9b1   28 seconds ago   7.76MB
my-first-image1   latest    d7a77ec9a86f   5 minutes ago    15.8MB
my-first-image    latest    4e9767cedcf4   16 minutes ago   15.8MB
```
### Push your first image
Now that you've created and tested your image, you can push it to [Docker Hub](hhttps://hub.docker.com/).

***First you have to login to your Docker Hub account***
```bash
docker login
```
Enter `YOUR_USERNAME` and `password` when prompted. 
```bash
PS D:\dock1> docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: deepakkc
Password: 
Login Succeeded
```
Now all you have to do is:

```bash
PS D:\dock1> docker tag  my-first-image2 deepakkc/my-first-image2
PS D:\dock1> docker push deepakkc/my-first-image2
Using default tag: latest
The push refers to repository [docker.io/deepakkc/my-first-image2]
e6244dc07df3: Pushed
5d6c8546b9b6: Pushed
596c0c0d55cf: Pushed
```
**Now yow you may delete your image and run it again. This time it will pull image from Docker Hub.**

```
PS D:\dock1> docker rmi deepakkc/my-first-image2 -f
Untagged: deepakkc/my-first-image2:latest
Untagged: deepakkc/my-first-image2@sha256:98d5f7439d211156871e8088ae5bf3a2289c9bf00
PS D:\dock1> docker images
REPOSITORY        TAG       IMAGE ID       CREATED         SIZE
<none>            <none>    54ffbbbdf9b1   2 hours ago     7.76MB
my-first-image1   latest    d7a77ec9a86f   2 hours ago     15.8MB
my-first-image    latest    4e9767cedcf4   3 hours ago     15.8MB
alpine            latest    9c6f07244728   2 weeks ago     5.54MB
hello-world       latest    feb5d9fea6a5   11 months ago   13.3kB
PS D:\dock1> docker run deepakkc/my-first-image2
Unable to find image 'deepakkc/my-first-image2:latest' locally
latest: Pulling from deepakkc/my-first-image2
Digest: sha256:98d5f7439263ecbe8088ae5bf3a2289c9b24ea4f00
Status: Downloaded newer image for deepakkc/my-first-image2:latest
Hello, World, I am learning to write a Dockerfile!
PS D:\dock1> docker images
REPOSITORY                 TAG       IMAGE ID       CREATED         SIZE
deepakkc/my-first-image2   latest    54ffbbbdf9b1   2 hours ago     7.76MB
my-first-image1            latest    d7a77ec9a86f   2 hours ago     15.8MB
my-first-image             latest    4e9767cedcf4   3 hours ago     15.8MB
alpine                     latest    9c6f07244728   2 weeks ago     5.54MB
hello-world                latest    feb5d9fea6a5   11 months ago   13.3kB
```
> Note: if you don't have an account, visit [Docker Hub](hhttps://hub.docker.com/) and create an account. 
> ***Now that you are done with this container, stop and remove it since you won't be using it again.***




>**Note:** If you want to learn more about Dockerfiles, check out [Best practices for writing Dockerfiles](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/).

## Exercise
1. Write a Dockerfile to build a Docker image using Ubuntu. Add MySQL database service to your image and push it to DockerHub. Tag the image as username/ubuntu-git:1.1 
2. Run your image from DockerHub, get to the interactive terminal and verify that you have installed MySQL (type `sudo mysql` in terminal, if you get the mysql prompt, it's installed. To exit, type `exit`)
3. Write a Dockerfile to create an image with CMD instruction (any). Use Alpine image as the base image.  
4. Continue working on the Dockerfile created in Task 3. Specify a working directory (WORKDIR as /opt) and use RUN to write "This is my work directory " in a text file test.txt (`echo "This is my work Directory" >test.txt`). Build Docker image as **workdir:v1** and run the image as `docker run -it wordkir:v1 ls`. Make sure that you find **test.txt**. 
5. Write a Docker file to build a Docker image that runs a simple Java app in a Docker container. 
6. Cleanup: You probably don't need any of these containers, therfore you may delete all of them.

## Creating Docker Volume within a **Dockerfile**

###### Content of the Dockerfile
```bash
FROM alpine:latest
RUN mkdir /data
WORKDIR /data
RUN echo "Hello from Volume" > test
VOLUME /data
CMD cat test
```
- Build the image `docker build . -t   vv`
- Run it `docker run vv`  
- Get the shell access and create some more files to it.
- Delete the container and run the image again by attaching the volume . 
- Check if your files still exist.

## Creating Docker Volume within a **Dockerfile**

###### Content of the Dockerfile
```bash
FROM alpine:latest
RUN mkdir /data
WORKDIR /data
RUN echo "Hello from Volume" > test
VOLUME /data
CMD cat test
```
- Build the image `docker build . -t   vv`
- Run it `docker run vv`  
- Get the shell access and create some more files to it.
- Delete the container and run the image again by attaching the volume . 
- Check if your files still exist.

