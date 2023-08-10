

## Deploy a container to ACI using Azure CLI

### Get the credentials of the container registry
```
$group = "DockerOnAzureCourse-HOL-RG"
$location = "eastus"

$acrname = $(az acr list --resource-group $group --query [0].name --output tsv)

$acrloginserver = $(az acr show --name $acrname --query loginServer)
$acrusername = $(az acr credential show --name $acrname --query username)
$acrpassword = $(az acr credential show --name $acrname --query 'passwords[0].value')
```

### Install/Upgrade the Azure Container Apps extension for Azure CLI
* The extension is in preview
```
az extension add --name containerapp --upgrade
```

### Deploy the container app
```
az containerapp up --name "containerapp-aca" --resource-group $group --location $location --environment 'container-apps' --image "$acrloginserver/containerappdevops" --target-port 80 --ingress external --registry-username $acrusername --registry-password $acrpassword
```

### Validate that the container app is running
```
$acafqdn = $(az containerapp show --resource-group $group --name "containerapp-aca" --query properties.configuration.ingress.fqdn --output tsv)

start chrome http://$acafqdn
```




