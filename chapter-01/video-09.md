


### Review the pipeline trigger and container registry
1. Azure DevOps Console → containerapp → Pipelines → containerapp → Edit
* Review the trigger

2. Azure Portal → Container registries → doazacr******* → Services → Repositories → containerappdevops
* Review the tags

### Make code changes locally and push to the remote repository
1. VS Code → Views → Home → Index.cshtml
* Change "Welcome" to "Docker on Azure Course"
* Commit → Message: "modified header" → Commit → Would you like to stage all changes: Yes → Sync changes
* This action will pull and push commits from and to "origin/master": OK

### Review the pipeline run
1. Azure DevOps Console → containerapp → Pipelines → Runs → Create pipeline

### Confirm the updated container image in the registry
1. Azure Portal → Container registries → doazacr******* → Services → Repositories → containerappdevops
* Review the tags





