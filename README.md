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

1. [Kubernetes + EKS + Blue/Green Deployment](https://medium.com/@jerome.decoster/kubernetes-eks-blue-green-deployment-99d611c596ad)

1. [Install Eksctl, Kubectl, AWS Cli](https://sunitabachhav2007.hashnode.dev/prometheus-and-grafana-dashboard-on-eks-cluster-using-helm-chart)
   
1. [Gir Repository](https://github.com/jeromedecoster/eks-blue-green.git)

2. [Update Kubeconfig file](https://docs.aws.amazon.com/eks/latest/userguide/create-kubeconfig.html)

3. [Install Terraform](https://www.linuxbuzz.com/install-terraform-on-ubuntu/)

_________________________________________________________________________________________________________________________________

#### High Level Steps 

1. Create AWS EC2 Instance with approriate IAM role to have ful access of ECR, EKS, Administrator
2. Install the required softwares on the system - Dockerengine, Terraform, Eksctl, KubeCtl, AWS Cli, Git, NPM
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

### Lets begin with the LAB 

#### 1. Install the required softwares

**Let us get the installation done for AWS CLI 2**

```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" 
sudo apt install unzip
unzip awscliv2.zip 
sudo ./aws/install

```
- Output
  
  ![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/c958bb10-55d4-4f63-8df5-5241cc14106d)

**Install and Setup Kubectl**

```
curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version

```
- Output

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/d752d1bb-032f-4e3e-a83e-45c1e7857cc9)

**Install and Setup eksctl**

```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp

sudo mv /tmp/eksctl /usr/local/bin

eksctl version

```
- Output

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/4fb9e8ec-9e7d-4b46-9541-498991a8ecd9)

**Configure AWS Interactive Admin User**

```
aws configure
```

-Output 

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/af5dea05-dfa1-445e-ada1-37c2fb44fc7a)

**Create AWS ECR**

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/8150f9e7-4f4c-4854-be6f-d124c18b0f17)

**Install Terrafrom**

```
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update
sudo apt install terraform -y
terraform version
```
- Output

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/09994d5c-d812-4763-b5d1-0259710d41ee)


#### 2. Clone the Git Reposiroty once all the prerequsites softwares are installed on the system

```
# download the code
git clone \
    --depth 1 \
    https://github.com/jeromedecoster/eks-blue-green.git \
    /tmp/aws
    
# cd
cd /tmp/aws
```
- Output

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/beefc625-ce7b-421c-aede-ba9a2edeffcf)

**To setup the project, run the following command :**
```
# npm install + terraform init + create ecr repository
$ make setup

```
- Output

  ![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/01e53da8-dd3a-4a46-bc16-2f4f5f15b25e)

- Note:- Plase update the terraform version form the file ./infra/versions.tf, before running the above command.

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/8876e7ec-6d2e-466b-8f50-d76394f95d63)


**Let’s run the website locally :**

```
make local-1.0.0
```

- Output

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/1476f93c-0426-435f-9429-dff99bbc327d)


- By opening the address http://localhost:3000 we can see the website :

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/96dfa094-4c24-4a3b-976a-377dac3ec250)


**We can see the site in version 1.1.0 by executing this command :**

```
make local-1.1.0
```

- Output

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/ea7e00df-7724-4558-b5fd-f5e2156b3865)


**By reloading the website we can see :**

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/570ec4a2-cfe2-47db-965e-573a14e9ca6d)

**And to see version 1.2.0 of the site :**

```
make local-1.2.0
```

- Output

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/14e15920-06d3-4414-838d-46da4ae36a01)

**By reloading the website we can see :**

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/d2340206-08b7-4d75-9053-1ccd2f5105ae)


### 3. Building and push the Docker images

**We will now create the 3 docker images with this command:**

```
# build all 1.x.0 versions
$ make build-all
```

- Output

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/efe4dccc-1a36-4009-8d5f-ffec66f8d4c6)


**We can now push our 3 images on ECR :**

```
# push all 1.x.0 versions to ecr

docker tag eks-blue-green:1.2.0 public.ecr.aws/m0v5l9b2/eks-blue-green:1.2.0
docker tag eks-blue-green:1.1.0 public.ecr.aws/m0v5l9b2/eks-blue-green:1.1.0
docker tag eks-blue-green:1.0.0 public.ecr.aws/m0v5l9b2/eks-blue-green:1.0.0
docker push eks-blue-green:1.2.0 public.ecr.aws/m0v5l9b2/eks-blue-green:1.2.0
docker push public.ecr.aws/m0v5l9b2/eks-blue-green:1.2.0
docker push public.ecr.aws/m0v5l9b2/eks-blue-green:1.1.o
docker push public.ecr.aws/m0v5l9b2/eks-blue-green:1.1.0
docker push public.ecr.aws/m0v5l9b2/eks-blue-green:1.0.0

```
- Output

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/4bf3362a-812c-4123-b6ea-ef48b66c884d)


#### 3. Creating the EKS cluster

**Create EKS Cluster**

```
eksctl create cluster --name eks-blue-green \
--node-type t2.medium \
--nodes 2 \
--nodes-min 2 \
--nodes-max 3 \
--region ap-south-1 \
--zones=ap-south-1a,ap-south-1b \
--authenticator-role-arn=arn:aws:iam::XXXXXXXXXXXX:instance-profile/SSM-FullAccess \
--auto-kubeconfig \
--asg-access \
--external-dns-access \
--appmesh-access \
--alb-ingress-access
```
- Output

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/f1fbe0b1-a024-44e0-84b6-958554be12c4)


**We can now correctly list the namespaces :**

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/c3971590-95ec-4446-878f-c6d066ecdaa5)

**We use these templates. We create a namespace :**

- CD to the K8s directory from the cloned repo folder

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/8aec4522-02c1-455d-b7bf-b876977968dc)

```
kubectl apply --filename namespace.yaml
export DOCKER_IMAGE=xxxxx.dkr.ecr.eu-west-3.amazonaws.com/eks-blue-green:1.0.0
export LABEL_VERSION=1-0-0
```

**Publish the version 1.0.0**

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/4dacabdd-828c-45e7-a6ce-5a2e2490e3f4)


**To upload our first version we run this command :**

```
# publish the 1.0.0 version
$ make k8s-1.0.0
```

- Output

![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/a18ddf78-5709-4b80-a4d6-6ea647158ab8)


![image](https://github.com/anand40090/Blue-Green-Deployment-EKS/assets/32446706/86aeb4de-caab-483d-ac33-a4215b578b04)




