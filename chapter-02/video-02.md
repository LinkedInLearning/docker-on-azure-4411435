

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

### Generate a random name for the container instance
```
$random = Get-Random
$acidnsname = "containerapp$random"
```

### Deploy the container instance using Azure CLI
```
az container create --resource-group $group --name "containerapp-aci" --image "$acrloginserver/containerapp:latest" --dns-name-label "$acidnsname" --ports 80 --cpu 2 --memory 4 --registry-username $acrusername --registry-password $acrpassword
```

### Validate that the container app is running
```
$acifqdn = $(az container show --resource-group $group --name "containerapp-aci" --query ipAddress.fqdn --output tsv)

start chrome http://$acifqdn
```




