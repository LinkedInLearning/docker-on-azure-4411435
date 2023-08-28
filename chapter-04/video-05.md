### Set variables
```
group="DockerOnAzureCourse-HOL-RG"
location="eastus"
```

### Create a YAML file for the container instance
```
code gitrepo-volume-demo.yaml

apiVersion: '2019-12-01'
location: eastus
name: gitrepo-volume-demo
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
      - mountPath: /mnt/repo1
        name: gitrepo1
      - mountPath: /mnt/repo2
        name: gitrepo2
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: gitrepo1
    gitRepo: {
      "repository": "https://github.com/Azure-Samples/aci-helloworld"
    }
  - name: gitrepo2
    gitRepo: {
      "directory": "my-custom-clone-directory",
      "repository": "https://github.com/Azure-Samples/aci-helloworld",
      "revision": "d5ccfcedc0d81f7ca5e3dbe6e5a7705b579101f1"
    }
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

### Create the container instance using the YAML template
```
az container create --resource-group $group --file gitrepo-volume-demo.yaml
```

### Validate gitRepo volume mount
```
az container exec -g $group --name gitrepo-volume-demo --container-name aci-app-01 --exec-command "/bin/sh"

ls /mnt/repo1/aci-helloworld
ls /mnt/repo2/my-custom-clone-directory

exit
```
