# Red-Hat-Developer-Hub
This is a repo with some of the RHDH activities


In this repo we are describing the architecture for deploying:
- AAP: via an operator, then an AAP instance and then create a Token (for a specific user within an AAP "Application) to be used by RHDH to connect to AAP
- RHDH: via an Operator and then the configuration of a Helm Chart with the relevant plugins
- RBAC on RHDH using Config-maps
- The creation of a golden template that will make use of the AAP capabilities.


## Overall Architecture

The figure below shows the high level implementation of the solution. 
TBD

## AAP Install and Configuration

The detailled installation and configuration of AAP can be found under the AAP-Configuration folder 
[here](https://github.com/SimonDelord/Red-Hat-Developer-Hub/tree/main/AAP-Configuration)

Just make sure to write down both the URL for accessing AAP as well as the Token you generated. 

## Figure out the various 

## RHDH Install and Configuration

- Go to Operator Hub
- Select Red Hat Developer Hub as an Operator
- Deploy the defaults for the Operator
![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/images/RHDH-Operator-Hub.png)
![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/images/RHDH-Deploy-Operator.png)

<p align=center>  Installing Red Hat Developer Hub from the Operator Hub Console </p>

- The next step is to actually create a Developer Hub instance on the OpenShift cluster and create the dynamic plugin to access AAP.
For this activity, I will use the Helm Chart and do the modifications for the dynamic plugin as part of the install.

On the OCP Console, go to Developer View, change the project to rhdh-operator.

### Installing the dynamic Plugins - AAP & RBAC

To install the dynamic plugin, we need to figure out both the package and the associated sha-512 for it.

The current package for AAP is @janus-idp/backstage-plugin-aap-backend@1.6.5

And the associated sha-512 is 

sha512-3jMQXrX6vEYZpkVWlF1z7L4KiaQwJlSJrjfZAQQhdmIlH/uGuz32aJvq42T0CsTvi/uD52TqyG88r1TCuRidfw==

The way to do this is to run the following commands (from your laptop or a jumphost)

- npm info @janus-idp/backstage-plugin-aap-backend@1.6.5 dist.integrity

![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/images/SHA-AAP-plugin-new.png)

<p align=center>  Finding the SHA-512 from AAP plugin </p>

For the RBAC component do the same. 

I'm putting below an example of screenshot for it.

![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/images/SHA-RBAC-plugin-new.png)

<p align=center>  Finding the SHA-512 from RBAC plugin </p>


### Modifying the helm chart

In the Developer view of OCP console, go to Add -> Helm Chart -> Select Developer Hub

![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/images/RHDH-Helm-Install.png)

<p align=center>  Deploying RHDH via Helm Chart </p>

You then need to modify the YAML view of it and add the following to the YAML configuration.
Find and replace the plugins []  and replace it with (remember to modify the url, the Token and the sha value).


```
  plugins:
    - disabled: false
      integrity: >-
           sha512-3jMQXrX6vEYZpkVWlF1z7L4KiaQwJlSJrjfZAQQhdmIlH/uGuz32aJvq42T0CsTvi/uD52TqyG88r1TCuRidfw==
      package: '@janus-idp/backstage-plugin-aap-backend@1.6.5'
      pluginConfig:
        catalog:
          providers:
            aap:
              dev:
                baseUrl: https://simon-aap-aap.apps.h28yrboo.eastasia.aroapp.io/
                authorization: 'Bearer LMkbvY88rEFhJ791sMZOheYHFISSF9'
                owner: owner
                system: system
                schedule: 
                  frequency: { minutes: 1 }
                  timeout: { minutes: 1 }
    - disabled: false
      integrity: sha512-zAKDnvMUv6zwMLx6GIrQDZ+M0D2pcqbZTgG7xqvPJj15j2ZuoJLPmLH7zQM9hdDfKWag0cqoPlP27139klr50Q==
      package: './dynamic-plugins/dist/janus-idp-backstage-plugin-rbac'

  host: 'redhat-developer-hub-rhdh-operator.apps.bswptuqm.eastus.aroapp.io'
```
You can just click Create and the helm chart will deploy.


## Start using RHDH

You can first check once the Route for RHDH is available, the plugins by checking the following url - $HOST/api/dynamic-plugins-info/loaded-plugins,
so in this demo https://simon-aap-aap.apps.h28yrboo.eastasia.aroapp.io/api/dynamic-plugins-info/loaded-plugins

![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/images/RHDH-List-Plugins-new.png)

<p align=center>  View of the Dynamic Plugins configured on RHDH </p>

### Create the Admin user in RHDH

This is a bit of tricky one. As it requires to add/modify some of the existing artefacts in the current RHDH install (e.g I'm too lazy to wrap it all up into a nice neat deployment, apologies).

We will use GitHub as the Auth method. 
Please create your own access from GitHub to developer Hub by following this tutorial until Step 13.
[here](https://developers.redhat.com/learning/learn:openshift:install-and-configure-red-hat-developer-hub-and-explore-templating-basics/resource/resources:configure-github-access-red-hat-developer-hub)

You then need to add a couple of configMaps that define the "Admin User" as well as the Permissions for it.

```
kind: ConfigMap
apiVersion: v1
metadata:
  name: developer-hub-rbac-config
  namespace: rhdh-operator
data:
  rhdh-config-rbac.yaml: |
    permission:
      enabled: true
      rbac:
        policies-csv-file: ./rbac-policy.csv
        admin:
          users:
            - name: user:default/simondelord
```

```
kind: ConfigMap
apiVersion: v1
metadata:
  name: developer-hub-rbac-policy
  namespace: rhdh-operator
data:
  rbac-policy.csv: |
    p, role:default/team-x, catalog-entity, read, allow
    p, role:default/team-x, catalog.entity.create, create, allow
    p, role:default/team-x, catalog.entity.create, read, allow
    p, role:default/team-x, catalog.entity.delete, delete, deny
    p, role:default/team-x, catalog.location.create, create, allow
    p, role:default/team-x, catalog.location.read, read, allow
    p, role:default/team-x, policy-entity, create, allow
    p, role:default/team-x, policy-entity, read, allow

    g, user:default/simondelord, role:default/team-x
```

You also need to update the ConfigMap redhat-developer-hub-app-config with the following extra (the tabulation is at the same level as backend)

```
app:
  title: Hacking your way around Developer Hub
  baseUrl: https://redhat-developer-hub-rhdh-operator.apps.bswptuqm.eastus.aroapp.io/
integrations:
  github:
    - host: github.com
      token: GITHUB-TOKEN
auth:
  allowGuestAccess: true
  environment: development
  providers:
    github:
      development:
        clientId: GIT-HUB-CLIENT-ID
        clientSecret: GIT-HUB-CLIENT-SECRET
permission:
  enabled: true
  rbac:
    admin:
      users:
        - name: user:default/simondelord
backend:
  auth:
    keys:
    - secret: ${BACKEND_SECRET}
  baseUrl: https://redhat-developer-hub-rhdh-operator.apps.bswptuqm.eastus.aroapp.io
  cors:
    origin: https://redhat-developer-hub-rhdh-operator.apps.bswptuqm.eastus.aroapp.io
  database:
    connection:
      password: ${POSTGRESQL_ADMIN_PASSWORD}
      user: postgres
```

Now under the OCP Console in the Deployment Tab add the following code (in the volume section add both developer-hub-rbac-policy and developer-hub-rbac-config

```
     volumes:
        - name: dynamic-plugins-root
          ephemeral:
            volumeClaimTemplate:
              metadata:
                creationTimestamp: null
              spec:
                accessModes:
                  - ReadWriteOnce
                resources:
                  requests:
                    storage: 2Gi
                volumeMode: Filesystem
        - name: dynamic-plugins
          configMap:
            name: dynamic-plugins
            defaultMode: 420
            optional: true
        - name: dynamic-plugins-npmrc
          secret:
            secretName: dynamic-plugins-npmrc
            defaultMode: 420
            optional: true
        - name: backstage-app-config
          configMap:
            name: redhat-developer-hub-app-config
            defaultMode: 420
        - name: backstage-developer-hub-rbac-config
          configMap:
            name: developer-hub-rbac-config
            defaultMode: 420
        - name: backstage-developer-hub-rbac-policy
          configMap:
            name: developer-hub-rbac-policy
            defaultMode: 420
      dnsPolicy: ClusterFirst
```

This should redeploy the PoD and then after it restarts (without any error hopefully), you should be able to login using your GitHub credentials.


 
