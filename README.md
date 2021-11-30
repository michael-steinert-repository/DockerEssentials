# Docker

* Docker is a Platform for building, running and shipping Applications
* It makes it easily to build and deploy Applications running in Containers
* A Container is a running Instance of an Application
* An Application is packed up in a Container so the local Development is the same across any Environment

## Docker Components
* Docker is using the following Features from the Unix Kernel:
* __cgroups__: 
  * cgroups restricts and isolates Resource Usage from a Collection of Processes
  * cgroups allow Quotas to be defined at the Process Level
  * Restrictions are placed on the Resource Usage of the Quotas
* __Namespaces__:
  * Namespaces enables independent Process Management
* The Combination of __cgroups__ and __Namespaces__ allows to isolate a Process and limit its Resources without having the Overhead of a Virtual Machine (VM)

## Container vs. Virtual Machine

### Virtual Machine

* Virtual Machines are an Abstraction of physical Hardware turning one Server into many Servers
* The Hypervisor allows multiple Virtual Machines to run on a single Machine
* Each Virtual Machine includes a full Copy of an Operating System, the Application, necessary Binaries and Libraries

<p align="center">
    <img src="https://user-images.githubusercontent.com/29623199/133385406-3a51903c-52ae-4f2a-856a-413d4eab9923.JPG" alt="VMs Overview" width="50%"/>
</P>

### Container

* Containers are an Abstraction at the Application Layer that packages Code and Dependencies together
* Multiple Containers can run on the same Machine and share the Operating System Kernel
* A Container is an isolated Environment for running Application
* Each Container runs as isolated Processes in the User Space
* A Container contains everything an Application needs (for Example the Operating System and Configurations)

<p align="center">
    <img src="https://user-images.githubusercontent.com/29623199/133127358-9b10c4d4-a74a-4630-9730-b400ad8e3af9.JPG" alt="Containers Overview" width="50%"/>
</P>

## Image

* An Image is a File used to execute Code in a Container
* It contains a Set of Instructions to build a Container
* It contains the Application Code, Libraries, Tools and everything needed to run a Application

### Image Layer

* Layers are used to speed up to pull Images

### Distroless Docker Images

* Distroless Images contain only the Application and its Runtime Dependencies
* They do not contain Package Managers, Shells or any other programs that are to find in a standard Linux Distribution

## Docker Volumes

* Volumes allow Data to be shared between Containers (and Container) and Host
* With Volumes can Data be kept after Containers stopped running
* __bind mount__: A Bind Mount allows the Host to share it own File System with the
  Container ```-v host-path:container:path```
* __volume__: Docker Volume is a Volume which is managed by Docker
* __tmpfs mount__: A Value that is mounted to the RAM

<p align="center">
    <img src="https://user-images.githubusercontent.com/29623199/133401039-0a8f9fee-fb26-4daa-b49a-f0b02e2ae707.JPG" alt="Volumes Overview" width="50%"/>
</P>

## Docker Architecture

* The Docker Architecture follows the Client-Server Approach
* The Client is the used CLI (Command Line Interface - Terminal) and Server is the Docker Host (Hosting System of Docker)
* The Docker Daemon on the Server is responsible to handle Commands from the Client
* The Docker Daemon fetches Images from local or remote Registries, and stores it on the Server (Hosting System of
  Docker)
* The Docker Daemon runs Containers of downloaded or created Images on the Hosting System of Docker

* The Client communicates with the Docker Daemon via the Unix Socket (/var/run/docker.sock)
* A Unix Socket is an inter-process Communication Mechanism that allows bidirectional Data Exchange between Processes
  running on the same Machine

<p align="center">
    <img src="https://user-images.githubusercontent.com/29623199/133390369-07921d96-69bc-47fd-8909-3f9351d10069.JPG" alt="Docker Architecture" width="50%"/>
</P>

## Docker Registries

* Docker Registries are a Storage and Distribution System for Docker Images
* Images can be pulled or pushed into Docker Registries
* Docker Registries can be public or private:
    * Public Registries are available for everyone
    * Private Registries are only available for the Owners

