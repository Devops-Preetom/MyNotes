Docker:

1. Kernel of the Docker host is shared between the docker images present on it .
	ex: if docker host is having Linux kernel , then only linux based images can be run on it , as kernel will be shared 

2. Docker images can be pulled from docker hub and spin up on the server.
	ex: docker run nginx , docker run mongodb, docker run nodejs

3. Diff between docker images & docker containers
	i. Docker images consist of one or more containers.
	ii. where as containers are separate enviroment and set of processes 

4. Docker has two versions
	1. community version
	2. Enterprise vesrion

Docker commands:

#docker run kodecloud/simple-webapp      >> it will pull the image from the docker hub from kokdecloud community >> post execution it runs on attached mode

#docker run -d kodecloud/simple-webapp   >> it will run in a dettached mode , it spins up the image in background 

#docker run -it imagename bash  >> to swtich on to the docker image with bash prompt

#docker ps 		>> only shows the running container 

#docker run imagename sleep 20 		>> here the image will run spinup and run for 20secs then will shutdown 

#docker ps -a 		>> will show all the containers including exited one too
 
#docker stop containerid_or_containername    >> to stop a container , check the exit code , if its 0 then automatically exited , if 137 then force close 

#docker rm containerid_or_containername     >> to delete it permanenetly 

#docker rm 123 456 789 			>> it will delete multiple containers at a strech 

#docker rmi 			>> to remove the images 

N:B- Docker container is a runnable sw or application , docker image is the template which used for the container spin up. 

#docker exec containerid cat /etc/os-release		>> to run the cat command inside a container 


Docker Run :

#docker run redis:4.0		>> to pull a image of this version of redis

#docker run -it imagename 		>> will start the terminal in interactive mode , i - interactive t - terminal

1.All docker contrainer will have a seprate ip, its an internal IP , only accessible from the Docker host.

2. If we need to access the internal container from the outside world. We need to assign an free IP of the host to the ip listening of the container. through below command

#docker run 80:5000 kodekloude/webapp 	>> 80 is the free ip on the docker host and 5000 is the container port

3. If we remove a database container , it will delete all the data present inside the container. So map a directory as a backup first for the same database

#docker run -v /opt/datadirectory:/var/lib/mysql mysql 		>> /opt/datadiractory is the external folder which is mapped to conatiner's /var/lib/mysql

4. To get more data about a container in json format we need below command

#docker inspect containername		>> it will give mount details , network config 

5. If a container is running in background , and we need the logs of threat container we can use below command

#docker logs containername 		>> give all details of a container running in background.


Advanced Docker Run :

1. If we want to reattach a container which is running in detached mode , use the below command.

#docker attach containerid

2. In order to map an IP and port we need to stop the same docker container. e.g below

#docker stop jenkins

#docker run -p 8080:8080 jenkins 	>> container IP 8080 will be mapped to docker host's 8080 port.

or full command in realtime 

#docker run -p 8080:8080 -v /root/jenkinsdata:/var/jenkins_home -u root jenkins		>> if you get permission issue , use -u root 


Docker Image :

1. Building an image through the docker file

#docker build . -f Dockerfile -t mumshad/my-custom-app 		>> go to the location where the file is present , so we can use (.) current directory , -t is to tag

#docker push mumshad/my-custom-app 		>> to push the image to docker hub on mumshad account with tag of my-custom-app

Docker file 

INSTRUCTION 		arguments

FROM 			Ubuntu		

RUN			apt get-update && apt-get -y install python python-pip

RUN			pip-install flask flask-mysql

COPY			. /opt/source-code

ENTRYPOINT		FLASK_APP=/opt/source-code/app.py flask run


N:B- each line from the docker file will create a layer and perform the task, e.g above docker file will create 5 layers, each layer will create a size of file next layer will only 
show the difference of size from the previous layer.


Docker environment variable:

To set an environment variable use the below command 

#docker run -e APP_COLOR = blue simple-webapp-color		>> -e is for environment 

#docker inspect simple-webapp-color 			>> check in the 2nd para for the environment variable set

#docker exec -it containername env


Docker command VS Entry Point

1. Container is not designed to host an OS or app , it runs an perticular task and exits (closed), until the next task

2. What task it run depends on the CMD variable in Dockerfile 

FROM		Ubuntu

ENTRYPOINT	["sleep"]

CMD 		["10"]			>> its sleeps for 10 secs & declaration always should be in json format 


3. To overide we need to use the below command.

#docker run --entrypoint sleeper2.0  containername 15


Docker Compose :

3 steps for docker compose
	1st. Install Docker compose
	2nd. Create compse file
	3rd. Run docker compse	

1. its in yml format

2. we can create a config file , and put together different services and track the changes 

3. Limitation is all docker containers should be in same docker host 

#docker-compose.yml

#docker-compose up

e:g -

services:
     Web:
	image: "mumshad/simple-webapp"
     database:
	image: "mongodb"
     messaging:
	image: "redis:alpine"
     orchestration:
	image: "ansible"


Docker Registry:

1. image: docker.io/nginx/nginx		>> first one is the registry,second one is useraccount & 3rd one is repository

2. To login to a private registry use the below command 

#docker login privateregistry.io	>> provide the registry username and password 

#docker run privateregistry.io/app/internal-app		>> if u didn't login then it will not run the container 

3. if you have a private registry in an onprime or as such , use the below command.

