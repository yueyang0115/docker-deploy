# docker-deploy
This project build a Docker container from scratch.  
It pushes the container to Amazon Elastic Container Registry(ECR) and DockerHub.  

## Reference
Source code: [noahgift/container-from-scratch-python](https://github.com/noahgift/container-from-scratch-python).  
Source code: [noahgift/container-revolution-devops-microservices](https://github.com/noahgift/container-revolution-devops-microservices).  
Youtube walkthrough: [Setup a Docker python project from scratch in AWS Cloud9](https://www.youtube.com/watch?v=WVifwRIwSmo).  


## How to use
To build a docker container and push it, you can follow these steps:

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

### Build a container
We will pull down an official base container, package up our own software and runtime on top of it.  
Build container accoridng to the setup in Dockerfile, name this container "app".  
```
docker build --tag=app .
```
List all docker images and find the newly created one. 
```
docker image ls
```
To delete a docker image, use ```docker image rm IMAGE_NAME```  
If the container is running, use ```docker rmi -f IMAGE_NAME``` to force the deletion.  

**Run docker and shell into it**. The shell will be like ```root@3f63ef850cdd:/app#```  
```
docker run -it app bash 
```
Then we can test this app inside docker. To exit docker, run "exit".  
```
python app.py --name yyyy
exit
```

### Push this docker image to Amazon ECR
Open Amazon ECR console.  
Create a repository, call it "dockerproj" and enable "Scan on push".  
Click on the newly created repo, and click on **"view push commands"**. It will show commands like below:  
First authenticate. The command will be like:  
```
aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 075300343026.dkr.ecr.us-east-2.amazonaws.com
```
Then build docker image. The command will be like:  
```
docker build -t dockerproj .
```
Then tag the image. The command will be like:  
```
docker tag dockerproj:latest 075300343026.dkr.ecr.us-east-2.amazonaws.com/dockerproj:latest
```
Finally push this image to AWS repository. The command will be like:  
```
docker push 075300343026.dkr.ecr.us-east-2.amazonaws.com/dockerproj:latest
```

### Pull from Amazon ECR
Open another Cloud9 environment.  
Authenticate first by using the same suthentication code from ECR console. It will look like this:  
```
aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 075300343026.dkr.ecr.us-east-2.amazonaws.com
```
Then pull down this image from ECR.  
```
docker pull 075300343026.dkr.ecr.us-east-2.amazonaws.com/dockerproj
```
**Run docker in this new environment**.  
```
docker run -it 075300343026.dkr.ecr.us-east-2.amazonaws.com/dockerproj python app.py --name yy
```

### Push this docker image to DockerHub
Create a new repository on DockerHub.  
Login to DockerHub in your AWS local.  
```
docker login --username=yourhubusername
```
Check your image using ```docker image ls```.  
Tag your image.  


### Pull frrom DockerHub

### Done!  
