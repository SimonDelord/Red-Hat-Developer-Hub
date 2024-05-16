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

Installing Red Hat Developer Hub from the Operator Hub Console

- The next step is to actually create a Developer Hub instance on the OpenShift cluster and create the dynamic plugin to access AAP.
For this activity, I will use the Helm Chart and do the modifications for the dynamic plugin as part of the install.

On the OCP Console, go to Developer View, change the project to rhdh-operator.

### Installing the dynamic Plugins - AAP 

To install the dynamic plugin, we need to figure out both the package and the associated sha-512 for it.

The current package for AAP is @janus-idp/backstage-plugin-aap-backend@1.6.5
And the associated sha-512 is sha512-3jMQXrX6vEYZpkVWlF1z7L4KiaQwJlSJrjfZAQQhdmIlH/uGuz32aJvq42T0CsTvi/uD52TqyG88r1TCuRidfw==

The way to do this is to run the following commands (from your laptop or a jumphost)

- npm info @janus-idp/backstage-plugin-aap-backend@1.6.5 dist.integrity

![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/images/SHA-AAP-plugin-new.png)

Finding the SHA-512 from AAP plugin

### Modifying the helm chart

In the Developer view of OCP console, go to Add -> Helm Chart -> Select Developer Hub

![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/images/RHDH-Helm-Install.png)

Deploying RHDH via Helm Chart

You then need to modify the YAML view of it and add the following to the YAML configuration.
Find and replace the plugins []  and replace it with (remember to modify the url, the Token and the sha value).



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

You can just click Create and the helm chart will deploy.


## Start using RHDH

You can first check once the Route for RHDH is available, the plugins by checking the following url - $HOST/api/dynamic-plugins-info/loaded-plugins,
so in this demo https://simon-aap-aap.apps.h28yrboo.eastasia.aroapp.io/api/dynamic-plugins-info/loaded-plugins

![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/images/RHDH-List-Plugins.png)

View of the Dynamic Plugins configured on RHDH

 
