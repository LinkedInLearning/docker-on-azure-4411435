

### Set variables
```
$group = "DockerOnAzureCourse-HOL-RG"
$location = "eastus"
$vnet = "DOAz-VNet"

$acrname = $(az acr list --resource-group $group --query [0].name --output tsv)
$acrloginserver = $(az acr show --name $acrname --query loginServer)
$acrusername = $(az acr credential show --name $acrname --query username)
$acrpassword = $(az acr credential show --name $acrname --query 'passwords[0].value')
```

### Create vNet and subnet
```
az network vnet create -n $vnet -g $group --address-prefix 10.1.0.0/16 --subnet-name "vm-subnet" --subnet-prefix 10.1.1.0/24
az network vnet subnet create -n "aci-subnet" --address-prefixes 10.1.2.0/24 -g $group --vnet-name $vnet
```

### Deploy a virtual machine into a subnet
```
az vm create -g $group -n LinuxVM --image UbuntuLTS --admin-username "azureuser" --generate-ssh-keys --vnet-name $vnet --subnet vm-subnet --size Standard_D2s_v3 

az vm open-port --port 22 --resource-group $group --name LinuxVM
```

### Deploy a container instance into a subnet
```
$random = Get-Random
$aciname = "ubuntucontainer$random"

az container create --resource-group $group --name $aciname --image nginx --vnet $vnet --subnet aci-subnet --ports 80 443

az container create --resource-group $group --name $aciname --image ubuntu --vnet $vnet --subnet aci-subnet --command-line "tail -f /dev/null" --registry-login-server $acrloginserver --registry-username $acrusername --registry-password $acrpassword --ports 22
```

### Verify private access to the container instance
```
* Get the container instance private IP
$aciIP = $(az container show --resource-group $group --name $aciname --query ipAddress.ip -o tsv)

echo $aciIP # Make a note of this IP address

* Get the VM's public IP and connect to it
$LinuxVM = $(az vm show -d --resource-group $group --name LinuxVM --query publicIps -o tsv)

echo $LinuxVM

ssh azureuser@$LinuxVM

* Connect to the container instance privately
curl http://<container_private_IP>

* Exit from the VM
exit
```