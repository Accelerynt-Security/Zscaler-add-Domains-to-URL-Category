# AS-Zscaler-Integration

Author: Accelerynt

For any technical questions, please contact info@accelerynt.com

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAccelerynt-Security%2FAS-Zscaler-Integration%2Fmaster%2Fazuredeploy.json)
[![Deploy to Azure Gov](https://aka.ms/deploytoazuregovbutton)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAccelerynt-Security%2FAS-Zscaler-Integration%2Fmaster%2Fazuredeploy.json)

This ARM template will deploy an Integration Account and a Playbook. It will pull domains from Sentinel Incidents and add them to a Zscaler URL Category of your choice. URL Categories can be configured to Block and Allow domains in the Zscaler Access Policies. This is extremely useful when a Sentinel Incident identifies a malicious domain communication and you want to quickly add it to your Zscaler Block list. 

# 
### Setup

**Background**:

Subdomains are parsed from a url string by first removing the following substrings, if found: "https://", "http://", and "ww<span></span>w.". 
The result is saved in a new string and then a search is performed on said string for the first occurring "/". 
If found, all characters starting at this position to the end of the string are removed.

For example:

**ht<span></span>tps://w<span></span>ww.example.<span></span>com/example-subfolder/gate.php**

would result in:  

**.example.com**

The **.** before the TDL and subdomains is added after parsing as a wildcard per Zscaler documentation to include subdomains of the TLD.  

**ZScaler**:

Before deployment will need to configure the Zscaler API Roles, API Admin Accounts, and gather the necessary information to fill in the ARM deployment template.  

For this Playbook to add URLs to your Zscaler custom domain list, you will need a local ZScaler admin account that has access to read and modify policy. To limit the access to the account we will need to make a Custom Administrator Role. 

In the Zscaler administration console hover over **Administration then click on Role Management**.

![](Images/zgit1.png)

Click on **Add Administrator Role**.

![](Images/zgit1a.png)

Create a **Name** for the **Administrator Role** and use the settings **exactly** as depicted in the image below. Then click the **Save** button.

![](Images/zgit2.png)

Hover over the **Activation** button and click **Activate** to enable the new Role.

![](Images/Activate.png)

Next, hover over **Administration** and click on **Administrator Management**.

![](Images/zgit4.png)

Click on **Add Administrator**.

![](Images/zgit5.png)

Enter a **Login ID** for the API Administrator account you want to create. 
In the **Email** box you can enter a service account that exists in your domain for this purpose or just make up an email that you will not use in your domain. There is no need for email access for this account.
Enter a name for the account and in the drop-down box below that select the Role you created in the previous step.
Make sure **Password Based Login** is checked and create a secure password for this API account.
The **email address and password** will be required when deploying the ARM template.
Click on **Save** then hover over the **Activation** button and click **Activate** to enable the new Administrator Account.

![](Images/zgit6.png)

![](Images/Activate.png)

To get your **API key** hover over the **Administration** button and click on **API Key Management**.

![](Images/zgit7.png)

Here you will find you **API Key** as well as **Zscaler Instance Name.** You will need **both** when deploying the ARM template. The typical instance names are Zscaler, ZscalerOne, ZscalerTwo, ZscalerThree, and ZsCloud.

![](Images/zgit8.png)

The last thing we will need from the Zscaler Portal is the Custom URL category. Hover over the **Administration** button and click on **URL Categories.** You will need to copy down the **exact** name of the Custom URL category you want the domains from the Sentinel Incident added to. In the example below we use name of our Custom category AS_Blocklist. This category in our test environment configured to a Zscaler access policy that disallows users access to any domain in in that list.

![](Images/URLcat.png)


#
### Requirements

You will need the following items to enter into the template settings during deployment: 

* URL for your Zscaler instance
* A Zscaler Local Admin Account configured for this API access
* The Username/Password Local Administrator API account in your Zscaler environment
* The API key for your Zscaler instance
* The name of the Zscaler Custom URL Category you wish to add the domains to


#
### Deployment                                                                                                         
                                                                                                        
To configure and deploy this playbook:

Open your browser and ensure you are logged into your Microsoft Sentinel workspace. In a separate tab, open the link to our playbook on the Arbala Security GitHub Repository:

https://github.com/Accelerynt-Security/AS-Zscaler-Integration

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAccelerynt-Security%2FAS-Zscaler-Integration%2Fmaster%2Fazuredeploy.json)
[![Deploy to Azure Gov](https://aka.ms/deploytoazuregovbutton)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAccelerynt-Security%2FAS-Zscaler-Integration%2Fmaster%2Fazuredeploy.json)

This deployment template contains two resources: An integration account and a logic app. The integration account is a requirement for executing javascript code, which is an operation used by the logicapp.

Click the “**Deploy to Azure**” button at the bottom and it will bring you to the custom deployment template.

In the **Project Details** section:

* Select the “**Subscription**” and “**Resource Group**” from the dropdown boxes you would like the playbook deployed to.  

In the **Instance Details** section:  
                                                  
* **Playbook Name**: This can be left as "**AS-Zscaler-Integration**" or you may change it. 

* **IntegrationAccountName**: Enter the name of an integration account created in the same resource group.

* **ZscalerURL**: Enter your Zscaler tenant URL here.

* **Zscaler Username**:  Enter the username of the Zscaler Admin account. 

* **Zscaler Password**: Enter the password of the Zscaler Admin account. 

* **Zscaler API Key**: Enter the API Key of your Zscaler org. 

* **Zscaler Custom URL Category Name**: Enter a Zscaler Custom URL Category Name. 

Towards the bottom, click on “**Review + create**”. 

![Template](Images/template.png)

Once the resources have validated, click on "**Create**".

The resources should take around a minute to deploy. Once the deployment is complete, you should see two resources. One is an Integration Account and the other is the Logic App.
Click the "**Go to resource group**" button.

![Success](Images/success.png)

Click the logic app resource.

![Click](Images/logicappclick.png)

Click on the “**Edit**” button. This will bring us into the Logic Apps Designer.

![Edit](Images/logicappedit.png)

Click on the bar labled “**Connections**”.  

![Logicapp1](Images/logicapp1.png)

Here you can select an existing connection or create a new one.

![Logicapp2](Images/logicapp2.png)

Click the "**Save**" button.

![Logicapp3](Images/logicapp3.png)

#
### Running the Playbook

To run your new Logic App, navigate to the Microsoft Sentinel workspace you used during deployment.

Here you can select an Incident that has an associated URL. Note that this Logic App will not work if no URL is associated with the Incident (item 2). 

Click the "**View full details**" button.

![run1](Images/run1.png)

Click the "**View playbooks**" link.

![run2](Images/run2.png)

Click the "**Run**" button.

![run3](Images/run3.png)

As you can see below - the domain/subdomains were parsed from the URL Entity in the Incident and added to the the Custom URL Category. 

![](Images/gitinc.png)
![](Images/gitinc1.png)