<p align="center">
    <img src="https://user-images.githubusercontent.com/29623199/133730387-a9315406-0915-42f7-b825-ba06d63ee412.JPG" alt="Docker Registry" width="50%"/>
</P>

## Docker Network

* A Docker Network allows communicating Containers between each other
* First a Docker Network it created then it is attached to the Containers
* In Docker Networks, the Container Name corresponds to the Host Name

<p align="center">
    <img src="https://user-images.githubusercontent.com/29623199/133881078-e5b287e6-f7fe-4531-903a-ca015d7564c4.JPG" alt="Docker Registry" width="50%"/>
</P>

```docker
docker network create my-network
docker network inspect my-network
docker run --name mongo-db -d --network my-network -p 27017:27017 \
    -e MONGO_INITDB_ROOT_USERNAME=username \
    -e MONGO_INITDB_ROOT_PASSWORD=password \
    mongo:4.2
docker run --name mongo-express --network my-network -d -p 8081:8081 \
    -e ME_CONFIG_MONGODB_ADMINUSERNAME=username \
    -e ME_CONFIG_MONGODB_ADMINPASSWORD=password \
    -e ME_CONFIG_MONGODB_SERVER=mongo-db \
    mongo-express:0.54.0
docker logs mongo-express
docker network connect my-network mongo-express
docker network disconnect my-network mongo-express
docker network rm my-network
```

### Network Drivers

* Bridge(default): Bridge Networks are used when Applications run in standalone Containers that need to communicate
* Host: For standalone Containers, remove Network Isolation between the Container and the Docker Host, and use the
  Host’s Networking directly
* Overlay: Overlay Networks connect multiple Docker Daemons together and enable Swarm Services to communicate with each
  other
* Macvlan: Macvlan Networks allow to assign a MAC address to a Container, making it appear as a physical Device on the
  Network
* None: For this Container, disable all Networking

## Docker Commands

| Command                                                                           | Description                                                                                                                                                                                    |
| --------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| docker run IMAGE                                                                  | Starts or creates a new Container from an Image                                                                                                                                                |
| docker start ID                                                                   | Starts a given Container with an ID                                                                                                                                                            |
| docker stop ID                                                                    | Stops a given Container with an ID                                                                                                                                                             |
| docker rm ID                                                                      | Removes a stopped Container with an ID                                                                                                                                                         |
| docker rm -f ID                                                                   | Removes a running Container with an ID                                                                                                                                                         |
|                                                                                   |                                                                                                                                                                                                |
| docker logs ID                                                                    | Shows Logs of the given Container                                                                                                                                                              |
| docker logs ID -f                                                                 | Shows dynamically Logs of the given Container                                                                                                                                                  |
| docker exec ID env                                                                | Gets the Environment Variables of the Container                                                                                                                                                |
| docker exec ID ls                                                                 | Lists Files from Working Directory of the Container                                                                                                                                            |
|                                                                                   |                                                                                                                                                                                                |
| docker run --name name IMAGE                                                      | Names the Container with the given Name "name"                                                                                                                                                 |
| docker run -p 81:80 IMAGE                                                         | Exposes the Port on the Host to Port 80 inside the Cotnainer                                                                                                                                   |
| docker run -d IMAGE                                                               | Runs the Container in Background and prints its ID                                                                                                                                             |
|                                                                                   |                                                                                                                                                                                                |
| docker image ls                                                                   | Show all downloaded or created Images                                                                                                                                                          |
| docker image rm IMAGE                                                             | Deletes downloaded or created Image with Image Name                                                                                                                                            |
| docker pull IMAGE                                                                 | Pulls an Image from the Repository                                                                                                                                                             |
| docker image inspect IMAGE                                                        | Inspects an downloaded or created Image                                                                                                                                                        |
|                                                                                   |                                                                                                                                                                                                |
| docker exec -it ID sh                                                             | Runs the Default Shell in a Container in interactive Mode (Allows to execute Commands to Container from the Terminal)                                                                          |
| docker run -v $PWD:/tmp bash bash -c "echo foo > tmp/bar.txt && cat /tmp/bar.txt" | Creates a Volume in the Working Directory (PWD - Print Working Directory), then creates a File "bar.txt" with Input "foo" in the mounted Directory, after prints the Input of the created File |
|                                                                                   |                                                                                                                                                                                                |
| docker volume ls                                                                  | Lists all Volumes that are managed by Docker                                                                                                                                                   |
| docker volume create vol1                                                         | Creates a Volume that is managed by Docker with Name "vol1"                                                                                                                                    |
| docker volume inspect vol1                                                        | Inspect a Volume that is managed by Docker with Name "vol1"                                                                                                                                    |
| docker run -v vol1:/tmp bash                                                      | Creates a Container that is using the Volume "vol1" which is managed by Docker                                                                                                                 |
| docker volume rm vol1                                                             | Deletes unused Volume with Name "vol1" which si managed by Docker                                                                                                                              |
|                                                                                   |                                                                                                                                                                                                |
| docker tag bash:latest bash:1                                                     | Creates a new Image from Image "bash:latest" with Tag "1"                                                                                                                                      |
| docker run bash-v1 -d bash:1                                                      | Runs a Container from the Image with Tag "1"                                                                                                                                                   |

