# Installation, SSL Cert and NGinX Config

### Install the SSL Cert and NGinX Configuration

{% hint style="info" %}
**Note**:  You _cannot_ use a self-signed certification.  Many of the services will not accept it.
{% endhint %}

Copy the certs to the cluster in the /etc/keys directory.  Copy the NGinX Config file to the /etc/NGinX directory, then edit the NGinX config file.

**OUTSTANDING - TO DO**

SSL can be disabled in the NGinX Config if a cert is unavailable. 

**OUTSTANDING - TO DO**

#### Add a DNS Entry that matches the Cert for the Public IP address

This depends greatly on corporate infrastructure, for internal testing we use Azure to host DNS zones, and add a new cluster public IP address to a zone that it matches a wildcard cert. It can take up to 30 minutes for a DNS entry to become visible, but usually only requires a few seconds. 

Verify that the DNS entry you have added is visible by pinging the cluster.  It will not respond, but you should see the IP address resolve.

```text
ping <cluster name>
```

