# docker-deploy
This project build a Docker container from scratch.  
It pushes the image to Amazon Elastic Container Registry(ECR) and Docker Hub. And deploys it to Amazon Elastic Container Container Service(ECS).  

## Reference
Source code: [noahgift/container-from-scratch-python](https://github.com/noahgift/container-from-scratch-python).  
Source code: [noahgift/container-revolution-devops-microservices](https://github.com/noahgift/container-revolution-devops-microservices).  
Youtube walkthrough: [Setup a Docker python project from scratch in AWS Cloud9](https://www.youtube.com/watch?v=WVifwRIwSmo).  


## How to use
To build a docker image and push it to registry, you can follow these steps:

### Set up a project
Launch AWS Cloud9, choose or create an environment.  
Create ssh-keys and upload it to Github.  ``ssh-keygen -t rsa``  
Create a new repo on github, git clone it to your AWS local and cd into it.  
Create all related files, including **Dockerfile, Makefile, requirements.txt, app.py**.  
(optional) Install hadolint.  
```
sudo wget -O /bin/hadolint https://github.com/hadolint/hadolint/releases/download/v1.19.0/hadolint-Linux-x86_64
sudo chmod +x /bin/hadolint
```
(optional) Test hadolint after you call "make install" and make the app run locally.  ```make lint```  

### Run this app locally
Create a virtual environment and activate it. (To deactivate it, run "deactivate") 
```
python3 -m venv ~/.dockerproj
source ~/.dockerproj/bin/activate
```
Install the required packages. ```make install```  
Run this app. ```python app.py --name yy258```  

### Build an image and run a container
We will pull down an official base image, package up our own software and runtime on top of it.  
Build an image accoridng to the setup in Dockerfile, name it "my-app", tag it "v1".  
```
docker build -t my-app:v1 .
```
View the newly created image by running ```docker images```.  

**Run docker and shell into it**. The shell will be like ```root@3f63ef850cdd:/app#```  
```
docker run -it my-app:v1 bash 
```
Then we can test this app inside docker. To exit docker, run "exit".  
```
python app.py --name yyyy
exit
```

### Push to Docker Hub
Create a new repository "dockerproj" on DockerHub.  
Login to DockerHub in your AWS local.  
```
docker login
```
Tag your image with hostname and self defined ```tag-name```.  
```
docker tag my-app:v1 yueyang0115/dockerproj:[tag-name]
```
Push it to Docker Hub.
```
docker push yueyang0115/dockerproj:[tag-name]
```

### Pull from Docker Hub
```
docker pull yueyang0115/[repo-name]:[tag-name]
```

### Push to Amazon ECR
Open Amazon ECR console.  
Create a repository, call it "dockerproj" and enable "Scan on push".  
Click on the newly created repo, and click on **"view push commands"**. It will show commands like below:  
First authenticate.  
Then build docker image.  
Then tag the image.  
Finally push this image to AWS repository.  
The command will be like:  
```
aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 075300343026.dkr.ecr.us-east-2.amazonaws.com
docker build -t dockerproj .
docker tag dockerproj:latest 075300343026.dkr.ecr.us-east-2.amazonaws.com/dockerproj:latest
docker push 075300343026.dkr.ecr.us-east-2.amazonaws.com/dockerproj:latest
```

### Pull from Amazon ECR
Open another Cloud9 environment.  
Authenticate first by using the same suthentication code from ECR console.  
Then pull down this image from ECR.   
**Run docker in this new environment**.  
It will look like this:  
```
aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 075300343026.dkr.ecr.us-east-2.amazonaws.com
docker pull 075300343026.dkr.ecr.us-east-2.amazonaws.com/dockerproj
docker run -it 075300343026.dkr.ecr.us-east-2.amazonaws.com/dockerproj python app.py --name yy
```

### Deploy to Amazon ECS fargate
Make sure you have pushed your image to Amazon ECR.  
Open ECS console and click on "get started".  
For "Container definition", choose to configure your custom container image. Name your container, copy your image url provided in ECR console, set up memory limit and port mapping.  
For "Define your service", set up a load balancer for your service.  
For "Configure your cluster", name your cluster.  
Review your setting and your container will begin running.  
You can find your task under "Cluster" in ECS console. You can find the public ip in the detailed information of your task. If you deployed a website, you can access it using this public ip.  

### Done!  
