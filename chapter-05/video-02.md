### Set variables
```
group="DockerOnAzureCourse-HOL-RG"
location="eastus"
```

### Create a YAML file for the container group
```
code faulty-aci-demo.yaml

apiVersion: '2019-12-01'
location: eastus
name: faulty-aci-demo
properties:
  containers:
  - name: aci-app-01
    properties:
      environmentVariables: []
      image: ubuntu:20.04
      command: ["/bin/nonexistentcommand"]
      ports:
      - port: 8080
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.0
      volumeMounts:
      - mountPath: /mnt/shareddata
        name: emptydir
  - name: aci-app-02
    properties:
      environmentVariables: []
      image: ubuntu:20.04
      command: ["/bin/bash", "-c", "while true; do echo 'Maxing out CPU'; done"]
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 4
      volumeMounts:
      - mountPath: /monty/shareddata
        name: emptydir
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: emptydir
    emptyDir: {}
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

### Create the container group using the YAML template
```
az container create --resource-group $group --file faulty-aci-demo.yaml
```