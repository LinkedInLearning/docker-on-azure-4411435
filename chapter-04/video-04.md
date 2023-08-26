
### Generate Base64 encoding of a secret value
```
echo "sensitivesecret" | base64
```

### Create a YAML file for the container instance
* Replace **`BASE_64_ENCODED_VALUE`** with the value generated earlier
```
code emptydir-volume-demo.yaml

apiVersion: '2019-12-01'
location: eastus
name: emptydir-volume-demo
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
      - mountPath: /mnt/shareddata
        name: emptydir
  osType: Linux
  restartPolicy: Always
  - name: aci-app-02
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
      - mountPath: /mnt/shareddata
        name: emptydir
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: emptydir
    emptyDir: {}
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

### Create the container instance using the YAML template
```
az container create --resource-group $group --file emptydir-volume-demo.yaml
```

### Validate volume mount
```
az container exec -g $group -name emptydir-volume-demo --container-name aci-app-01 --exec-command "/bin/sh"
ls /mnt/shareddata
touch /mnt/shareddata/data.txt
exit

az container exec -g $group -name emptydir-volume-demo --container-name aci-app-02 --exec-command "/bin/sh"
ls /mnt/shareddata
exit
```

