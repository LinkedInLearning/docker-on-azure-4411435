

## Challenge solution: Limit container outbound access

### Verify outbound connectivity on port 80
```
az container exec -g $group --name $aciname --container-name $aciname --exec-command "/bin/bash"

curl http://www.google.com

exit
```

### Set variables
```
$group = "DockerOnAzureCourse-HOL-RG"
$location = "eastus"
$vnet = "DOAz-VNet"
$acisubnet = "aci-subnet"
$acisubnetnsg = "aci-subnet-nsg"
```

### Create a NSG rule to block outbound traffic on ports 80 and 443
```
az network nsg rule create --name "Block-Outbound-HTTP-HTTPS" --nsg-name $acisubnetnsg --priority 1000 --resource-group $group --access Deny --source-address-prefixes "*" --source-port-ranges "*" --destination-address-prefixes "*" --destination-port-ranges 80, 443 --protocol Tcp --direction Outbound
```

### Verify NSG effectiveness (Wait for some minutes )
```
az container exec -g $group --name $aciname --container-name $aciname --exec-command "/bin/bash"

curl http://www.google.com

exit
```

































