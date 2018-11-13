# Installation,  Docker Azure:Cloud Plugin

### Install the Docker Azure:Cloud Plugin

#### Create the Storage Account

{% hint style="info" %}
**Note**:  The name of a Storage account must be globally unique within Azure - so select a name that is unlikely to have a collision.
{% endhint %}

```text
az storage account create -g $RESOURCEGROUP -n <storage account name> --sku Standard_LRS --kind Storage
```

#### Get the Credentials to Connect to it

```text
az storage account keys list -n <storage account name> -g $RESOURCEGROUP --query "[0].value"
```

#### Exit the Container 

Add the Secure Shell \(SSH\) private key to the agent.  This allows you to forward the private key, so you can add the SSH to agent nodes on the cluster.

```text
ssh-add <private key file name>
```

{% hint style="warning" %}
**Warning**:  If for some reason you _do not_ wish to do this, you must copy the private key to the master node.  ****I**f you do not copy the private key to the master node, the remaining provisioning steps will fail.**
{% endhint %}

#### Install the Docker Cloud Driver on all of the Nodes

```text
ssh -A docker@<ssh lb ip address> "ansible all -i scripts/d.py -m shell -a 'docker plugin install --alias cloudstor:azure --grant-all-permissions docker4x/cloudstor:18.03.1-ce-azure1 CLOUD_PLATFORM=AZURE AZURE_STORAGE_ACCOUNT_KEY=\"<Account Key>\" AZURE_STORAGE_ACCOUNT=\"<storage account name>\"'"
```

#### Verify the Install

```text
ssh -A docker@<ssh lb ip address> "ansible all -i scripts/d.py -m shell -a 'docker plugin ls'"
```

#### All Nodes should show the Plugin enabled as indicated below:

```text
swarmm-agentpublic-15097170000003 | SUCCESS | rc=0 >>
ID                  NAME                DESCRIPTION                       ENABLED
18df342986d2        cloudstor:azure     cloud storage plugin for Docker   true
swarmm-agentpublic-15097170000004 | SUCCESS | rc=0 >>
ID                  NAME                DESCRIPTION                       ENABLED
e16821ad5629        cloudstor:azure     cloud storage plugin for Docker   true
swarmm-agentpublic-15097170000002 | SUCCESS | rc=0 >>
ID                  NAME                DESCRIPTION                       ENABLED
4d983ba58907        cloudstor:azure     cloud storage plugin for Docker   true
swarmm-master-15097170-0 | SUCCESS | rc=0 >>
ID                  NAME                DESCRIPTION                       ENABLED
6ccd18e0db46        cloudstor:azure     cloud storage plugin for Docker   true
```

