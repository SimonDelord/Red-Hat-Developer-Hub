
This is the configuration for AAP.

## Configuration of AAP as an Operator

- Go to Operator Hub
- Select AAP as an operator 

![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/AAP-Configuration/images/AAP-in-Operator-Hub.png)
AAP in the Operator Hub console

- Deploy the defaults for the Operator
![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/AAP-Configuration/images/Deploy-AAP-Operator.png)
Deploy AAP as an Operator


- Create an Automation Controller Instance by clicking on Controller Instance & Use the defaults (or change the name like I did) then click Create
![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/AAP-Configuration/images/AAP-Automation-Controller.png)
![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/AAP-Configuration/images/AAP-Instance.png)
Deploy the instance of AAP running on OCP

- Wait for a few minutes and it should come up. A route will come up giving you access to the URL for AAP. You will need this url for the configuration of dynamic plugins in RHDH.
In this example, the route is https://aap-simon-aap.apps.bswptuqm.eastus.aroapp.io
![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/AAP-Configuration/images/AAP-Route.png)
URL For AAP on OpenShift



## Configuration of AAP for receiving requests from RHDH

- Check the default admin password for AAP - go into the OpenShift Console -> Workloads -> Secrets -> admin
![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/AAP-Configuration/images/AAP-Admin-Secret.png)
Default Admin Password for AAP on OpenShift

- Provide the users credentials and right SKU selection as part of the default loading
Click on the username/password and enter the values and then proceed through a couple of screens by clicking next.

![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/AAP-Configuration/images/AAP-SKU.png)
Setup for AAP SKU 

On success you should be redirected to the AAP Dashboard.

- From the AAP Dashboard, Go into Administration -> Applications -> Add
Fill in the relevant fields for the Application as per the screenshot below
![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/AAP-Configuration/images/AAP-Application-Setup.png)
![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/AAP-Configuration/images/AAP-Application-Setup-2.png)
Setup Application on AAP

We're almost there. Now we finally need to create the Token in the user. 
I will use the Admin user which is probably not a great idea, but for simplicity, I'll stick with that.

- From the AAP Dashboard, Go into Access -> Users -> Click on the user you want to create a token for (Admin in my case)
Follow the screenshots below to create this token (don't forget to write down the value as you'll need it for the setup in RHDH).
![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/AAP-Configuration/images/AAP-Token-step-1.png)
![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/AAP-Configuration/images/AAP-Token-step-2.png)
![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/AAP-Configuration/images/AAP-Token-step-3.png)
![Browser](https://github.com/SimonDelord/Red-Hat-Developer-Hub/blob/main/AAP-Configuration/images/AAP-Token-step-4.png)
Token setup on AAP



