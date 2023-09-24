

### Create a new DOTNET core project 
```
dotnet --list-sdks
dotnet --list-runtimes

mkdir dotnet-projects
cd .\dotnet-projects\
dotnet new mvc -o containerapp
cd .\containerapp\
ls
code .
```

### Install the Visual Studio Code extensions (C# and Docker)
```
VS Code → Extensions → Search for C# → Install the extension

VS Code → Extensions → Search for Docker → Install the extension
```

### Containerize the application
```
VS Code → F1 → Docker: Add docker files to workspace
.NET: ASP.NET Core
Linux
80,443 → Enter
Include optional Docker Compose files?: No

Review Dockerfile
Right click Dockerfile → Build image
```

### Test the containerized app locally
```
Go back to PowerShell:

docker image ls
docker run --detach --publish 80:80 containerapp
docker container ls

start chrome http://localhost:80

docker container stop <container_id>
```

### Create a container registry using the Azure CLI
```
az login

$group = "DockerOnAzureCourse-HOL-RG"
$location = "eastus"
$random = Get-Random
$acrname = "doazacr$random"

az group create --resource-group $group --location eastus
az acr create --resource-group $group --name $acrname --sku Basic --admin-enabled true
```

### View the repositories and images in the registry
* It should be empty as we are yet to create a repository or push an image to the registry
```
az acr repository list --name $acrname
```

### Upload the containerized app to the registry
```
# Before pushing and pulling container images, we must log in to the registry
# For this, we need our registry login server address, username and password

$acrloginserver = $(az acr show --name $acrname --query loginServer)
$acrusername = az acr credential show --name $acrname --query username
$acrpassword = az acr credential show --name $acrname --query 'passwords[0].value'

echo $acrloginserver
echo $acrusername
echo $acrpassword

# Log in to registry using Docker
docker login "$acrloginserver" --username $acrusername --password $acrpassword

# Tag the image
docker tag containerapp:latest $acrloginserver/containerapp:latest
docker image ls

# Push the image
docker push $acrloginserver/containerapp:latest
```

### Verify the image in the registry
```
az acr repository list --name $acrname

az acr repository show --name $acrname --repository containerapp

az acr manifest list-metadata $acrloginserver/containerapp
```
