---
title: "How to Build a Container Image Running on Docker Hub."
datePublished: Mon Apr 14 2025 05:50:05 GMT+0000 (Coordinated Universal Time)
cuid: cm9gnmt2d000409jr93dyfd8n
slug: how-to-build-a-container-image-running-on-docker-hub
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1743744113204/72b84081-a88d-45b3-8550-07234fc6bd0a.png
tags: docker, docker-images, building-docker-container-and-image

---

In this blog post, I will guide you through the process of building a container image that runs on Docker Hub.

Additionally, I will explain what containers are, allowing you to establish a strong foundation in understanding how container images work.

By the end of this post, you will have a practical understanding of containerization and be able to build your container images.

So, let's dive in and explore the world of container images

### **What is a Container?**

In Docker, a container is **<mark>a lightweight, standalone, and executable package of software that includes everything needed to run an application, like code, dependencies, and libraries, without relying on the host machine's environment</mark>**<mark>.</mark>

**Here's a more detailed explanation:**

* **What it is:**
    
    A container is a runnable instance of a Docker image, which is a read-only template containing the application's code and dependencies.
    
* **How it works:**
    
    Docker containers share the host machine's operating system kernel, making them lightweight and efficient.
    
* **Why use it:**
    
    Containers isolate applications and their dependencies, ensuring they run consistently across different environments, eliminating "it works on my machine" problems and facilitating faster software delivery.
    
* **Benefits:**
    
    * **Portability:** Containers can run on any system that supports the container runtime, regardless of the underlying operating system.
        
    * **Isolation:** Each container runs in its isolated environment, preventing application conflicts and ensuring a secure environment.
        
    * **Resource efficiency:** Containers share the host's kernel, requiring fewer resources than virtual machines.
        

### **What is an Image?**

An image can be defined as a software package, for instance, an operating system. These Images are stored in what we call an **image repository.**

To get a machine image into our environment, we use a "PULL" operation. For instance, if we want to download redis. We input the **"command docker pull redis"** To start an image, we use the **"run"** command.

For more information on how to install the Docker engine in your Linux terminal, see the steps [**here**](https://docs.docker.com/engine/install/ubuntu/). To check if Docker has been successfully installed, you can check with the command "**docker -v"**

### **Let's Get Started**

**Prerequisite**: Ensure you have [`installed Docker on your Linux terminal.`](https://docs.docker.com/engine/install/ubuntu/)

a) To begin, enter into root user with the command `"sudo su"` and press **Enter**

b) Thereafter, you are prompted to input your password, which doesn't reflect when you type it.

c) Having inputted your password, press **Enter**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743834913055/12287b89-17a9-4601-abde-e61144a3bc0f.jpeg align="left")

d) Input the `"apt update"` command on your system to have the latest information about available software updates before performing any package installations or upgrades.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743834935022/113d0744-aa05-4bf8-b9c9-8f80a1ceffc9.jpeg align="left")

e) The next step is to create your directory with the command `"mkdir"`, leave a space, and give your directory a name.

f) To open the directory, input the command `"cd"`, leave a space, and type the directory name. Press Enter.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743835397804/db0913b8-4bb6-4e5e-8142-ae20cc72c77c.jpeg align="left")

g) To create our text file use the command `"vi"` for default or install `"vim"` for the latest version. Leave a space and type in the default text file.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743835836011/fbb88b6e-6837-49bd-9233-d54b35014589.jpeg align="left")

h) Once you click on enter, the text file opens as shown in the capture below.

i) Click on the letter `"I"` on your desktop to enable you to type or insert your text in the space.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743855299756/0b9ce8d2-887e-492f-9886-c0137cdbabc7.jpeg align="left")

j) To build our container image, the first command is `"FROM"`. It specifies the base image that will be used as the starting point for building our custom Docker image. Then we input the following commands:

