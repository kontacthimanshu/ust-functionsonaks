# Pre-requisites
### - An Azure Subscription with contributor permissions
### - Azure CLI or Azure PowerShell
### - Azure Functions Core Tools
### - Docker Desktop latest community edition running on the laptop
# ----------------------------------------------------------------

# High Level Steps
# ----------------------------------------------------------------
## 1) Create an Azure resource group
## 2) Create a Azure Storage Account
## 3) Create Azure Container Registry instance
## 4) Create Service Principal 
## 5) Create Azure Kubernetes Services instance
## 6) Install KEDA in the AKS cluster
## 7) Clone the repo
## 8) Run func command to install function app
## 9) Upload few files to blob container to see if the files are being picked up and function is being triggered
# ----------------------------------------------------------------
#### - Launch a Windows PowerShell Window
#### - Run the command below to login to Azure:
##### az login
#### - Key in the credentials and come back to PowerShell window post login.
#### - Run the following command to create a resource group:
##### az group create -l eastus -n ustdemo
#### - Run following command to create a Azure storage account:
#### az storage account create -n ustfuncdemostorage -g ustdemo -l eastus --sku Standard_LRS
#### - Run the following command to create ACR instance:
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
#### - Create namespace in AKS cluster for KEDA components/resources and install KEDA
##### kubectl create namespace keda
##### helm install keda kedacore/keda --namespace keda
#### - Clone the repo by running following command inside a folder of your choice:
##### git clone https://github.com/kontacthimanshu/ust-functionsonaks
#### - Update storage connection strings in the local.settings.json file inside folder "azstoragetriggerdemo"
#### - Update storage connection strings for keys named "AzureWebJobsStorage" and "storageconstr".
#### - On the command prompt or PowerShell be within folder "azstoragetriggerdemo" and run following command
##### func kubernetes deploy --name azstoragetriggerdemo --registry ustdemoacr.azurecr.io
#### - Upload few files to the blob container
#### - Run following commands to check the logs of the POD running your function
##### kubectl get pods
#### - Note the POD ID from the output of previous command
##### kubectl logs "POD ID noted from previous step"
#### - You should see messages like "C# Blob trigger function Processed blob name <file name you uploaded>"
  




