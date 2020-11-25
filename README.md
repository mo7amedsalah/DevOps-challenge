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

After you've done this, initalize your Terraform workspace, which will download the provider and initialize it with the values provided in the **terraform.tfvars** file by running 
```bash
$ terraform init
``` 

Provision your EKS cluster by running 
```bash
$ terraform apply
```

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

** to see running pods
 
```
$ kubectl get po  -n test
```

To show services find it by

```
$ kubectl get svc -n test
```

** By Eternal -IP you can access all services

3)Implementation a CICD pipeline for any application using the tools and the platform implemented from the previous steps this pipeline should be using groovy scripting Jenkins file.

1- Configure jenkins to create CI/CD pipeline

```
$ kubectl exec -n test pod name -- cat /var/jenkins_home/secrets/initialAdminPassword
```

