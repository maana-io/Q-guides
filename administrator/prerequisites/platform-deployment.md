# Platform Deployment

## Platform Deployment

### Preparations

* Copy the Compose file to the Cluster:

```text
scp <compose file> docker@<SSH IP address>:
```

* SSH to the Cluster:

```text
ssh -A docker@<ssh lb ip address>
```

* Login to Docker:

```text
docker login
```

### Deploy the Platform

The stack can be named anything you want \(we use azureDeploy, as shown  below\). 

{% hint style="info" %}
**Note**:  Please remember the Docker requirement that every command must be typed in.
{% endhint %}

`REACT_APP_PORTAL_AUTH_DOMAIN=  REACT_APP_PORTAL_AUTH_IDENTIFIER=  REACT_APP_PORTAL_AUTH_CLIENT_ID=  REACT_APP_CLI_AUTH_CLIENT_ID=  REACT_APP_CLI_AUTH_DOMAIN<=CLI App Domain - usually the same as prtal Auth domain>  SERVICE_AUTH_CLIENT_ID=  SERVICE_AUTH_CLIENT_SECRET=  SERVICE_AUTH_IDENTIFIER=  API_AUTH_DOMAIN=  API_AUTH_CLIENT_ID<=id of service app>  API_AUTH_CLIENT_IDENTIFIER=  PUBLICNAME=  PUBLIC_BACKEND_URI=https://:8443  docker stack deploy -c docker-compose.yml --with-registry-auth azureDeploy`

#### Be Patient

Wait for everything to come up.  Services will fail during startup.  This is normal, as long as there is at least one copy of the service in a running state at the end of startup. You can look at the current state with either:

```text
docker service ls
```

or:

```text
docker stack ps azureDeploy
```

#### Determine the System Stability

Go to the prometheus page at:

```text
http://<cluster name>:9090
```

Under status/targets, verify that saq is reporting green.  It is normal for Docker to report unavailable.

#### Metrics

Available on publicIP address port 4000, default login is admin/admin, I suggest you login and change it.

```text
http://<cluster name>:4000
```

#### **OUTSTANDING - TO DO - Update with import of dashboards**

