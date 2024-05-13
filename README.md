# Red-Hat-Developer-Hub
This is a repo with some of the RHDH activities


In this repo we are describing the architecture for deploying:
- AAP: via an operator, then an AAP instance and then create a Token (for a specific user within an AAP "Application) to be used by RHDH to connect to AAP
- RHDH: via an Operator and then the configuration of a Helm Chart with the relevant plugins
- RBAC on RHDH using Config-maps
- The creation of a golden template that will make use of the AAP capabilities.
