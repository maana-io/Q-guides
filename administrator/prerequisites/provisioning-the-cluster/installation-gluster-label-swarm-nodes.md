# Installation, Gluster/Label Swarm Nodes

### Install Gluster/Label Swarm Nodes with Roles

This is done through an Ansible Playbook .ssh to Master Node, and run the Ansible Playbook.

{% hint style="info" %}
**Note**:  You must specify agent forwarding \(-A\), or copy the private key to the master.
{% endhint %}

```text
ssh -A docker@<ssh lb ip address>
ansible-playbook -i scripts/d.py scripts/mountdisks.yml
```

#### Validate as Follows:

```text
cat /proc/mounts
```

Your line should appear to be similar to the one shown below: 

```text
swarmm-agentpublic-15097170000004:/saq_volume /maana/saq fuse.glusterfs rw,relatime,user_id=0,group_id=0,default_permissions,allow_other,max_read=131072 0 0
```

