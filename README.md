# DevOps Exercise #1

* Deploy a web application to a cloud provider of your choice. This web application can be something you have written yourself or an open-source project.
* Deploy the web application as a Docker Container.
* Deploy the Docker Container using Kubernetes.
* Any supporting infrastructure should be configured and deployed as code (e.g. Terraform)
* Bonus points for any build and deployment automation employed in the deployment of the web application.
* Bonus points for demonstrating the ability to deploy, destroy and re-deploy the web application and any supporting infrastructure.
* Include all code and artefacts you create to complete this exercise within this repository for review.



Steps to execute

1. Create AWS Account
2. Get a Amazon ec2 instance
3. Install terraform in it
  curl -O https://releases.hashicorp.com/terraform/0.14.10/terraform_0.14.10_linux_amd64.zip
	sudo su
	unzip terraform_0.14.10_linux_amd64.zip -d /usr/bin/
	terraform -v
4. Configure aws
  aws configure
  provide Access Key ID:, Secret Access Key:
	region:us-east-2 and output: json
5. install git
  yum install git -y
  
6. git clone https://github.com/vilasvarghese/devops-exercise-1-infra-vilasvarghese
  Replace this with your account if you have forked.
7. cd devops-exercise-1-infra-vilasvarghese/terraform
8. Make necessary configurational changes to 
  https://github.com/vilasvarghese/devops-exercise-1-infra-vilasvarghese/blob/main/terraform/variables.tf
  https://github.com/vilasvarghese/devops-exercise-1-infra-vilasvarghese/blob/main/terraform/terraform.tfvars
9.  terraform init
10. terraform plan
11. terraform apply
12. Launch an ec2 instance
  curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
  sudo mv /tmp/eksctl /usr/local/bin
  sudo ln -s /usr/local/bin/eksctl /usr/bin/eksctl
  eksctl version
  eksctl create cluster --name my-cluster --region us-east-2 --fargate

  Setup kubectl to work with remote kubernetes master
  

13. Setup jenkins to execute a ci/cd job
  Login to <jenkins instance ip>:8080
  Find the password from sudo cat /var/lib/jenkins/secrets/initialAdminPassword and enter the same.
  
  Install Docker plugin and GitHub Integration Plugin
  Manage Jenkins → Manage Plugins → Available.
  
  Create Credentials
  Docker Hub: Click Credentials → global → Add Credentials, choose Username with password as Kind, enter the Docker Hub username and password and use dockerHubCredentials for ID.

GitHub: Click Credentials → Global → Add Credentials , choose Username with password as Kind, enter the GitHub username and password and use gitHubCredentials for ID.
(N.B: For now i have hardcoded this in the yaml files - kindly modify the same rather than creating these credentials)

Create a simple job (not pipeline)
Copy paste the following script

gitHub repo: https://github.com/vilasvarghese/devops-exercise-1-infra-vilasvarghese
-------------------------------------------------------------
mvn clean install
docker build -t java-web-app-cicd:latest .
if (docker ps -a | grep 'java-web-app-cicd')
then
  docker stop java-web-app-cicd
  docker rm -f java-web-app-cicd
fi
docker run -d -p 8888:8080 --name java-web-app-cicd java-web-app-cicd
mvn test
docker login -u <your docker user name> -p <your password>
docker commit java-web-app-cicd <your docker user name>/java-web-app-cicd:latest
docker push <your docker user name>/java-web-app-cicd:latest

kubectl delete deploy --all

kubectl create -f deploy-tomcat.yaml
-------------------------------------------------------------

14. Execute the job.
