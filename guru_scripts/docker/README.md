Installing Docker Desktop
============================
- Install Docker on your OS (choose one)
   - Intsall Docker for Mac OS laptop, follow this video
     https://www.youtube.com/watch?v=MU8HUVlJTEY

   - Install Docker for Linux, follow this video
     https://www.youtube.com/watch?v=KCckWweNSrM

   - Intsall Docker for Windows OS laptop, follow this video
     https://www.youtube.com/watch?v=ymlWt1MqURY

- Configure Docker Desktop using at least 2 cores, and 10G memory. Recommend 4 cores, 16G memory

  - Click Dock Desktop icon, click Preferences...>>Advanced menu, drag CPU and Memory slide to the desired configuration,
quit and restart docker desktop

- Master Docker Container and Image concepts by watching this video
  https://www.youtube.com/watch?v=Rv3DAJbDrS0

Prepare Shared Folder On Host OS With Docker Container
=================================================================
Open a shell on your host machine, find a directory where you want to share data with docker container. 
Create the data folder there to share between your host machine and docker container. Grant read/write/execute permission to the folder. 

        mkdir data
        chmod 777 data

You can mount(map) the "data" folder to a folder under the docker container (will show -v of the mount command later). 
Then, you can share files between your host OS and Docker OS. 

Suppose we mount the host OS ~/data folder to a docker folder /home/tigergraph/mydata, then anything we put on ~/data will be visible in docker container under /home/tigergraph/mydata, and vice versa.  

Since our dev edition does not support backup/restore data, you can persist your data (raw file, gsql script etc.) 
on the data volume. After upgrading Dev version, you can start a new container using the same data volume. 

Pull Pre-built TigerGraph Docker Image And Run It As A Server
================================================================
One command pull docker image and bind all ports for first time user from the TigerGraph docker registry. 
This image will start as a daemon, so user can ssh to it. 

1. remove old version, only do this step in shell if you upgrade your docker image

        docker rmi -f docker.tigergraph.com/tigergraph-dev:latest > /dev/null 2>&1
        docker pull docker.tigergraph.com/tigergraph-dev:latest
    > Note: replace "latest" with specific version number if a dedicated version of TigerGraph is to be used. E.g. If you want to get 2.4.1 version, the following would be the URL. 
     docker pull docker.tigergraph.com/tigergraph-dev:2.4.0

1. stop and remove existing container in shell only if an old version is being used

        docker ps -a | grep tigergraph_dev
        docker stop tigergraph_dev
        docker rm tigergraph_dev

1. pull the tigergraph docker image and run it as a daemon, change the ports accordingly if there is a conflict. 
   - the command is very long, user need to drag the horizontal bar to the right to see the full command. 
   - The command does the following
     - "-d" make the container run in the background. 
     - "-p" map docker 22 port to host OS 14022 port, 9000 port to host OS 9000 port, 14240 port to host OS 14240 port.
     - "--name" name the container  tigergraph_dev. 
     - "--ulimit" set the ulimit (the number of open file descriptors per process) to 1 million.
     - "-v" mount the host OS ~/data folder to the docker /home/tigergraph/mydata folder using the -v option. Note that if you are using windows, change the above ~/data to something using windows file system convention, e.g. c:\data
     - download the "latest" docker image from the TigerGraph docker registry url docker.tigergraph.com/tigergraph-dev. 
```bash
   docker run -d -p 14022:22 -p 9000:9000 -p 14240:14240 --name tigergraph_dev --ulimit nofile=1000000:1000000 -v ~/data:/home/tigergraph/mydata -t docker.tigergraph.com/tigergraph-dev:latest
```        

After pulling the image and launch the container in the background, you can try the following to verify it's running. 
1. verify that container is running, you should see a row to describe the running container.

        docker ps | grep tigergraph_dev
        
1. open a shell on your host OS to ssh to the container. At prompt, enter "tigergraph" without quotes as password.
         
         ssh -p 14022 tigergraph@localhost
1. start TigerGraph service 

         gadmin start

Optional: stop/start container

        docker container stop tigergraph_dev
        docker container start tigergraph_dev
        
        

Content of the Docker Image
================================
In the dev image, we include 

- ssh server 
- git
- wget
- curl
- emac, vim etc. 
- jq
- tar
- tutorial: gsql 101, gsql 102 sub folders.
- latest gsql open source graph algorithm library: gsql-graph-algorithms folder

More Operation Commands
==========================
- Start/shutdown Docker Desktop
  - To shut down it, click you docker desktop, click "Quick Docker Desktop" will shutdown it. 
  - To start it, find the Docker Desktop icon, double click it. 

- Start/shutdown TigerGraph service
  - After you start Docker Desktop, use the below command to start the container created 

        docker start tigergraph_dev

1. ssh to the container, if localhost is not recognized, remove localhost entry from ~/.ssh/known_hosts

        sed -i.bak '/localhost/d' ~/.ssh/known_hosts
        ssh -p 14022 tigergraph@localhost
    > Linux users can access the container through its ip address directly

        docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' tigergraph_dev
        ssh tigergraph@<container_ip_address>

1. enter password for tigergraph user

        tigergraph

1. start tigergraph service

        gadmin start 

1. start graph studio
open a browser on your laptop and access:

        http://localhost:14240

1. check version

        gsql version
    > Use 2.4 and above

Documents and Forum
=====================
- Tutorial

    https://docs.tigergraph.com/intro/gsql-101

    https://docs.tigergraph.com/intro/gsql-102

- Forum
If you like the tutorial and want to explore more, join the GSQL developer community at 

    https://groups.google.com/a/opengsql.org/forum/?hl=en#!forum/gsql-users

- Videos to learn

    https://www.youtube.com/channel/UCnSOlBNWim68MYzcKbEswCA/videos

- ebook 

    https://info.tigergraph.com/ebook

- webinars 

    https://www.tigergraph.com/webinars-and-events/
