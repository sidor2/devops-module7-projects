<details>
<summary>Project1:  Use Docker for local development</summary>

### Technologies used:
- Docker
- Node.js
- MongoDB
- MongoExpress

Project Description:

- Create Dockerfile for Nodejs application and build Docker image
- Run Nodejs application in Docker container and connect to MongoDB database container locally.
- AlsorunMongoExpress container as a Ul of the MongoDB database.

### With Docker

#### To start the application

Step 1: Create docker network
```shell
docker network create mongo-network
```
Step 2: start mongodb
```shell
docker run -d -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=password --name mongodb --net mongo-network mongo
```
Step 3: start mongo-express
```shell
docker run -d -p 8081:8081 -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin -e ME_CONFIG_MONGODB_ADMINPASSWORD=password --net mongo-network --name mongo-express -e ME_CONFIG_MONGODB_SERVER=mongodb mongo-express
```
_NOTE: creating docker-network in optional. You can start both containers in a default network. In this case, just emit `--net` flag in `docker run` command_

Step 4: open mongo-express from browser

    http://localhost:8081

Step 5: create `user-account` _db_ and `users` _collection_ in mongo-express

Step 6: Start your nodejs application locally - go to `app` directory of project

    cd app
    npm install 
    node server.js

Step 7: Access you nodejs application UI from browser

    http://localhost:3000

</details>

<details>
<summary>Project2: Docker Compose - Run multiple Docker containers</summary>

Technologies used:
- Docker
- MongoDB
- MongoExpress

Project Description:
- Write Docker Compose file to run MongoDB and MongoExpress containers

### With Docker Compose
#### To start the application

Step 1: start mongodb and mongo-express

    docker-compose -f docker-compose.yaml up

_You can access the mongo-express under localhost:8080 from your browser_

Step 2: in mongo-express UI - create a new database "user-account"

Step 3: in mongo-express UI - create a new collection "users" in the database "user-account"

Step 4: start node server

    cd app
    npm install
    node server.js

Step 5: access the nodejs application from browser

    http://localhost:3000

#### To build a docker image from the application

    docker build -t my-app:1.0 .       

The dot "." at the end of the command denotes location of the Dockerfile.
</details>

<details>
<summary>Project3: Dockerize Nodejs application and push to private Docker registry</summary>

Technologies used:
- Docker
- Nodejs
- Amazon ECR

Project Requirements:
- Write Dockerfile to build a Docker image for a Nodejs application
- Create private Docker registry on AWS (Amazon ECR)
- Push Docker image to this private repository

Step 1: create a repository in AWS ECR.

Step 2: follow the push steps from AWS ECR:

Login:

    aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <account>.dkr.ecr.us-east-1.amazonaws.com

Build:

    docker build -t devops-module7-projects .

Tag:

    docker tag devops-module7-projects:latest <account>.dkr.ecr.us-east-1.amazonaws.com/devops-module7-projects:latest

Push:

    docker push <account>.dkr.ecr.us-east-1.amazonaws.com/devops-module7-projects:latest

</details>
<details>
<summary>Project4: Deploy Docker application on a server with DockerCompose</summary>

Technologies used:
- Docker
- Amazon ECR
- Node.js
- MongoDB
- MongoExpress

Project Description:
- Copy Docker-compose file to remote server
- Login to private Docker registry on remote server to fetch our app image
- Start our application container with MongoDB and MongoExpress services using docker compose

### login into the private repo
```shell
aws ecr get-login-password --region us-east-1 --profile <aws-creds-profile> | docker login --username AWS --password-stdin <account>.dkr.ecr.us-east-1.amazonaws.com
```

### add in docker-compose.yaml
```
   my-app:
     image: <account>.dkr.ecr.us-east-1.amazonaws.com/devops-module7-projects:1.0
     ports:
      - 3000:3000
```

### update the server.js file
```
// use when starting application as docker container, part of docker-compose
let mongoUrlDockerCompose = "mongodb://admin:password@mongodb";
    
// pass these options to mongo client connect request to avoid DeprecationWarning for current Server Discovery and Monitoring engine
let mongoClientOptions = { useNewUrlParser: true, useUnifiedTopology: true };
```

### build the image
```shell
docker build -t devops-module7-projects .
```

### tag the image
```shell
docker tag devops-module7-projects:latest <account>.dkr.ecr.us-east-1.amazonaws.com/devops-module7-projects:1.1
```
### push to repo
```shell
docker push 253021321210.dkr.ecr.us-east-1.amazonaws.com/devops-module7-projects:1.1
```

### run docker-compose
```shell
docker-compose -f docker-compose.yaml up -d
```
### Access you nodejs application UI from browser

    http://localhost:3000

</details>

<details>
<summary>Project5: Persist data with Docker Volumes</summary>

Technologies used:
- Docker
- Node.js
- MongoDB

Project Description:
- Persist data of a MongoDB container by attaching a Docker volume to it

### inside docker-compose.yaml
```
services:
...
   mongodb:
    image: mongo
    ports:
     - 27017:27017
    environment:
     - MONGO_INITDB_ROOT_USERNAME=admin
     - MONGO_INITDB_ROOT_PASSWORD=password
    volumes:
     - mongo-data:/data/db
...
volumes:
  mongo-data:
    driver: local
```

To access the shell of the Docker VM in order to view volume information, use this command:

    docker run -it --privileged --pid=host debian nsenter -t 1 -m -u -n -i sh

</details>

<details>
<summary>Project6: Create Docker repository on Nexus and push to it</summary>

Technologies used:
- Docker
- Nexus
- DigitalOcean
- Linux

Project Description:
* Create Docker hosted repository on Nexus
* Create Docker repository role on Nexus
* Configure Nexus, DigitalOcean Droplet and Docker to be able to push to Docker repository
* Build and Push Docker image to Docker repository on Nexus

### in Nexus repository add port for Docker
!["add Docker port"](./2023-10-15_12-46.png)

### in Droplet open the port in FW

### configure Realm in Nexus
!["add realm"](./2023-10-15_12-56.png)

### add in Docker Engine
```
"insecure-registries": ["143.110.158.5:8083"],
```

### Docker
```shell
docker login 143.110.158.5:8083

docker build app-for-nexus:1.0 .

docker tag app-for-nexus:1.0 143.110.158.5:8083/app-for-nexus:1.0

docker push 143.110.158.5:8083/app-for-nexus:1.0
```

</details>

<details>
<summary>Project7: Deploy Nexus as Docker container</summary>

Technologies used:
- Docker
- Nexus
- DigitalOcean
- Linux

Project Description
- Create and Configure Droplet
- Setup and run Nexus as a Docker container

### follow steps
```
https://hub.docker.com/r/sonatype/nexus3
```

### in Droplet
```shell
ls /var/snap/docker/common/var-lib-docker/volumes/nexus-data/_data/
```

</details>