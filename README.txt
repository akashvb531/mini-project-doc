

## 🧠 Application Overview

A basic HTTP server created using *Node.js* that returns a simple plain text message:
`Hello from Brain Tasks App! `

---

## 📂 Repository Cloning

Clone the React/Node.js application using the following command:

bash
git clone https://github.com/Vennilavan12/Brain-Tasks-App.git
cd Brain-Tasks-App


---

## 🚀 Application Setup

### Run Locally

bash
node server.js


Navigate to:
http://localhost:3000
You should see:
`Hello from Brain Tasks App! `

---

## 🐳 Dockerize the Application

### Dockerfile

Dockerfile
FROM node:14

WORKDIR /app

COPY . .

EXPOSE 3000

CMD ["node", "server.js"]


### Build & Run Docker Image

bash
docker build -t brain-tasks-app .
docker run -p 3000:3000 brain-tasks-app


---

## ☁ AWS ECR Setup

1. *Create ECR Repository*

bash
aws ecr create-repository --repository-name brain-tasks-app


2. *Authenticate & Push Image*

bash
aws ecr get-login-password | docker login --username AWS --password-stdin <your_account_id>.dkr.ecr.<region>.amazonaws.com

docker tag brain-tasks-app:latest <your_account_id>.dkr.ecr.<region>.amazonaws.com/brain-tasks-app

docker push <your_account_id>.dkr.ecr.<region>.amazonaws.com/brain-tasks-app


---

## ☸ Kubernetes (EKS)

### Setup EKS Cluster

* Use AWS Console or CLI to create an EKS cluster and node group.

### Deployment YAML

yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: brain-tasks-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: brain-tasks
  template:
    metadata:
      labels:
        app: brain-tasks
    spec:
      containers:
      - name: brain-tasks
        image: <your_ecr_image_url>
        ports:
        - containerPort: 3000


### Service YAML

yaml
apiVersion: v1
kind: Service
metadata:
  name: brain-tasks-service
spec:
  type: LoadBalancer
  selector:
    app: brain-tasks
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000


### Deploy to EKS

bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml


✅ Application Deployed At:
*[http://a0d42432a62a64bceaa8d7d9702b85e4-768409516.ap-south-1.elb.amazonaws.com](http://a0d42432a62a64bceaa8d7d9702b85e4-768409516.ap-south-1.elb.amazonaws.com)*

---

## 🔧 AWS CodeBuild

### buildspec.yml

yaml
version: 0.2

phases:
  install:
    runtime-versions:
      nodejs: 14
  pre_build:
    commands:
      - echo Logging in to Amazon ECR...
      - aws ecr get-login-password | docker login --username AWS --password-stdin <your_account_id>.dkr.ecr.<region>.amazonaws.com
  build:
    commands:
      - echo Building the Docker image...
      - docker build -t brain-tasks-app .
      - docker tag brain-tasks-app:latest <your_account_id>.dkr.ecr.<region>.amazonaws.com/brain-tasks-app
  post_build:
    commands:
      - echo Pushing the Docker image...
      - docker push <your_account_id>.dkr.ecr.<region>.amazonaws.com/brain-tasks-app

artifacts:
  files: '**/*'


---

## 🚀 AWS CodeDeploy + AppSpec

### appspec.yml

yaml
version: 0.0
Resources:
  - myAppEKS:
      Type: AWS::EKS::Cluster
      Properties:
        Name: <your-cluster-name>
        Namespace: default
        DeploymentGroupName: <your-deployment-group-name>


---

## 🔄 CodePipeline Setup

* *Source*: GitHub (your repository)
* *Build*: AWS CodeBuild
* *Deploy*: AWS CodeDeploy (or Lambda to apply Kubernetes manifests)

---

## 📈 Monitoring

Use *CloudWatch Logs* to monitor:

* CodeBuild Logs
* Application logs (via container logs)
* CodeDeploy status

---

## 📝 Screenshots

✅ Docker Image Build
✅ ECR Push
✅ Kubernetes Deployment
✅ CodeBuild and CodeDeploy success
✅ Application Running via LoadBalancer



---

## ✅ Final Deployment URL

Your application is accessible at:

*[http://a0d42432a62a64bceaa8d7d9702b85e4-768409516.ap-south-1.elb.amazonaws.com](http://a0d42432a62a64bceaa8d7d9702b85e4-768409516.ap-south-1.elb.amazonaws.com)*

---

## 🔁 Version Control

Push the project to GitHub:

bash
git init
git remote add origin https://github.com/<your-username>/<repo-name>.git
git add .
git commit -m "Initial commit"
git push -u origin master

