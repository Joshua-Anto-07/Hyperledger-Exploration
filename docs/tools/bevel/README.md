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

### Setting your Hashicorop Vault
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
5.  Click Download Keys or copy the keys, you will need them. Then click Continue to Unseal.
6.	Provide the unseal key first and then the root token to login.
7.  After the initialization of the Vault, you will be directed to Vault Homepage as shown below.











