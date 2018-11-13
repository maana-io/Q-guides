# System Bootstrap/Verify Install

### Bootstrap the System with a GraphQL Request

* Find the ID of the Consul Node running on the Master, and start a shell in it.

`docker ps   
docker exec -it  /bin/sh`

* Run the bootstrap command:

```text
curl -v -H "Content-Type: application/json" -X POST -d "{\"query\": \"mutation { bootstrapSystem }\"}" http://maana-admin:8020/graphql
```

* Be Patient

This takes a few minutes, watch for completion in the maana-admin logs:

```text
docker service logs azureDeploy_maana-admin -f
```

The logs will end with something like:

```text
azureDeploy_maana-admin.1.tkz0nuqy7llg@swarmm-master-15097170-0    | [io.maana.admin] Finished bootstrapping Maana!! (139.207 seconds)
```

## Verify the Installation

```text
https:<cluster name>
```

### Update Auth0

Login and add the callback it complains about to the Auth0 Callback section \(you can just click on the link\).   Reopen the page and login.

