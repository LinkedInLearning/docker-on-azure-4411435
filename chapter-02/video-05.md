
### Pre-Requisites
* Azure SubScription
* Azure DevOps Organization

### Modify the pipeline to include a deployment stage
1. Azure DevOps Console → project → containerappdevops → Pipelines → containerappdevops → Edit

2. Add a new stage
```
- stage: Deploy
  displayName: Deployment stage
  jobs:
  - job: Deploy
    displayName: Deploy
    pool:
      vmImage: $(vmImageName)
    steps:
    - task: AzureCLI@2
      inputs:
        azureSubscription: '<YOUR SUBSCRIPTION DETAILS>'
        scriptType: 'bash'
        scriptLocation: 'inlineScript'
        inlineScript: |
          group="DockerOnAzureCourse-HOL-RG" location="eastus"
          
          acrname=$(az acr list --resource-group $group --query [0].name --output tsv)
          
          acrloginserver=$(az acr show --name $acrname --query loginServer --output tsv)
            acrusername=$(az acr credential show --name $acrname --query username --output tsv)
          acrpassword=$(az acr credential show --name $acrname --query 'passwords[0].value' --output tsv)
          
          random=$RANDOM
          acidnsname="containerappdevops$random"
            
          # Retry logic
          max_retries=5
          retries=0
          while [ $retries -lt $max_retries ]; do
            # Create the container
              az container create --resource-group $group --name "containerappdevops-aci" --image "$acrloginserver/containerappdevops:$(tag)" --dns-name-label "$acidnsname" --ports 80 --cpu 2 --memory 4 --registry-username $acrusername --registry-password $acrpassword
              
              # Check if the container creation was successful
              if [ $? -eq 0 ]; then
                break
              fi
              
              # Check the status of the container
              status=$(az container show --resource-group $group --name "containerappdevops-aci" --query "instanceView.state" --output tsv)
              
              # If the container is transitioning, wait and retry
              if [ "$status" == "Transitioning" ]; then
                echo "Container is transitioning. Waiting for 30 seconds before retrying..."
                sleep 30
                retries=$((retries+1))
              else
                # If there's another error, exit the loop
                break
              fi
            done
            
            if [ $retries -eq $max_retries ]; then
              echo "Failed to create the container after $max_retries attempts."
              exit 1
            fi

```

