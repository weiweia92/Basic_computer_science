## Docker  

Docker 和虚拟机技术的不同：

- 传统虚拟机，虚拟出一条硬件，运行一个完整的操作系统，然后在这个系统上安装和运行软件。
- 容器内的应用直接运行在宿主机上，容器没有自己的内核，也没有虚拟我们的硬件，所以就轻便了。
- 每个容器间是相互隔离的，每个容器内有属于自己的文件系统，互不影响。

![](pics/1516083786353046.png)

### Docker Images  

docker镜像就好比一个模板，可以通过这个模板来创建容器服务， tomcat 镜像--> run --> tomcat01 容器（提供服务器）。通过这个镜像可以创建多个容器（最终服务运行或者项目运行就是在容器中）

1.Read Only Template Used To Create Containers  

2.Build By Docker User  

3.Stored In Docker Hub Or Your Local Registry  

- `docker images`:lists down all the images in your local repo  

![](https://github.com/weiweia92/pictures/blob/master/Screenshot%20from%202020-07-01%2009-44-12.png)  

### Docker Container  

Docker利用容器技术，独立运行一个或一组应用，通过镜像来创建的（Docker Images --> run to generate Docker Containers）。启动、停止、删除，基本命令！ 目前可以把这个容器理解为一个简易的linux系统。

- `docker container logs [CONTAINER ID]`: come out the container logs  

- `docker container kill`:

- `docker container rm [CONTAINER ID]`: remove the docker container  

- `docker container run`   

- `docker container start`  

### Docker respority

仓库是存放镜像的地方，仓库分为公有仓库和私有仓库，Docker Hub（默认是国外的）阿里云都有容器服务（配置镜像加速）

## Docker Commands  

How to add docker to sudo:  

`sudo groupadd docker`  

`sudo gpasswd -a weiweia92 docker`  

`newgrp docker`    

- `docker --version`:returns the version of Docker which is installed  

We need to start the docker service after install.  

- `sudo service docker start`    

- `docker info`:查看docker信息  

此时的docker是一个‘裸’docker，没有container也没有image，image简单理解就是container的只读版，用来方便存储与交流  

- `docker pull ubuntu:18.04`:pulls a new Docker image from the Docker Hub  

![](https://github.com/weiweia92/pictures/blob/master/Screenshot%20from%202020-07-01%2009-43-02.png)

`-it`:表示交互  
仔细看，你会发现终端交互的用户名变掉了，说明我们进入到了容器的内部,执行`exit`退出此终端，回到系统本身的终端  

![](https://github.com/weiweia92/pictures/blob/master/Screenshot%20from%202020-07-01%2009-43-23.png)

- `docker ps`:This command lists all the running containers in the host  

- `docker ps -a`:If '-a' flag is specified,shutdown containers are alse displayed  

- `sudo docker commit -m "Added ubuntu18.04" -a "weiweia92" 79c761f627f3 weiweia92/ubuntu:v1`  

    - `commit`命令用来将容器转化为镜像  
    - `m`用来来指定提交的说明信息;`-a`可以指定用户信息的;`79c761f627f3`代表容器的`id`;`weiweia92/ubuntu:v1`指定目标镜像的用户名、仓库名和 `tag` 信息  

![](https://github.com/weiweia92/pictures/blob/master/Screenshot%20from%202020-07-01%2009-44-00.png)

- `docker login`:The command is used to Login to Docker Hub repo from the CLI  

- `docker push`:This command pushed a Docker image on your local repo to the Docker Hub  

- `docker run -it test:0.1`:This command executes a Docker image on your local repo&creates a running Container out of it  

- `docker rm <CONTAINER ID>`:remove the container

- `docker rmi -f <IMAGE>`:remove docker image  

- `docker build -t <REPOSITORY>:<TAG> .`:This command is used to compile the Dockerfile,for building custom Docker images  

- `docker stop <CONTAINER ID>`:This command shuts down the container whose Container ID is specified in arguments.Container is shut down gradfully by waiting for other dependencies to shut, not a force stop  

- `docker kill <CONTAINER ID>`:This command kills the container by stopping its execution immediately.Its similar to force kill.  

- `docker exec`:This command is used to access an already running container and perform operations inside the container.  

`eg:docker exec -it fe6e370a1c9c bash`  

- `docker commit`:This command creats a new image of an edited container on the local repo  

`eg:docker commit fe6e370a1c9c vardhanns/MyModifiedImage`  

- `docker export --output=<'filename.tar'> <CONTAINER ID/IMAGE NAME>`:This command is used to export a Docker image into a tar file in your local system.  

`eg:docker export --output='latest.tar' mycontainer`   

- `docker import <tar file path>`:This command is used to import the contents of a tar file(usually a Docker image)into your local repo.  

`eg:docker import /home/weiweia92/Downloads/demo.tgz`  


- `docker search 关键字`  

`eg：docker search redis`,检索镜像(一般从docker hub检索)  

- `docker images | grep none | awk '{print $3} ' | xargs docker rmi`:删除所有名称为none的镜像  

### 如何将建立好的镜像上传到dockerhub上  

首先打开Dockerhub登录自己的账号,建立一个新的仓库，仓库的名字要和待上传的镜像名字一致,实例中叫test  

![](https://github.com/weiweia92/pictures/blob/master/Screenshot%20from%202020-07-01%2009-44-23.png)

在终端执行`docker push weiweia92/test:0.1`  

![](https://github.com/weiweia92/pictures/blob/master/Screenshot%20from%202020-07-01%2009-44-32.png)

将yolov4模型的test镜像上传到dockerhub上  


`docker run -it test:0.1`: go to docker image  

Done!  
  

最好建立一个文件夹专门放Dockerfile的  

`mkdir docker-sample && cd docker-sample`  

`touch Dockerfile`
  
## Dockerfile Syntax  

Dockerfile syntax consists of two kind of main line blocks:comments and commands+arguments

注释用#


`ENV`（设置环境变量）、`EXPOSE`（暴露容器内部端口）也很常用，用法分别是：

`docker build -t <REPOSITY_NAME>:<TAG> -f <Dockerfile_Path>`：构建定制好的镜像 

`-t`:指镜像的标签信息

`docker images`:查看镜像

`docker ps -a`:查看容器是否运行

**FROM**   

From directive is probably the most crucial amongst all others for Dockerfiles.It defines the base image to use to start the build process，标志着我们定制的镜像是以哪个镜像作为基础进行制作的。 举个栗子: `FROM postgres:latest` 

`FROM [base image name]`  

`FROM ubuntu:18.04`  

**RUN**   

RUN command is the central executing directive for Dockerfiles.It takes a command as its argument and runs it to form the image. Unlike CMD,it actually is used to build the image. `RUN`的默认权限是`sudo`。需要注意的是，如果你需要执行多个 `RUN` 操作，那最好把它们合并在一行 (用`&&`连接)，因为每执行一次`RUN`就会在`docker`上新建一层镜像，所以分开来写很多个`RUN`的结果就是会导致整个镜像无意义的过大膨胀。 正确的做法: `bash RUN apt-get update && apt-get install vim`，不提倡的做法： `bash RUN apt-get update RUN apy-get install vim`

`RUN [command]`  

It has a slight difference from CMD.RUN is used to run a command,which could be a shell command or basically runs my image into a container.But CMD,it can execute a shell command like `CMD "echo""Welcome to Edureka"`,but however it can not use CMD to build my docker image.  

**ENTRYPOINT**  

**ADD**  

ADD command gets two arguments:a source and a destination. It basically copies the files from the source on the host into the container's own filesystem at the set destination.  

`ADD [source directory or URL] [destination directory]`  

`ADD /my_app_folder /my_app_folder`  

**ENV**  

The ENV command is used to set the environment variables(one or more).These variables consist of "key value"pairs which can be accessed within the container by scripts and applications alike.  

`ENV <KEY> <VALUE>`
eg: `ENV SERVER_WORKS 4`  

**WORKDIR**  

WORKDIR directive is used to set where the command defined with CMD is to be executed. WORKDIR允许你在Docker建立image时更改目录，the new directory remains the current directory for the rest of the build instructions.

`WORKDIR /path`

**CMD and ENTRYPOINT**  

CMD  

CMD instruction runs commands like RUN, but the commands run when the Docker container launches. Only one CMD instruction can be used.  容器启动时执行的命令 需要用`CMD`来执行一些容器启动时的命令，注意与`RUN`的区别，`CMD`是在`docker run`执行的时候使用，而`RUN`则是`在docker build`的时候使用，还有，划重点，一个`Dockerfile`只有最后一个`CMD`会起作用。 栗子： `bash CMD ["/usr/bin/wc","--help"]`

`CMD ["python", "app.py"]`  

ENTRYPOINT：  
    * 设置容器启动时运行的命令  
    * 让容器以应用程序或者服务的形式运行（后台进程）  
    * 不会被忽略，一定会执行  
    * 常见使用方式：写一个脚本作为ENTRYPOINT去执行  

**EXPOSE**  

EXPOSE command is used to associate a specified port to enable networking between the running process inside the c
ontainer and the outside world.  

`EXPOSE <PORT ID>` 
eg:`EXPOSE 8080`  

EXPOSE告诉Docker服务端容器暴露的端口号，供互联系统使用。在启动Docker时，可以通过-P,主机会自动分配一个端口号转发到指定的端口，如：

`docker run -d -p 127.0.0.1:23333:22 centos6-ssh`:容器ssh服务的22端口将被映射到宿主机的23333端口  

**MAINTAINER**  

THis non-executing command declares the author,hence setting the author field of the images.It should come nonetheless(尽管如此) after FROM. 这里要写上 `Dockerfile` 编写者的信息，一般写上自己的邮箱或者 `nickname`就可以，用法是 `LABEL maintainer="个人信息"`。 栗子: `LABEL maintainer="mambahj24@gmail.com"`

`MAINTAINER [name]`  

`MAINTAINER authors_name`  

**USER**  

The USER directive is used to set the UID(or username)which is to run the container based on the image being built.  

`USER [UID]`  

`USER 751`  

### 根据Dockerfile创建docker image  

建立完一个sample_image/Dockerfile后，创建docker image  

`sudo docker build -t sample_image .`  

### 运行Docker container   

`sudo docker run -ip 5000:5000 sample_image:latest`  

-i:交互  

-p:docker host port:Docker container port  


