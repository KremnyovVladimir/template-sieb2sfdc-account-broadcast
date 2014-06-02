
# Anypoint Template: template-sieb2sfdc-account-broadcast

+ [License Agreement](#licenseagreement)
+ [Use Case](#usecase)
+ [Run it!](#runit)  
	* [Running on premise](#runonopremise)
    * [Running on CloudHub](#runoncloudhub)
	* [Properties to be configured](#propertiestobeconfigured)
+ [Customize It!](#customizeit)
    * [config.xml](#configxml)
    * [endpoints.xml](#endpointsxml)
    * [businessLogic.xml](#businesslogicxml)
    * [errorHandling.xml](#errorhandlingxml)
+ [Testing the Anypoint Template](#testingtheanypointtemplate)

# License Agreement <a name="licenseagreement"/>
Note that using this template is subject to the conditions of this [License Agreement](AnypointTemplateLicense.pdf).
Please review the terms of the license before downloading and using this template. In short, you are allowed to use the template for free with Mule ESB Enterprise Edition, CloudHub, or as a trial in Anypoint Studio.

# Use Case <a name="usecase"/>
This Anypoint Template should serve as a foundation for setting an online sync of accounts from Oracle Siebel Business Objects to Siebel.
Everytime there is a new account or a change in an already existing one, the integration will poll for changes from Oracle Siebel Business Objects source instance and it will be responsible for updating the account in Salesforce target instance.

Requirements have been set not only to be used as examples, but also to establish a starting point to adapt your integration to your requirements.

As implemented, this Anypoint Template leverage the [Batch Module](http://www.mulesoft.org/documentation/display/current/Batch+Processing).
The batch job is divided in Input, Process and On Complete stages.
The integration is triggered by a poll defined in the flow that is going to trigger the application, querying newest Oracle Siebel Business Objects updates/creations matching a filter criteria and executing the batch job.
During the Process stage, each Siebel Account will be filtered depending on, if it has an existing matching Account in Salesforce.
The last step of the Process stage will group the users and create/update them in Salesforce.
Finally during the On Complete stage the Anypoint Template will log output statistics data into the console.


# Run it! <a name="runit"/>

Simple steps to get Oracle Siebel Business Objects to Salesforce Account Broadcast running.


## Running on premise <a name="runonopremise"/>

In this section we detail the way you have to run you Anypoint Temple on you computer.

### Running on Studio <a name="runonstudio"/>
Once you have imported you Anypoint Template into Anypoint Studio you need to follow these steps to run it:

+ Locate the properties file `mule.dev.properties`, in src/main/resources
+ Complete all the properties required as per the examples in the section [Properties to be configured](#propertiestobeconfigured)
+ Once that is done, right click on you Anypoint Template project folder 
+ Hover you mouse over `"Run as"`
+ Click on  `"Mule Application"`


### Running on Mule ESB stand alone <a name="runonmuleesbstandalone"/> 
Complete all properties in one of the property files, for example in [mule.prod.properties] (../blob/master/src/main/resources/mule.prod.properties) and run your app with the corresponding environment variable to use it. To follow the example, this will be `mule.env=prod`.


## Running on CloudHub <a name="runoncloudhub"/>

While [creating your application on CloudHub](http://www.mulesoft.org/documentation/display/current/Hello+World+on+CloudHub) (Or you can do it later as a next step), you need to go to Deployment > Advanced to set all environment variables detailed in **Properties to be configured** as well as the **mule.env**. 

Once your app is all set and started, there is no need to do anything else. Every time a account is created or modified, it will be automatically synchronized to Salesforce by Account Name.

### Deploying your Anypoint Template on CloudHub <a name="deployingyouranypointtemplateoncloudhub"/>
Mule Studio provides you with really easy way to deploy your Anypoint Template directly to CloudHub, for the specific steps to do so please check this [link](http://www.mulesoft.org/documentation/display/current/Deploying+Mule+Applications#DeployingMuleApplications-DeploytoCloudHub)

## Properties to be configured (With examples)<a name="propertiestobeconfigured"/>
In order to use this Anypoint Template you need to configure properties (Credentials, configurations, etc.) either in properties file or in CloudHub as Environment Variables. Detail list with examples:

### Application configuration
+ poll.frequencyMillis `60000`
+ poll.startDelayMillis `0`
+ watermark.defaultExpression `#[System.currentTimeMillis() - 1000 * 60 * 60 * 24]`

#### Oracle Siebel Business Objects Connector configuration
+ sieb.user `SADMIN`
+ sieb.password `SADMIN`
+ sieb.server `192.168.10.8`
+ sieb.serverName `SBA_82`
+ sieb.objectManager `EAIObjMgr_enu`
+ sieb.port `2321`

#### Salesforce Connector configuration
+ sfdc.username `bob.dylan@org`
+ sfdc.password `DylanPassword123`
+ sfdc.securityToken `avsfwCUl7apQs56Xq2AKi3X`
+ sfdc.url `https://login.salesforce.com/services/Soap/u/28.0`


# Customize It!<a name="customizeit"/>
This brief guide intends to give a high level idea of how this Anypoint Template is built and how you can change it according to your needs.
As mule applications are based on XML files, this page will be organized by describing all the XML that conform the Anypoint Template.
Of course more files will be found such as Test Classes and [Mule Application Files](http://www.mulesoft.org/documentation/display/current/Application+Format), but to keep it simple we will focus on the XMLs.

Here is a list of the main XML files you'll find in this application:

* [config.xml](#configxml)
* [endpoints.xml](#endpointsxml)
* [businessLogic.xml](#businesslogicxml)
* [errorHandling.xml](#errorhandlingxml)


## config.xml<a name="configxml"/>
This file holds the configuration for Connectors and [Properties Place Holders](http://www.mulesoft.org/documentation/display/current/Configuring+Properties) are set in this file. 
**Even you can change the configuration here, all parameters that can be modified here are in properties file, and this is the recommended place to do it so.** 
Of course if you want to do core changes to the logic you will probably need to modify this file.

In the visual editor they can be found on the *Global Element* tab.


## endpoints.xml<a name="endpointsxml"/>
This is the file where you will found the inbound and outbound sides of your integration app.


## businessLogic.xml<a name="businesslogicxml"/>
This file holds the functional aspect of the Anypoint Template, directed by one flow responsible of conducting the business logic.

Functional aspect of the Anypoint Template is implemented on this XML, directed by one flow that will poll for SalesForce creations/updates.
Several message processors constitute four high level actions that fully implement the logic of this Anypoint Template:

1. During the Input stage the Anypoint Template will query all the existing accounts from Oracle Siebel Business Objects modified after watermark.
2. During the Process stage, each Account will be filtered depending on, if it has an existing matching user in the Salesforce.
3. The last step of the Process stage will group the users and create/update them in Salesforce.
Finally during the On Complete stage the Anypoint Template will log output statistics data into the console.


## errorHandling.xml<a name="errorhandlingxml"/>
This is the right place to handle how your integration will react depending on the different exceptions. 
This file holds a [Choice Exception Strategy](http://www.mulesoft.org/documentation/display/current/Choice+Exception+Strategy) that is referenced by the main flow in the business logic.

## Testing the Anypoint Template <a name="testingtheanypointtemplate"/>

You will notice that the Template has been shipped with test.
These are divided into two categories:

+ Unit Tests
+ Integration Tests

You can run any of them by just doing right click on the class and clicking on run as Junit test.

Do bear in mind that you'll have to tell the test classes which property file to use.
For you convenience we have added a file mule.test.properties located in "src/test/resources".
In the run configurations of the test just make sure to add the following property:

+ -Dmule.env=test