

### Pre-Requisites
* Azure SubScription
* Azure DevOps Organization

### Create an Azure DevOps Project
1. Azure DevOps Console → New project
**`Project Name`**: containerapp
**`Visibility`**: Private
**`Create`**

2. Azure DevOps Console → containerapp (Project) → Repos → Copy URL


### Initialize git and push code to the remote repository
```
git init
git remote add origin <repo_url>
git remote -v
git config --global user.name "<name>"
git config --global user.email "<email>"

git status
git add .
git commit -m "added dotnet mvc template"
git push --set-upstream origin master
git status
```

### Create and run pipeline
Azure DevOps Console → containerapp → Pipelines → Create pipeline

**`Where is your code?`**: Azure Repos Git → containerapp
**`Configure your pipeline`**: Docker - Build and push an image to Azure Container Registry → Select your Azure subscription → Continue

**`Container registry`**: doazacr*********
**`Image Name`**: containerappdevops
**`Dockerfile`**: $(Build.SourcesDirectory)/Dockerfile
Validate and configure

Review pipeline

Save and run → Save and run

Select and monitor Build job


### Review the container image in the registry
Azure Portal → Container registries → doazacr******* → Services → Repositories → containerappdevops