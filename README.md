# DevOps-challenge

This repo is a DevOps challange  to deploy an application in Amazon EKS cluster.

---

### Steps for deploying the cluster using EKS:

1- Implement and configure an EKS cluster on AWS using infrastructure as a code.

* install terraform
* install awscli
* install  [AWS IAM Authenticator](https://docs.aws.amazon.com/eks/latest/userguide/install-aws-iam-authenticator.html)
* install WGET
* Install [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

2- Setting up AWS IAM users for Terraform: 
* create an AWS IAM user and give it:
  * AdministratorAccess
  * AmazonEKSClusterPolicy

After these steps, AWS will provide you a Secret Access Key and Access Key ID. Save them preciously because this will be the only time AWS gives it to yo
Configure AWS CLI to use your credentials

![AWS_CLI](https://github.com/mo7amedsalah/DevOps-challenge/blob/main/images/Screenshot%20from%202020-11-25%2016-50-40.png)

After you've done this, initalize your Terraform workspace, which will download the provider and initialize it with the values provided in the **terraform.tfvars** file by running 
```bash
$ terraform init
``` 

![TF_INIT](https://github.com/mo7amedsalah/DevOps-challenge/blob/main/images/Screenshot%20from%202020-11-25%2016-57-43.png)



Provision your EKS cluster by running 
```bash
$ terraform apply
```

![TF_APPLY](https://github.com/mo7amedsalah/DevOps-challenge/blob/main/images/Screenshot%20from%202020-11-25%2017-47-09.png)


In Console : 
![console](https://github.com/mo7amedsalah/DevOps-challenge/blob/main/images/Screenshot%20from%202020-11-25%2017-48-15.png)
![console](https://github.com/mo7amedsalah/DevOps-challenge/blob/main/images/Screenshot%20from%202020-11-25%2017-49-03.png)

---

### configure kubectl:

This command will get the access credentials for your cluster and automatically configure kubectl.

```
$ aws eks --region us-east-2 update-kubeconfig --name challenge-eks-oGMblHKa
```

2) Implementation the needed DevOps tools to create CICD pipeline (Jenkins, SonarQube, Nexus, etc. ...) using ansible or any other configuration management tool on the created k8s cluster.

** install openshift
** install ansible


** run `$ ansible-playbook roles.yaml`

![roles](https://github.com/mo7amedsalah/DevOps-challenge/blob/main/images/Screenshot%20from%202020-11-25%2018-21-37.png)

** to see running pods

```
$ kubectl get po  -n test
```
![pods](https://github.com/mo7amedsalah/DevOps-challenge/blob/main/images/Screenshot%20from%202020-11-25%2018-23-27.png)

To show services find it by

```
$ kubectl get svc -n test
```
![services](https://github.com/mo7amedsalah/DevOps-challenge/blob/main/images/Screenshot%20from%202020-11-25%2019-10-02.png)

** By External -IP you can access all services

-jenkins
![jenkins](https://github.com/mo7amedsalah/DevOps-challenge/blob/main/images/Screenshot%20from%202020-11-25%2018-25-46.png)

-Nexus
![nexus](https://github.com/mo7amedsalah/DevOps-challenge/blob/main/images/Screenshot%20from%202020-11-25%2018-47-21.png)

-SonarQube
![sonarqube](https://github.com/mo7amedsalah/DevOps-challenge/blob/main/images/Screenshot%20from%202020-11-26%2000-20-40.png)


3)Implementation a CICD pipeline for any application using the tools and the platform implemented from the previous steps this pipeline should be using groovy scripting Jenkins file.

** Configure jenkins to create CI/CD pipeline

```
$ kubectl exec -n test pod name -- cat /var/jenkins_home/secrets/initialAdminPassword
```
![initialpass](https://github.com/mo7amedsalah/DevOps-challenge/blob/main/images/Screenshot%20from%202020-11-25%2019-22-35.png)

-Install plugins

![install plugins](https://github.com/mo7amedsalah/DevOps-challenge/blob/main/images/Screenshot%20from%202020-11-25%2019-24-29.png)

-Create user

![create user](https://github.com/mo7amedsalah/DevOps-challenge/blob/main/images/Screenshot%20from%202020-11-25%2019-25-29.png)

-Jenkins dashboard

![dashboard](https://github.com/mo7amedsalah/DevOps-challenge/blob/main/images/Screenshot%20from%202020-11-25%2019-29-02.png)

- Add credentials

![credentials](https://github.com/mo7amedsalah/DevOps-challenge/blob/main/images/Screenshot%20from%202020-11-26%2003-39-27.png)

** Create jenkinsfile

```
pipeline {
   agent any
   stages {
       stage('Build') {
           agent {
               any {
                   image 'golang'
               }
           }
           steps {
               // Create our project directory.
               sh 'cd ${GOPATH}/src'
               sh 'mkdir -p ${GOPATH}/src/hello-world'
               // Copy all files in our Jenkins workspace to our project directory.               
               sh 'cp -r ${WORKSPACE}/* ${GOPATH}/src/hello-world'
               // Build the app.
               sh 'go build'              
           }    
       }
       stage('Test') {
           agent {
               any {
                   image 'golang'
               }
           }
           steps {                
               // Create our project directory.
               sh 'cd ${GOPATH}/src'
               sh 'mkdir -p ${GOPATH}/src/hello-world'
               // Copy all files in our Jenkins workspace to our project directory.               
               sh 'cp -r ${WORKSPACE}/* ${GOPATH}/src/hello-world'
               // Remove cached test results.
               sh 'go clean -cache'
               // Run Unit Tests.
               sh 'go test ./... -v -short'           
           }
       }
 
       stage ('Deploy') {
           steps {
               script{
                   def image_id = registry + ":$BUILD_NUMBER"
                   sh "ansible-playbook  playbook.yaml --extra-vars \"image_id=${image_id}\""
               }
           }
       }
   }
}

```

** Create pipeline from jenkins dashboard





