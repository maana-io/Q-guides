# Installation and Setup

## Prerequisites

We use Auth0 to provide authentication and authorization.  Auth0 provides:

* Account configuration with a source of users
* Sufficient privileges to add a callback address
* Single pages application in Auth0

{% hint style="info" %}
**Note**:  **If you are upgrading from v3.1.1**-  
Clusters prior to 3.1.2 did not set some client flags when mounting the gluster volumes, these are required on later deployments. _Not setting them can result in corrupted data._ To update an existing v3.1.1 cluster, the gluster volume mount must be updated in /etc/fstab for each node as follows:  
`:/saq_volume /maana/saq glusterfs defaults,_netdev,attribute-timeout=0,entry-timeout=0,direct-io-mode=enable 0` The only change here is adding `,attribute-timeout=0,entry-timeout=0,direct-io-mode=enable`   
to the existing entry. Once this has been changed on all of the nodes, with the cluster stopped, the following should be run to remount the drives with the correct options.  
`ansible all -i scripts/d.py -m shell -a "sudo umount /maana/saq"   
ansible all -i scripts/d.py -m shell -a "sudo mount /maana/saq"`
{% endhint %}

### Free Accounts to use

The accounts shown below are all free to create, and are recommended to be set up prior to using Maana:

* [Github](https://github.com/) – Utilized for cloning repos and logging into Launchpad. web-based hosting
* [Heroku](https://signup.heroku.com/) – Utilized for hosting the service we code on the cloud.  cloud platform as a service \(PaaS\)

### Recommended Installs

The following installs are recommended prior to using Maana:

* Google Chrome \(preferred browser\)
* Python 3.6
* NodeJs 9.3 \(and NPM\)

### GitHub Repos to Clone

* The Command Line Interface we use to programmatically interact with the platform: [https://github.com/maana-io/Q-tutorials.git](https://github.com/maana-io/Q-tutorials.git)
* The Python template for creating a Maana service: [https://github.com/maana-io/Q-ksvc-templates.git](https://github.com/maana-io/Q-ksvc-templates.git)

### Launchpad Examples to Fork \(requires above GitHub account\)

* Introductory Example: [https://launchpad.graphql.com/mpjk0plp9](https://launchpad.graphql.com/mpjk0plp9)
* Weather Example:  [https://launchpad.graphql.com/r9xzw8zxmn](https://launchpad.graphql.com/r9xzw8zxmn)

### Subjects for Prior Review

It is suggested that a review of the following areas be made prior to utilizing Maana: 

* **GraphQL intro concepts**:
  * High level introduction: [https://www.howtographql.com/basics/0-introduction/](https://www.howtographql.com/basics/0-introduction/)
  * Developer focused: [https://www.youtube.com/watch?v=Y0lDGjwRYKw&list=PL4cUxeGkcC9iK6Qhn-QLcXCXPQUov1U7f](https://www.youtube.com/watch?v=Y0lDGjwRYKw&list=PL4cUxeGkcC9iK6Qhn-QLcXCXPQUov1U7f)
* **Readme for Graphene Library**.  ****This is the Python tool used to create GraphQL endpoints for Python services: [https://github.com/graphql-python/graphene](https://github.com/graphql-python/graphene)

## 

