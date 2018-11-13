---
description: 'Azure Docker Container, Configuration, Firewall rules,'
---

# Provisioning the Cluster

### Azure Docker Container 

The Azure Docker Container is not required if you have the Azure CLI tools installed, but guarantees the Azure commands will behave as described in the following sections.

#### Starting the Azure CLI Docker Container and Login 

From the directory containing this file:

`docker run -t -d --rm --name azcli_template -v pwd:/data azuresdk/azure-cli-python   
docker exec -it azcli_template /bin/bash` 

#### Login and Select the Subscription:

`az login  
az account set --subscription <Subscription ID or Name>`  

#### Create the Resource Group for the Deployment 

Export the environment variables to make it easier to use them in the latter steps, and create the group.

`export RESOURCEGROUP= <resource group name>  
export LOCATION= <Region Name (example: centralUS)>  
az group create --name $RESOURCEGROUP --location $LOCATION` 

### Cluster Configuration 

#### Edit the Parameters File 

`vi parametersFile.json` 

We recommend machines with at least 50 GB's of RAM and available SSD, currently we test internally on "Standard\_E8s\_v3". By default a single data disk is added to each agent node, the size of which can be adjusted in the parameter file, additional disks can be added to the agent boxes, and to gluster as required. 

The default configuration is for a distributed Gluster cluster \(no replication\).  If failover is required, the configuration can be adjusted in the Ansible Playbook that mounts Gluster. See the Gluster Administration Guides for configuration options, and general administration guidelines.

### Start the Cluster 

This will usually take between 5 and 15 minutes.

`az group deployment create -g $RESOURCEGROUP --template-file "./template.json" --parameters "./parametersFile.json"` 

### Add the Firewall Rules

If you don't need to whitelist IP address ranges, you can omit the creation of the security group. It can also be created in the UI later.  The only requirement is that both the loadbalancer public IPs must be whitelisted for all incoming ports in the incoming rules.

{% hint style="info" %}
**Note**:  If you do not need to install this, please skip to the Install the **Docker Azure:Cloud Plugin** section that follows.
{% endhint %}

#### Get the IP address's of the load balancers 

You can do this from the Azure UI, but the following commands will return them:

`az network public-ip list -g $RESOURCEGROUP --query "[].[name, ipAddress]"` 

#### Create a Network Security Group for the Network 

Again we'll use an environment variable to make it easier to specify consistently:

`export SECURITYGROUP= <security group name>  
az network nsg create --resource-group $RESOURCEGROUP --name $SECURITYGROUP` 

#### Whitelist the Machines in the Cluster 

Edit the IP addresses below to match the addresses returned in the previous step.   
**TO DO - Check names of loadbalancer**

```text
az network nsg rule create --resource-group $RESOURCEGROUP --nsg-name $SECURITYGROUP --name externalLB1 --access allow --protocol "*" --direction inbound --priority 100 --source-address-prefix <SSH IP address>/32 --source-port-range "*" --destination-address-prefix "*" --destination-port-range "*"
az network nsg rule create --resource-group $RESOURCEGROUP --nsg-name $SECURITYGROUP --name externalLB2 --access allow --protocol "*" --direction inbound --priority 101 --source-address-prefix <public IP addres>/32 --source-port-range "*" --destination-address-prefix "*" --destination-port-range "*"
```

#### Whitelist Maana Bellevue 

Skip if unnecessary.  Add any additional IP ranges as required.

```text
az network nsg rule create --resource-group $RESOURCEGROUP --nsg-name $SECURITYGROUP --name Bellevue --access allow --protocol "*" --direction inbound --priority 150 --source-address-prefix 198.233.204.202/32 --source-port-range "*" --destination-address-prefix "*" --destination-port-range "*" 
```

#### Add the Security Group to the VNet 

* The VNet name can be obtained with the following:

`az network vnet list -g $RESOURCEGROUP --query "[].name"` 

* Then add the sec group to the agents subnet:

`az network vnet subnet update --vnet-name  --resource-group $RESOURCEGROUP --name swarmm-agentpublicsubnet --network-security-group $SECURITYGROUP` 

