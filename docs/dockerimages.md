##  Building Docker Images with Dockerfile

Previously, we utilized **images** from the **registry (Dockerhub)** and instructed the Docker client to run a container based on that **image**. In this section, we will learn to create custom Docker images using Dockerfile. 

### What is an image?
***Docker images*** are like blueprints that contain the application code, runtime environment, libraries, dependencies, and other configuration required to run an application. They are essential for containerization. Docker images are lightweight, portable, and allow for consistent deployments across different environments.

 **Let's begin by listing the local images available on the device and understand important concepts about images.**
```bash
$ docker images
```
**Example**
```
$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
alpine        latest    9c6f07244728   2 weeks ago     5.54MB
hello-world   latest    feb5d9fea6a5   11 months ago   13.3kB
```
Above is a list of **images** that I've pulled from the registry. You will have a different list of images on your machine. 

- The `TAG` refers to a particular snapshot of the image and the `ID` is the corresponding unique identifier for that image.

**Images** can be [committed](https://docs.docker.com/engine/reference/commandline/commit/) with changes and have multiple versions. 

>When you do not provide a specific version number, the client defaults to `latest`.

For example you could pull a specific version of `ubuntu` image as follows:

```bash
$ docker pull ubuntu:20.04
```

- If you do not specify the version number of the image then, as mentioned, the Docker client will default to a version named `latest`.

***For example to get the latest version of ubuntu image:***

```bash
$ docker pull ubuntu
```
***Some Tips on Docker Images***
- You can get a new Docker image  from a registry (such as the **Docker hub**) or create your own. 
- There are hundreds of thousands of images available on [Docker Store](https://store.docker.com). 
- You can also **search for images** directly from the command line using `docker search`.<br> <br>
 ***For example, if you want to search a MySQL related Image*** 
```
$ docker search mysql
NAME                            DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                           MySQL is a widely used, open-source relation…   13077     [OK]
mariadb                         MariaDB Server is a high performing open sou…   5001      [OK]
phpmyadmin                      phpMyAdmin - A web interface for MySQL and M…   614       [OK]       
percona                         Percona Server is a fork of the MySQL relati…   584       [OK]
```   
### Types of Images

- **Base images** are images that have no parent images, usually images with an `OS` like ubuntu, alpine or debian.

- **Child images** are images that are build on base images and add additional functionality.

Another key concept is the idea of _official images_ and _user images_. (Both of which can be base images or child images.)

**Official images**
- Images that are verified by Docker.
- A dedicated team is responsible for reviewing and publishing all Official Repositories content. 
- ***Examples:*** `python`, `node`, `alpine` and `nginx` images are official (base) images. To find out more about them, check out the [Official Images Documentation](https://docs.docker.com/docker-hub/official_repos/).

**User images** 
- Images created and shared by users like you and me. 
- They build on base images and add additional functionality. 
- Typically these are formatted as `user/image-name`. The `user` value in the image name is your Dockerhub user or organization name.

