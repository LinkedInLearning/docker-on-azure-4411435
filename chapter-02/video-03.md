

## Deploy a container to ACI using Docker CLI


### Create deployment credentials using Azure CLI 
* Make a note of the **`appID`**, **`password`** and **`tenant`**

```
$subid = $(az account show --query id -o tsv)

az ad sp create-for-rbac --name docker-azure-cred --role Contributor --scopes /subscriptions/$subid
```

### Create resource group for container instance
```
$group = "DockerOnAzureCourse-RG"
az group create --name $group --location eastus
```


### Create an ACI context
```
docker login azure --help

docker login azure --client-id "<replace_with_appID>" --client-secret "<replace_with_password>" --tenant-id "<replace_with_tenant>"

docker context list

docker context create aci --help

docker context create aci azsubcontext

* Select Azure subscription
* Select Azure resource group

docker context create aci azsubcontext --subscription-id $subid --resource-group $group --location "eastus"
```

## Switch to the ACI context
```
docker context use azsubcontext
```

## Run a container in ACI using Docker CLI
```
$group = "DockerOnAzureCourse-RG"
$location = "eastus"
$acrname = $(az acr list --resource-group $group --query [0].name --output tsv)
$acrloginServer = $(az acr list --resource-group $group --query [0].loginServer --output tsv)

docker run --name containerapp -p 80:80 "$acrloginserver/containerapp" --restart always --cpus 1.5 --memory 2G

az container create --resource-group $group --name containerapp --image "$acrloginserver/containerapp" --dns-name-label "aci-demo" --ports 80
```

### View running containers in ACI
```
docker ps
```

## To view logs from your container, run:
```
docker logs <CONTAINER_ID>
docker logs web
docker logs web --follow
```

## To execute a command in a running container, run:
```
docker exec --tty <CONTAINER_ID> COMMAND
docker exec --tty web ls
```

## To stop and remove a container from ACI, run:
```
docker stop <CONTAINER_ID>
docker rm <CONTAINER_ID>
docker stop web
docker rm web
```

## Healthchecks
A health check can be described using the flags prefixed by --health-. 
This is translated into LivenessProbe for ACI. 
If the health check fails then the container is considered unhealthy and terminated. 
In order for the container to be restarted automatically, the container needs to be run with a restart policy (set by the --restart flag) other than no. 
Note that the default restart policy if one isn’t set is no

--restart



