### Set variables
```
holgroup="DockerOnAzureCourse-HOL-RG"
vmgroup="DockerOnAzureCourse-RG"
location="eastus"
```

### Clean up resources
```
az group delete --name $holgroup --yes --no-wait
az group delete --name $vmgroup --yes --no-wait
```
