# No Code Watson Assistant and IBM App Connect integration sample

This is a simple [Watson Assistant](https://cloud.ibm.com/catalog/services/watson-assistant) bot that demonstrates how to call [IBM App Connect](https://cloud.ibm.com/catalog/services/app-connect) "natively" to run any number of integrations e.g. business processes, automations or external API calls and more. 

Not long ago, you would have needed to write (and therefore host and secure) code to achieve this, however that is no longer the case. You can easily create a chatbot/virtual assistant from "out of the box" [IBM Cloud](https://cloud.ibm.com) services with no custom code to manage, secure or maintain. The killer feature of the whole solution is that, once assembled, the end to end system can be managed and maintained by business users.

This is a write up of a real project I completed and, as an example, I was able to add a ServiceNow problem creation conversation to my Watson Assistant in less than 15 minutes (including the creation of the ServiceNow sandbox from https://developer.servicenow.com).

## A little about IBM App Connect
IBM App Connect allows you to "connect anything to anything" and build workflows to "do anything" between those connections. We are talking workflow/business logic and transformations basically. This really could be anything from turning an email into a problem ticket to recording the mileage of a fleet of connected vehicles and figuring out which miles should be expenses and then handling the expense process, to performing automated insurance claims based on images sent by the customer etc.

IBM App Connect can run two kinds of flows; event driven flows, and flows for APIs. This solution uses an API flow that exposes an API which acts as a router for incoming calls from Watson Assistant. 

## Why do we need this?
Watson Assistant has a powerful Webhook feature that allows native API calls from conversation dialogues, but it is limited in that only one web hook can be configured per assistant skill. It's entirely possible to have a number of skills within a Watson Assistant, but even within a single skill you may want to be able to call a number of integrations, so a router API makes sense. 

# Architecture

![Image of Architecture](/images/Architecture.png)
> Note: IBM API Connect is shown in the architecture above but not explicitly used in this tutorial. IBM App Connect actually contains API Connect functionality (we use this built in functionality to publish the API below). In an enterprise you may want to consider using the IBM API Connect service externally as shown above.

# Setup

Sign in or create an IBM Cloud account [here](https://cloud.ibm.com)

Create a Watson Assistant service on IBM Cloud [here](https://cloud.ibm.com/catalog/services/watson-assistant).
The free version can be used again. In my actual solution I used the Plus version that comes with an incredibly simple way to embed the Assistant into a web page.

Open the Management page of your Watson Assistant and click on the Assistants menu (top left) then click on Create Assistant.

![Initial WA interface](/images/initalcreateassistant.png)

Give the assistant a name.

![Assistant creation page](/images/createassistantdetails.png)

Then click on the Add dialogue skill button on the next page...

![Assistant page](/images/createassistant.png)

Select the Create Skill option at the top of the page then give the skill a name e.g.

![Skill creation page](/images/createskill.png)

This page shows the assistant settings including the skill connected to it and any Integrations (interfaces) set up. Note the Preview Link integration that was created for you. Click on the Demo Skill you created to edit the contents.

![Skill selection page](/images/editskill.png)

Here you edit the intents, entities and dialogue and other settings of your assistant. Click Create intent to create the first intent of your system.

![Create intent initial page](/images/createintentsplash.png)

Go to the Intents section. Add an intent called "#get_my_ip" and complete it as shown below.

![Image of Architecture](/images/createintent.png)

Fill in the intent as shown below.

![Image of Architecture](/images/defineintent.png)

Close the Intent (top right arrow) and click on the Dialogue menu.
Click on the Add node button in the dialogue view (shown below).

![Image of Architecture](/images/initialdialogue.png)

The first thing to do here is select the intent you just created in the box shown below.
Then turn on the webhook feature. Click on the Customise gear icon at the top right.

![Image of Architecture](/images/initialdialoguesettings.png)

The following dialogue will be shown.
Click the Webhooks slider to on.

![Image of Architecture](/images/turnonwebhooks.png)

Now, back in the dialogue node settings you will see some additional fields have appeared...

![Image of Architecture](/images/webhookdialoguesettings.png)

The parameters section allows you to send data with your webhook call. We will be creating an API that accepts a "type" parameter later. For this demo lets add the type parameter and give it a value of "getIPAddress". Any number of parameters can be sent.
The Return variable is auto populated with a name but can be changed to anything you want. This becomes a Watson Assistant context variable (called $webhook_result_1 in this example) and will be filled with all the data that is returned by the webhook call later. Context variables can be accessed and used in your dialogues.
The "Assistant responds" section is also pre-populated with a couple of values. If you change the variable name above, you'll need to change the name in this section too.
The first line basically says, "if we get a result back from the webhook (i.e. the variable is populated) then say x".
    This is the whole line: If assistant recognises `$webhook_result_1` then respond with `My IP address is <? $webhook_result_1.responseData ?>`
    The <? ?> format basically inserts a variable into the dialogue reply and can be used with any context variable used within Watson Assistant. 
The second line handles errors and Watson Assistant returns errors in a particular format, shown below.
    If assistant recognises `anything else` (i.e. an error) then reply with `The callout generated this error: <? output.webhook_error.webhook_result_1 ?>.`
    
> Note: We return two variables in our API call be build below; responseData and responseBody. We set responseData to contain the IP Address value and responseBody to contain the whole of the JSON object returned by the API we call. To display the whole JSON object `<? $webhook_result_1.responseBody ?>` can be used in the Watson Assistant dialogue.

The last part to complete in Watson Assistant is setting the webhook, but we need to create the API to call first!

We will use IBM App Connect to create the API. Within APP Connect we can then create as many integrations as we want (each called via a different "type" parameter, that can be called via that one API.

# Create an instance of IBM App Connect

To create a new instance of APP Connect, go to your cloud account and search the catalogue for App Connect or click [here](https://cloud.ibm.com/catalog/services/app-connect). Follow the setup steps and choose the Lite plan.

Once the setup completes open IBM App Connect, go to Manage and then Launch App Connect.

Go to the Dashboard section and then click the New button and select Flows for an API.
Then give your API a name and give your model a name as shown below.

![Image of Architecture](/images/apicreation.png)

Click Create Model and set your API properties. I've set mine as shown below. All the fields are required exactly as shown in this tutorial except for responseData. I used that to return raw data in my test but we don't discuss that here.

![Image of Architecture](/images/apiproperties.png)

Click on the Operations option and choose Add a custom operation.

![Image of Architecture](/images/addoperation.png)

Configure it as shown below.

![Image of Architecture](/images/operations.png)

Click the Implement Flow button. 
You will be presented with a drag and drop flow editor. Click the + between the Request and Response objects, click on the Toolbox option and add an "If (conditional)" block as shown below. 

![Image of Architecture](/images/initialifflow.png)

Click on the yellow diamond icon in the If flow and configure the If statement as shown below i.e. select the Type parameter by clicking in the first field then clicking the blue icon on the right side of the field.

![Image of Architecture](/images/ifsettings.png)

Next add an Application by clicking the + icon and selecting the http application. Note you will need to Add a new Account and then select the Invoke method. You don't need to add any details in the Account settings for this example, all the fields can be left blank because we will not be calling an API that requires authentication or any special connection details. 

![Image of Architecture](/images/ifflow1.png)

Once added, edit the settings of the http application to match those below. The API we will be calling is this one https://api.ipify.org?format=json and it just returns the IP address of the calling system in JSON format.

![Image of Architecture](/images/httpsettings.png)

Add another node to the flow by clicking the + icon to the right of the http node and selecting JSON Parser from the Toolbox list. 

> Adding a JSON Parser node is not strictly necessary but we do want to use it in this tutorial to get accross one additional point (and so that the Watson Assisant dialogue we created above works). Using it allows us to select exactly what data returned from the http node is returned by the If statement. Otherwise only the whole output of the http node (a JSON object) is returned and your Assistant will need to handle extracting the data (which may actually be desired in some situations). 

![Image of Architecture](/images/jsonparser.png)

Configure the JSON Parser node as follows:
Using the blue 3 bar icon in the field, select Response body from the HTTP Invoke method drop down (not the Request drop down).

![Image of Architecture](/images/parsesettings1.png)

Scroll down and enter an example output as shown below `{"ip":"192.168.0.1"}` and then click the generate schema button. 

> In this case we know what the example output is (because I've called it previously) but obviously you'd need find out yourself what any other API you want to call produces. This may seem fidly but it's required otherwise the flow doesn't know what data it has to map of course.

![Image of Architecture](/images/parsesettings2.png)

Back on the If flow, click the yellow If icon again. 
We are going to configure the output of the getIPAddress If statement. 

First, at the top of the If flow, click on the Output schema drop down and configure as shown below.
This is where the actual output of the whole If flow is configured.
In our case we could remove the type property if we wanted. I have left it in place.

![Image of Architecture](/images/ifoutputsettings2.png)

Then click on the Output data drop down and select the options shown below.
* responseBody comes from the http node (and contains the full JSON response of the API we called).
* type comes from the request object (ie it is passed through all the way).
* responseData comes from the JSON Parser node (and is the value of the ip key obtained from the full JSON response of the http node).

![Image of Architecture](/images/ifoutputsettings.png)

Now we need to publish the API so Watson Assistant can call it via its webhook feature.
Click the Done button and then select Manage (next to Define) from the top bar.
As it says, "To get started, scroll down to 'Sharing Outside of Cloud Foundry organization' and click on 'Create API key'. Follow the 'API Portal Link' link to explore the API in the portal and invoke it by clicking on 'Try it'".
Give the API key any name you want.

![Image of Architecture](/images/sharingoutside.png)

You'll end up with an API key and a URL linking to the API definition as shown below.

![Image of Architecture](/images/sharingoutsidekey.png)

Copy the link and paste to a new browser window.
You'll see something similar to the page below.

![Image of Architecture](/images/apiendpoint.png)

The endpoint URL is what you'll need to paste into the Watson Assistant webhook setting (along with the API Key you created above).

Now we need to start the flow in IBM APP Connect so it is callable.
At the top of the page you'll see three dots, click those and select Start API. If all goes well, the circle should turn green and we are now done in IBM APP Connect and our API is running. 

> Note: If you want to edit the flow later, you'll need to stop the flow first and then start it again (that gets me every time! :smile).

## Putting it all together

Back in Watson Assistant, go to your skill and select Options/Webhooks.
Paste the API endpoint URL into the webhook URL field.
Add three headers (using the Add header option below the headers section) and complete them as shown below (the API Key from above goes in the X-IBM-Client-Id header setting.

![Image of Architecture](/images/webookapisettings.png)

We can now try the integration!
Click on the Try it out link at the top right of the page and type "what's your ip address?".
The result should come back as shown below!

![Image of Architecture](/images/tryitout.png)

### License

Copyright 2020 Tim Minter

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

See also the file LICENSE.
