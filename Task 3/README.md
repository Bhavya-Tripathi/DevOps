# Task 3
*Report for both assignments present in the pdf*

## Assignment 1
#### Setting up CI/CD Jenkins pipeline for Kubernetes (For a NodeJs Application)

## Prerequisites 
- NodeJS v8+
- Docker installed locally: [See how to install docker on Ubuntu](https://www.linuxtechi.com/install-use-docker-on-ubuntu/)
- Minikube: [See how to install minikube on Ubuntu](https://www.linuxtechi.com/how-to-install-minikube-on-ubuntu/)

## Step 1
1. Create a folder and initialize a package.json file by running the `npm init` command. Then install the express module by running `npm i express --save`.
2. Then create a file called server.js, like so: [source code for server.js file](https://github.com/Bhavya-Tripathi/Devops-Jenkins/blob/master/server.js)
3. Running the command `node server.js` should give the output in the browser at localhost:PORT
## Step 2
1. Create a Dockerfile and put the following code in it to build a docker image. [Source Code](https://github.com/Bhavya-Tripathi/Devops-Jenkins/blob/master/Dockerfile)

```
FROM node:15
WORKDIR /home/bhavya/Desktop
COPY package*.json ./ 
RUN npm install
COPY . . 
EXPOSE 8000
CMD [ "node", "server.js" ]
```
Where, 15 is the version of Node, WORKDIR tells the working directory of the image, RUN executes the command defined above. EXPOSE informs the container that it needs to open port 8000. CMD stands for command.  
2. Create a .dockerignore file and put `node_modules` in it.    
3. Build a Docker image. Run the following command in the project directory.
`docker build -t <docker username>/nodejs-app .`
  ![Docker Build](https://github.com/Bhavya-Tripathi/DevOps/blob/main/Task%203/images/A1-docker-build.png)
Now check the image using `docker images` command  
4. Run the image. Running the image with `-d` flag leaves the container running in the background and the `-p` flag redirects a public port to a private port in the container.
`docker run -p 3000:8000 -d <docker username>/nodejs-app`
![Docker run](https://github.com/Bhavya-Tripathi/DevOps/blob/main/Task%203/images/A1-docker-run.png)
To check if it is running, go to the browser and type URl : `<docker_ip_address:8000>` and you should see the output.  [Docker output](https://github.com/Bhavya-Tripathi/DevOps/blob/main/Task%203/images/A1-docker-op.png)
5. Login to docker hub through the command line and push to the docker hub registry.
`docker push <username>/nodejs-app`
![docker Push](https://github.com/Bhavya-Tripathi/DevOps/blob/main/Task%203/images/A1-docker-push.png)
This pushes to the registry and attaches the latest tag by default.
## Step 3
1. Create a deployment file called deployment.yaml and add the following code in it. [Source Code](https://github.com/Bhavya-Tripathi/Devops-Jenkins/blob/master/deployment.yaml)
2. Start minikube ![minikube start](https://github.com/Bhavya-Tripathi/DevOps/blob/main/Task%203/images/A1-minikube-start.png)
3. Then apply the yaml file using kubectl command.
`kubectl apply -f deployment.yaml`
The output is:
![Deployment](https://github.com/Bhavya-Tripathi/DevOps/blob/main/Task%203/images/A1-apply-deployment.png)
This creates a pod with one replica and a service file.
To see the output of the pod run command `minikube service nodejs-service`
[Minikube service](https://github.com/Bhavya-Tripathi/DevOps/blob/main/Task%203/images/A1-minikube-service.png)  
4. To see the pod has been created go the minikube dashboard using command `minikube dashboard`
![Minikube dashboard](https://github.com/Bhavya-Tripathi/DevOps/blob/main/Task%203/images/A1-minikube-dashboard.png)
## Step 4
Push all the code into a git repository.
## Step 5
1. Install [Jenkins](https://www.jenkins.io/download/)
Run the jenkins file, the first time it asks for an admin password.
Run `cat /var/lib/jenkins/secrets/initialAdminPassword` and copy the password and paste it.
Next install the suggested plugins
After installing the dashboard opens up 
Go to Manage Jenkins -> Manage Plugins -> Available and install plugins for Nodejs, Docker, Kubernetes, Github, Kubernetes CLI.
2. Configure Dockerhub and github credentials. Go to Manage Jenkins -> Manage credentials and add a credential for docker hub with your username and password. Create a credential id (which will be used later) and description. ![Credentials](https://github.com/Bhavya-Tripathi/DevOps/blob/main/Task%203/images/A1-dockerhub-credentials.png)Similarly add github credentials, i.e. username and Personal Access Token.
3. Run the following command to create an environment variable named KUBECONFIG and provide the .kube/config path. Jenkins goes to this path to execute kubectl commands.
`export KUBECONFIG=~/.kube/config`
## Step 6
1. Create a jenkins pipeline. Add this code in a file named Jenkinsfile. [Source Code](https://github.com/Bhavya-Tripathi/Devops-Jenkins/blob/master/Jenkinsfile) Go to the `~/ .kube` directory and type the command `cat config` to get all the information about the cluster to fill inside the withKubeConfig method in the pipeline. ![cluster details](https://github.com/Bhavya-Tripathi/DevOps/blob/main/Task%203/images/A1-Cluster-details.png)
2. Push this file into the github repository.
3. Now create a pipeline project in Jenkins. Click on New Item in the dashboard. Put a name of your choice, choose Pipeline and save it.
4. Go to Build Triggers section and choose Github hook trigger for GITScm polling.
5. In Pipeline phase choose Pipeline script from SCM and choose the SCM as Git and provide the URL to your github repository. Save this file and check the process by clicking on Build Now.
6. If you click on the Full Stage View option, it should look like this: ![Full Stage View](https://github.com/Bhavya-Tripathi/DevOps/blob/main/Task%203/images/A1-First-Stage-View.png) 
7. Opening the public_kubernetes_url:PORT in the browser will give us the output now. ![Browser output](https://github.com/Bhavya-Tripathi/DevOps/blob/main/Task%203/images/A1-Browser-op.png)
8. Since we want to make this pipeline automatically build whenever a commit is made in our repository, we have to use github webhooks.
9. [Ngrok](https://ngrok.com/download) exposes local servers behind NAts and firewalls to the public internet over secure tunnels.
10. Run the `ngrok http 8080` command. This command exposes the 8080 port (jenkins) over the public internet and gives us a URL for 8 hours.![Ngrok](https://github.com/Bhavya-Tripathi/DevOps/blob/main/Task%203/images/A1-ngrok.png)
11. Copy this URL into github webhooks (Settings -> Webhooks -> Add Webhook) and paste it in the URL section. Append /github-webhook/ to the URL.
12. Click on add webhook. Now if any change is made that should trigger the event and build the pipeline. 
![Build-triggered](https://github.com/Bhavya-Tripathi/DevOps/blob/main/Task%203/images/A1-Triggered-build.png) Build #26 is triggered as a new commit is made.

`
