# docker-deploy

## How to use
To deploy docker on AWS and set up continuous deployment, you can follow these steps:

### Set up a project
Launch AWS Cloud9.  
Create ssh-keys and upload it to Github.  
```
ssh-keygen -t rsa
```
Create a new repo on github, git clone it to your AWS local and cd into it.
Create all related files, including **Dockerfile, Makefile, requirements.txt, app.py**.  
(optional) Install hadolint.  
```
sudo wget -O /bin/hadolint https://github.com/hadolint/hadolint/releases/download/v1.19.0/hadolint-Linux-x86_64
sudo chmod +x /bin/hadolint
```
(optional) Test hadolint after you call "make install" and make the app run locally.  
```
make lint
```

### Run this app locally
Create a virtual environment and activate it. (To deactivate it, run "deactivate") 
```
python3 -m venv ~/.dockerproj
source ~/.dockerproj/bin/activate
```
Install the required packages.
```
make install
```
Run this app.  
```
chmod +x app.py
python app.py --name yy258
```

### Build a container
We will pull down an official base container, package up our own software and runtime on top of it.  
Build container accoridng to the setup in Dockerfile, name this container "app".  
```
docker build --tag=app .
```
List all docker images and find the newly created one. (To delete an image, run "docker image rm IMAGE_NAME")  
```
docker image ls
```
Run docker and shell into it. The shell will be like root@3f63ef850cdd:/app#  
```
docker run -it app bash 
```
Then we can test this app inside docker. To exit docker, run "exit".  
```
python app.py --name yyyy
exit
```

### deploy this container to the AWS Container Registry




### Set up continuous deployment


### Done! See pipeline under Cloud Build
Now if you make a change to your code on github, this app will be redeployed automatically. 
You can see the new build pipeline under Cloud Build.  