#docker image tag my-image localhost:5000/my-image   >> to tag the image 

#docker push localhost:5000/my-image 		>> to push to registry 

#docker pull 168.98.xx.xx:500/my-image 		>> to pull an image from remote registry with ip (168.98.xx.xx)


Docker Engine:

1.Docker engine is where you have installed docker , generally three parts installed in docker  i. Docker CLI ii. Rest API iii. Docker deamon

2. Docker CLI to communicate with Docker Deamon through Rest API

3. Docker CLI need not be present on the docker host , it could be remote too, to connect to REST API of a different machine use below.

# docker -H=remote-docker-engine:2375

e.g

#docker -H=10.123.2.1:2375 run nginx       >> docker daemon and REST API is in 10.123.2.1 on port 2375

4. Namespace , its a concept in which isolated environment present inside the docker host, procss running inside the namespace of a container is actually running on the docker host VM
   But while checking both the process ID will be different 

5. Cgroups or control group is a concept through which container will not end up taking all the resource or the docker host VM. we can below command to restrict the container

#docker run --cpu=0.5 ubuntu		>> to restrict the container from using more than .5 for the VM 

#docker run --memory=100m ubuntu 	>> to restrict the container from using more than 100mb of RAM


Docker Storage:

1. /var/lib/docker is the path where docker store its data such as aufs, containers, images , volumes

2. Layered arch of docker benefit -- if two docker file has 3 first line in common then last two lines are different , si while building the 2nd docker file it take the 
cache of the first docker build 3 lines , it saves time and also while changing the code , its always fast.

3.1 Image layer is a read layer in which it will read the lines layer by layer from dockerfile

3.2 Container layer is the layer created when the container is spinup through image , its read and write layer , in which data will be stored till the container is live 

4. Docker volume , all the volumes path is /var/lib/docker/volumes . to create and map a volume below is the command

#docker volume create data_volume	>> it will create a subfolder inside /var/lib/docker/volumes/data_volume

#docker run -v data_volume:/var/lib/mysql mysql  >> it will mount the data_volume to the /var/lib/mysql location of my sql container

#docker run - v /data/mysql:/var/libmysql mysql  >> if we mount the remote vol to a container >> its called bind mount 

5.some of the commonly used storage driver is 
AUFS
ZFS
BTRFS
Device Mapper
Overlay
Overlay2

docker will automatically choose the storage driver according to the best suit for the OS
 
5. to check more details about the docker installed on th VM use the below one 

#docker info | more 

6. To know how a image is been built , even if we don't have docker file use the below one.

#docker history containerid		>> it will show the docker file layers from bottom to top 

5. To get the app code check in /var/lib/docker/aufs_or_the_storage/diff/layercode_agwtejdnyehy997373hd7u3jnei39j/opt/

>> check for the lowest in size , generally thats the code used for app build



Docker Network:

1. Docker default network will have 3 components 
	i. Bridge	
	ii. None
	iii.Host

i. Bridge - e.g - #docker run ubuntu , its a private network (internal), generally starts with 172.17 series 
ii. None - #docker run ubuntu --network=none , they run in an isolated network
iii. Host - #docker run ubuntu --network=host , they run on host vm network upon one port.

2. We can create network of our own by below 

#docker network create --driver bridge xyz --subnet 182.18.0.0/16 custom-isolated-network


# docker network ls 		>> to list all network 

#docker inspect containername 		>> to get the details of the network 

3. Docker has an internal DNS to resolve the ip inside the docker

4. Reboot may change the IP of the container , so always specifiy the container name rather than IP

5. DNS Server IP = 127.0.0.11


Docker SWARM :

1.it is an orchestration tool from Docker, which will check and create the replicas of containers if required. generally it is practical approach if you rae using
more containers where manual orachestration is tidious. Autoscaling is done by this 

#docker service create --replicas=100 node.js

example of some orchestration tools - docker swarm, kubernetes (by google) , mesos (by apache)

2.Docker swarm uses one or multiple docker hosts. where one can be manager and others could be  the workers

to initiate the docker swarm for a manger use the below

#docker swarm init --advertise-addr 192.x.x.x

to join the worker to the above manager. the output of above command will give one command to run on worker nodes to setup the connections

#docker swarm join --token xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

3. If we need to push a container accross all the workers , run the below command on the docker manager node.

#docker service create --replicas=3 imagename

#docker service create --replicas=3 --network frontend imagename

#docker service create --replicas=3 -p 8080:80 imagename


Kubernetes:

1.Kubernetes can scale up or down the instances as per the load.

2.Nodes: These are the worker nodes , where the containers are lunched 

3. Cluster : its a set of nodes working together, if one nodes fails then the app will not fail.

	i. Master is the node in the Kubernetes cluster where the control pane is installed.  it will watch over to the working nodes

4. When we install Kubernetes on the any server we actually install the following components too.

i. API Server 	> its the frontend of the Kubernetes , all the command line UI interacts to Kubernetes cluster through this API server
ii. etcd	> its stores all the key value data for the nodes and master , if we have multiple master and nodes it will save the details to make sure no conflicts happen 
iii. Scheduler	> schedules which nodes to perform what.
iv. controller	> its a decision making component , to decide which nodes to bring back if one is down.
v. container runtime	> 
vi. Kubelet	> it ensure containers are running on the nodes as expected,
	
























 

