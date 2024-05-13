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
