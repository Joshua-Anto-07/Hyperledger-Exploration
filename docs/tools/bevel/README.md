# Hyperledger Exploration  ![](https://img.shields.io/badge/-Live-darkgreen)
![](https://img.shields.io/badge/Domain-Blockchain-blue) ![](https://img.shields.io/badge/Blockchain-Hyperledger-brown) <br/> 

# Hyperledger Bevel
![](https://img.shields.io/badge/Exploration_By-Joshua_Anto_A-gold) ![](https://img.shields.io/badge/Shree_Harini_T-gold)  <br/>
![](https://img.shields.io/badge/Start-Jan-silver) ![](https://img.shields.io/badge/End-June-silver) 

<p align="center"><img src="../../logos/Hyperledger_Bevel.jpg" width=320> </p>

## Introduction
### What is Hyperledger Bevel?
Bevel is a Blockchain Automation Framework designed for effortlessly deploying production-ready solutions with minimal effort.

The framework leverages Ansible, Helm, and Kubernetes to orchestrate the deployment of production DLT (Distributed Ledger Technology) networks. DevOps Engineers utilize Ansible for network configuration, while Helm charts serve as instructions for deploying essential components within Kubernetes. Kubernetes is chosen for its flexibility, enabling the Blockchain Automation Framework to deploy DLT networks across any Kubernetes- supported cloud environment.

Hyperledger BEVEL specializes in deploying Hyperledger Fabric networks, employing YAML files for network configuration. When Ansible executes its playbook, which handles tasks such as setting up channels or adding a peer, it takes a network YAML file as input to ensure proper configuration and deployment.

### What platforms are supported by Hyperledger Bevel ?
Bevel currently supports the following DLT/Blockchain Platforms:<br/>
  -	R3 Corda
  -	Hyperledger Fabric
  -	Hyperledger Indy
  -	Hyperledger Besu
  -	Quorum
  -	Substrate.3

## Prerequissites
  -	Laptop/Desktop
  -	Kubernetes Cluster
  -	Hashicorp Vault-1.13.1
  -	Docker
  -	Gitops

### Setting up Github

1.	Go to bevel on GitHub and click Fork button on top right.
2.	Create a project directory in your home directory and clone the forked repository to your local machine.
```
mkdir project cd project
cd project
git clone https://github.com/<username>/bevel.git
cd bevel
```

### Setting up Docker
We have used Docker on which kubernetes cluster will run
```
sudo apt-get update
sudo apt install docker
```

### Installing Hashicorop Vault
We need Hashicorp Vault for Secure storage of the certificate and private keyes
1.	Vault can be installed using these commands on Ubuntu.
```
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install vault
```
Note: If there’s an error in the 2nd command,  specify your ubuntu/Linux version instead of “$(lsb_release -cs)”.

2.	After installing Vault to start the vault server
   Create a config.hcl file in the <project> directory with the following contents. (use a file path in the path attribute which exists on your local machine)
```
ui = true
storage "file" {
  path ="./<project>/data
}
listener "tcp" {
  address = "0.0.0.0:8200"
  tls_disable = 1
}
```
3.	Start the Vault server by executing. Do not close this terminal.
   ```sudo vault server -config=config.hcl```
4.	Open browser at http://localhost:8200/. And initialize the Vault by providing your choice of key shares and threshold.
<p align="center"><img src="/docs/tools/bevel/Images/Vault-Homepage.png" width=75%> </p>

5.  Click Download Keys or copy the keys, you will need them. Then click Continue to Unseal.
<p align="center"><img src="/docs/tools/bevel/Images/Download-keys.png"width=75%> </p>
6.	Provide the unseal key first and then the root token to login.
<p align="center"><img src="/docs/tools/bevel/Images/Vault-Unseal.png"width=75%> </p>
7.  After the initialization of the Vault, you will be directed to Vault Homepage as shown below.
<p align="center"><img src="/docs/tools/bevel/Images/Vault-Homepage.png"width=75%> </p>


### Installing Minikube
Minikube can be used as the Kubernetes cluster on which the DLT network will be deployed.
-	Minikube can be installed using these commands on Ubuntu.
```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 

sudo install minikube-linux-amd64 /usr/local/bin/minikube 
```
-	Configure Minikube to use 4GB (or in accordance with your computer’s available memory) memory and the default Kubernetes version.
```
minikube config set memory 4096

minikube config set kubernetes-version v1.23.1
```
- Start your kubernetes cluster. (This might take a while, if you’re starting it for the first time)
```
minikube start
```
- Check status of minikube by running.
```
minikube status
```
- To stop (do not delete) minikube execute the following.
```
minikube stop
```
**ALL THE PRE-REQUISITES HAS BEEN SETUP SUCCESSFULLY.**

## Deploying a DLT network on Minikube using Bevel

### 1. Update kubeconfig file
- Create a build folder inside your bevel repository
```
mkdir build
```
- Copy ca.crt, client.key, client.crt from ~/.minikube to build
```
cp ~/.kube/config build/
```
- Copy ~/.kube/config file to build
```
cp ~/.minikube/ca.crt build/
cp ~/.minikube/profiles/minikube/client.key build/
cp ~/.minikube/profiles/minikube/client.crt build/
```
- Open the above config file in build directory and update file path for certificate-authority, client-certificate and client-key to point to the respective files copied in build directory.
<p align="center"><img src="/docs/tools/bevel/Images/config_file.png"> </p>
<p align="center"><img src="/docs/tools/bevel/Images/config_file2.png"> </p>

Note: Update config server value using the following command

If using Ubuntu VM, update config server value to following:
**server: https://<specify public ip of VM>:8443**
<p align="center"><img src="/docs/tools/bevel/Images/minikube_ip.png"> </p>


### 2. Setup Hashicorp Vault
- Get your local IP Address (127.0.0.1) for Vault.
- In a new terminal, execute the following:
```
export VAULT_ADDR='http://<Your Vault local IP address>:8200' #e.g. http://192.168.0.1:8200
export VAULT_TOKEN="<Your Vault root token>"

# enable Secrets v2 
vault secrets enable -version=2 -path=secretsv2 kv
```
### 3. Edit the network configuration file
- Navigate using these commands
```
# For example, for Fabric
cd project/bevel
cp platforms/hyperledger-fabric/configuration/samples/network-proxy-none.yaml build/network.yaml
```
- Open the above build/network.yaml in your editor and update the following.
- Update Docker configurations
```
docker:
    url: "ghcr.io/hyperledger"
    # Comment username and password as it is public repo
    #username: "<your docker username>"
    #password: "<your docker password/token>"
```
- For each organization, update ONLY the following and leave everything else as it is.
```
cloud_provider: minikube
k8s:
    context: "minikube"
    config_file: "/home/bevel/build/config"
vault:
    url: "http://<Your Vault local IP address>:8200" # Use the local IP address NOT localhost e.g. http://192.168.0.1:8200
    root_token: "<your vault_root_token>"
gitops:
    git_protocol: "https" # Option for git over https or ssh
    git_url: "<https/ssh url of your forked repo>" #e.g. "https://github.com/hyperledger/bevel.git"
    git_repo: "<url of your forked repo without the https://>" #e.g. "github.com/hyperledger/bevel.git"
    username: "<github_username>"
    password: "<github token>"
    email: "<github_email>"
```
Reference:
<p align="center"><img src="/docs/tools/bevel/Images/network_yaml_file.png"> </p>


## Execution
1.	Make sure that Minikube and Vault server are running. Check by running:
   ```
minikube status
vault status
```
<p align="center"><img src="/docs/tools/bevel/Images/minikube_vault_status.png"> </p>

2.Now run the following commands to deploy your chosen DLT on minikube:
```
cd bevel

docker run -it -v $(pwd):/home/bevel/ --network="host" ghcr.io/hyperledger/bevel-build:latest /bin/bash

# Ensure that git config is setup
git config --global user.name "UserName"
git config --global user.email "UserEmailAddress" cd bevel
export KUBECONFIG=/home/bevel/build/config
chmod +x run.sh
./run.sh
```
Reference:
<p align="center"><img src="/docs/tools/bevel/Images/execution_reference_picture.png"> </p>












