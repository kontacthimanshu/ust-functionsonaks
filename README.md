# Pre-requisites
### - An Azure Subscription with contributor permissions
### - Azure CLI or Azure PowerShell
### - Azure Functions Core Tools
### - Docker Desktop latest community edition running on the laptop
# ---------------------------------------------------------------------------

# High Level Steps
# ---------------------------------------------------------------------------
## 1) Create an Azure resource group
## 2) Create a Azure Storage Account
## 3) Create Azure Container Registry instance
## 4) Create Service Principal 
## 5) Create Azure Kubernetes Services instance
## 6) Install KEDA in the AKS cluster
## 7) Clone the repo
## 8) Run command --- to install function app
## 9) Hit the following URL:
# ---------------------------------------------------------------------------
#### - Launch a Windows PowerShell Window
#### - Run below command:
##### az acr create -n ustdemoacr -g ustdemo -l eastus --sku standard
#### - Verify that Container Registry has been created by using command below:
##### az acr list -o table
#### - Login to Azure Container Registry by using following command:
##### az acr login -n ustdemoacr
#### - Get the name of login server and copy it with command below:
##### az acr list -o table
#### - Create service principal with following command: (Ensure to copy the output of this command)
##### az ad sp create-for-rbac --skip-assignment
#### - Grant this service principal permission to be able to pull images from Azure ACR
##### $acrId = az acr show --name ustdemoacr --resource-group ustdemo --query "id" --output tsv
##### az role assignment create --assignee ''Use appId attribute from the output you copied'' --role Reader --scope $acrId
#### - Create the AKS cluster
##### az aks create --name ustdemoakscluster --resource-group ustdemo --node-count 2 --generate-ssh-keys --service-principal ''use appId from the output copied'' --client-secret ''use password copied from output above'' --location eastus --network-plugin azure
#### - Get cluster credentials and merge the configuration into our existing config file.
##### az aks get-credentials --resource-group "ustdemo" --name ustdemoakscluster
##### kubectl config get-contexts
#### - set current context to the Azure context
##### kubectl config use-context ustdemoakscluster
#### - Install KEDA in the AKS cluster
#### - Add KEDA repo to your Helm repo with following command:
##### helm repo add kedacore https://kedacore.github.io/charts
##### helm repo update
#### - Create namespace in AKS cluster for KEDA components/resources
##### kubectl create namespace keda
##### helm install keda kedacore/keda --namespace keda