## Dockerfile

* A Dockerfile is a Set of Commands used to assemble a (Docker) Image

```docker
FROM nginx
COPY . /tmp
```

* Setting the Base Image "nginx" and copy all Files from the Source (current Directory) inside the Container "/tmp"

```docker
docker build . -t customImage
docker run --name customName -d customImage
```

* Builds a Docker Image from the given Dockerfile - The Build Command needs a Directory where a Dockerfile exists as Input
* Runs a Container from a given Image 

|Command|Description| 
|---|---| 
|docker run -w /src -v $PWD:/src --rm node npm init --yes| Creates a Container with the Working Directory "/src" and mount the current Files from the Host inside the Container - Also execute the Command "npm init --yes" after the Container with Image "node" have started - After stopping the Container it will be automatically removed| 
|docker run -w /src -v $PWD:/src --rm node npm install --save express|Runs the Container, and install the NPM Package "express"|

* Same Commands like above as a Dockerfile:

```docker
FROM node
WORKDIR /src
ADD . .
RUN npm install
CMD["node", "server.js"]
```

```docker
docker build . -t express-image
docker run --name express-image -p 3000:3000 -d express-image
```

* __ADD__: Adds all Files from current Host Folder to the Working Directory
* __RUN__: Executes the initial Command "npm install" with the given package.json (Package.json was copied by the
  Command "ADD")
* __CMD__: Executes the Command "node server.js" to Start the Express Server (Dependencies were installed by initial
  Command "npm install")

## Docker Compose

* Docker Compose is a Tool defining and running Multi-Container Docker Applications using a YAML File

```yaml
services:
  mongo:
    image: mongo:5.0.1
    container_name: mongo-db
    ports:
      - 27017:27017
    environment:
      - MONGO_INITDB_ROOT_USERNAME=username
      - MONGO_INITDB_ROOT_PASSWORD=password
    networks:
      - my-network
    volumes:
      - data:/data
  mongo-express:
    image: mongo-express:0.54.0
    container_name: mongo-express
    ports:
      - 8081:8081
    environment:
      - ME_CONFIG_MONGODB_ADMINUSERNAME=username
      - ME_CONFIG_MONGODB_ADMINPASSWORD=password
      - ME_CONFIG_MONGODB_SERVER=mongo-db
    depends_on:
      - mongo-db
    restart: always
    networks:
      - my-network

networks:
  mongo:
    name: my-network

volumes:
  data: { }
```

* Working with Docker Compose:

```docker
docker compose -f docker-compose.yaml up -d
docker volume ls
docker compose ps
docker compose ls
docker compose stop
docker compose start
docker compose down
```

## Docker Scan

* Docker Scan is a Tool to scan Images for Vulnerabilities
* It is using the Tool Snyk which find and fix Security Vulnerabilities

```docker
docker scan node
```