
### Set variables
```
group="DockerOnAzureCourse-HOL-RG"
location="eastus"
```

### Generate Base64 encoding of a secret value
```
echo "sensitivesecret" | base64
```

### Create a YAML file for the container instance
* Replace **`BASE_64_ENCODED_VALUE`** with the value generated earlier
```
code secret-volume-demo.yaml

apiVersion: '2019-12-01'
location: eastus
name: secret-volume-demo
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
      - mountPath: /mnt/secrets
        name: secretvol
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvol
    secret:
      secret1: BASE_64_ENCODED_VALUE
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

### Create the container instance using the YAML template
```
az container create --resource-group $group --file secret-volume-demo.yaml
```

### Validate volume mount
```
az container exec -g $group --name secret-volume-demo --exec-command "/bin/sh"

ls /mnt/secrets
cat /mnt/secrets/secret1

exit
```