**i)** `FROM ubuntu`: The `"FROM"` command and the Ubuntu base image name will be used to build our Docker image. This means our Docker image is housed in the Parent image, called "ubuntu".

**ii)** `LABEL maintainer=umeokolivincent@gmail.com`: The `"LABEL"` command is an instruction used to add metadata to a Docker image in the form of key-value pairs.

**iii)** `CMD [ "echo", "Welcome"]`: The `"CMD"` command is used to give an instruction. In this demo, we are telling Docker that when the container image starts, it should show the message "Hello World" by default.

iv) `WORKDIR /app`: The `WORKDIR` command sets the working directory (also called the "current directory") for any subsequent instructions like `RUN`, `CMD`, `COPY`, `ADD`, or `ENTRYPOINT`. It functions similarly to `cd` in Linux but persists for the rest of the Dockerfile instructions.

v) `COPY . /app`: The `COPY` command in a Dockerfile is used to **copy files or directories from the host machine (your local system) into the Docker image** during the build process. It is one of the most frequently used instructions for adding application code, configuration files, or dependencies into the container.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743855313187/ac48766c-e57d-40e8-8fb2-a2daf7bc685e.jpeg align="left")

k) To exit the text space,

i) Press the `ESC` button on your desktop

ii) Press the **space button** on your desktop

iii) Input the `:wq` command. This command is used within the **Vim text editor** to save changes to the file and exit the editor.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743855323255/f2542170-d28e-4c95-84ab-02a6fc4181de.jpeg align="left")

L) Having inputted the `:wq` command above, the text file page will close. You will be returned to your directory as shown below.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743837330973/df4f1239-d0b3-491a-8f70-c9736f2a9671.jpeg align="left")

m) The next step is to build our container image.

n) Input the command `docker build -t utomimg .` Once you press Enter, your container image starts to build.

o) Once it has finished building, you will see the "**FINISHED**" command.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743855766556/a97a6bb2-51d7-455c-81ac-d7d7a9327a42.jpeg align="left")

p) Type in `docker images` command to display all the images created, including the recently created image “**utomimg**“. You can also use the `ps` or `ps -a` commands to view the running images or all images.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743857562157/1c88bbba-6749-4b72-987e-4b2c85223dbd.jpeg align="center")

q) The next step is to log into Docker Hub and create a repository so that we can be able to push our image. Take note of the docker push, Docker Hub name, and the repository name.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743862355763/30a030e8-b3a7-45f3-ae59-7d361d4250c6.jpeg align="center")

r) We then log in to Docker Hub from the terminal, typing the following command: `docker login —username=<name of the Docker Hub account>`. It would prompt you for your Docker Hub password, input it and hit Enter, then you will be logged into the Docker Hub remotely.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743861287836/8999c6b1-6a4c-42d6-ab43-ac37b8841812.jpeg align="center")

s) We then put a new tag to our repository by running the command: `docker tag <name of image> <name of your repository>:latest`, hit Enter.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743862406170/d3eef336-1214-4478-a204-08d6afe6f5b2.jpeg align="center")

t) We then run `docker inspect` to check our image.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743862854811/b35aeb0c-2a18-4aa9-ac07-80e950b38838.jpeg align="center")

u) We push by running `docker push <name of the repository>`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743863646312/383c942e-4bd4-46f7-84e9-a9b17c358d5a.jpeg align="center")

We go to the Docker Hub to check if the image has been pushed to the repository, as seen below.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1744607711026/fb8fee2d-ea80-455b-9a1c-173d8bda0e32.jpeg align="center")

v) We then use the image created to create a container. type `docker run -d —name <name of container> -p 8080:80 <name of image>`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743864150769/f51d5e5f-2117-4de4-93eb-4d6be4bb77cb.jpeg align="center")

w) We then run `docker ps` to see the status of our container and image.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1744604878223/85d76b06-6030-48b5-a7a4-4ebbc17db918.jpeg align="center")