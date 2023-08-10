

### Set variables
```
$group = "DockerOnAzureCourse-HOL-RG"
$location = "eastus"
$vnet = "DOAz-VNet"
$acisubnet = "aci-subnet"
$acisubnetnsg = "aci-subnet-nsg"
```

### Create a Network Security Group (NSG)
```
az network nsg create --name $acisubnetnsg --resource-group $group --location $location
```

### Create a NSG rule to block inbound traffic on port 80
```
az network nsg rule create --name "Block-Inbound-HTTP" --nsg-name $acisubnetnsg --priority 1000 --resource-group $group --access Deny --source-address-prefixes "*" --source-port-ranges "*" --destination-address-prefixes "*" --destination-port-ranges "80" --protocol Tcp --direction Inbound
```

### Attach the NSG to the ACI subnet
```
az network vnet subnet update -g $group -n $acisubnet --vnet-name $vnet --network-security-group $acisubnetnsg
```

### Validate security rules
```
* Get the VM's public IP and connect to it
$LinuxVM = $(az vm show -d --resource-group $group --name LinuxVM --query publicIps -o tsv)

echo $LinuxVM

ssh azureuser@$LinuxVM

* Attempt to connect to the container instance privately (this should fail)
curl http://<container_private_IP>

* Exit from the VM
exit
```





























