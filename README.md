# Blue-Green-Deployment-EKS
Blue-Green-Deployment-EKS

### Testing Kubernetes Blue/Green Deployment on EKS

**The Goal**

1. Create a simple node site
1. Create an docker image and host it on ECR
1. Create and host 3 different versions
1. Use Terraform to create the EKS cluster
1. Publish version 1.0.0 to version 1.2.0 using the Blue/Green Deployment pattern

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/8f9dbe7d-ca90-4a8b-ad76-0adb2961aae2)

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/b64cb1c1-c450-4ee1-b72a-793e0d5cdfc3)

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/73ea8c24-c3ea-4e68-a4fd-ca5f1be2f9a3)

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/35397fc2-51a7-4c29-bc11-412299930f33)

_________________________________________________________________________________________________________________________________


#### Prerequsites 
1. Dockerengine
2. EKSCTL, KUBECTL
3. Git
4. AWSCLI
5. Terraform
6. npm
7. Amazon ECR
8. AWS user Access & Secret key

#### Usefule Articles 

[Kubernetes + EKS + Blue/Green Deployment](https://medium.com/@jerome.decoster/kubernetes-eks-blue-green-deployment-99d611c596ad)
[Install Eksctl, Kubectl, AWS Cli](https://sunitabachhav2007.hashnode.dev/prometheus-and-grafana-dashboard-on-eks-cluster-using-helm-chart)

_________________________________________________________________________________________________________________________________

#### High Level Steps 

1. Create AWS EC2 Instance with approriate IAM role to have ful access of ECR, EKS, Administrator
2. Install the required softares on the system
3. Configure the AWS user in the system with Admin rights, this user will interact for ECR & EKS cluster tasks
4. Clone the git reposiroty
5. Build the docker image of the project with version wise - 1.0.0, 1.1.0, as shown in the diagram above
6. Open the firwall ports from AWS security group as well from the system firewall for the required TCP ports
7. Deploy the duilt image and test the deployment
8. Push the images to AWS ECR
9. Create the EKS cluster to run the ECR image over k8s
10. Expose the deployment service over the EKS load balancer
11. Switch the deployment and test if it is working as expected

_________________________________________________________________________________________________________________________________








