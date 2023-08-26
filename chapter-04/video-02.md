

### Set variables
```
$group = "DockerOnAzureCourse-HOL-RG"
$location = "eastus"
$random = $RANDOM
$storageAccountName = "doazstore$random"
$sharename = "containerstore"
```

### Create a storage account with the parameters
```
az storage account create -g $group -n $storagestorageAccountNameacct -l $location --sku Standard_LRS --kind StorageV2
```

### Create a file share in the storage account
```
az storage share create --name $sharename --account-name $storageAccountName
```

### Get the storage credentials (this will be needed later)
```
$storageAccountKey = $(az storage account keys list -g $group --account-name $storageAccountName --query "[0].value" --output tsv)

echo $storageAccountKey
```

### Create a YAML file for the container instance
```
echo $storageAccountName
echo $storageAccountKey
echo $sharename

code file-share-volume-demo.yaml

apiVersion: '2019-12-01'
location: eastus
name: file-share-volume-demo
properties:
  containers:
  - name: aci-app-01
    properties:
      environmentVariables: []
      image: ubuntu
      command: ["tail", "-f", "/dev/null"]
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 4
      volumeMounts:
      - mountPath: /aci/data
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: <SHARE_NAME>
      storageAccountName: <STORAGE_ACCT_NAME>
      storageAccountKey: <STORAGE_ACCT_KEY>
tags: {}
type: Microsoft.ContainerInstance/containerGroups

* Replace the values for storageAccountName, storageAccountKey, and sharename
```

### Create the container instance using the YAML template
```
az container create --resource-group $group --file file-share-volume-demo.yaml
```

### Validate volume mount
```
az container exec -g $group -n file-share-volume-demo --exec-command "/bin/sh"

ls /aci/data
touch /aci/data/data02.txt
exit
```

>* Verify in storage account file share