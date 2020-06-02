# No Code Watson Assistant and App Connect integration sample

This is a sample [Watson Assistant](https://cloud.ibm.com/catalog/services/watson-assistant) bot which demonstrates how to call [IBM App Connect](https://cloud.ibm.com/catalog/services/app-connect) "natively" to run any number of integrations eg business processes, automations or external API calls and more. 

Not long ago, you would have needed to write (and therefore host and secure) code to achieve this, however that is no longer the case. You can easily create a chatbot/virtual assistant from "out of the box" [IBM Cloud](https://cloud.ibm.com) services with no custom code to manage, secure or maintain. The killer feature of the whole solution is that, once assembled, the end to end system can be managed and added to by business users without the need for developer time.

This is a write up of a real project I completed and, as an example, I was able to add a ServiceNow problem creation conversation to my Watson Assistant in less than 15 minutes (including the creation of the ServiceNow sandbox from https://developer.servicenow.com).

IBM App Connect allows you to "connect anything to anything" and build workflows to "do anything" between those connections. We are talking workflow/business logic and transformations basically. This really could be anything from turning an email into a problem ticket to recording the mileage of a fleet of connected vehicles and figuring out which miles should be expenses and then handling the expense process, to performing automated insurance claims based on images sent by the customer etc etc.

This solution uses an IBM App Connect API flow that exposes an API which acts as a router for incoming calls from Watson Assistant. Watson Assistant has a powerful Webhook feature but is limited in that only one web hook can be configured per assistant skill. It's entirely possible to have a number of skills within a Watson Assistant but even within a single skill you may want to be able to call a number of integrations, so a router API makes sense. 

# Architecture

![Image of Architecture](/images/Architecture.png)

# Setup

1. Sign in to or up for an IBM Cloud account [here](https://cloud.ibm.com)

1. Create a Watson Assistant service on IBM Cloud [here](https://cloud.ibm.com/catalog/services/watson-assistant).
The free version can be used. In my actual solution I used the Plus version that comes with an incredibly simple way to embed the Assistant into a web page.

1. Create an instance of IBM Connect on IBM Cloud [here](https://cloud.ibm.com/catalog/services/app-connect).
The free version is perfectly fine again.

1. Open the [management page](https://us-south.assistant.watson.cloud.ibm.com) (this link may not work depending on where you created your assistant) of your Watson Assistant and click on the Assistants menu (top left) then click on Create Assistant

![Initial WA interface](/images/initalcreateassistant.png)

1. Give the assistant a name

![Assistant creation page](/images/createassistantdetails.png)

1. Then click on the Add Dialogue skill on the next page...

![Assistant page](/images/createassistant.png)

1. Select the Create Skill option at the top of the page then give the skill a name eg.

![Skill creation page](/images/createskill.png)

1. This page shows the assistant settings including the skill connected to it and any Integrations (interfaces) set up. Note the Preview Link integration that was created for you. Click on the Demo Skill you created to edit the contents.

![Skill selection page](/images/editskill.png)

1. Here you edit the intents, entities and dialogue and other settings of your assistant. Click Create intent to create the first intent of your system.

![Create intent initial page](/images/createintentsplash.png)

1. Go to the Intents section. Add an intent called "#get_my_ip" and complete it as shown below.

![Image of Architecture](/images/createintent.png)

1. Fill in the intent as shown below.

![Image of Architecture](/images/defineintent.png)

1. Close the Intent (top right arrow) and click on the Dialogue menu
1. Click on the Add node button in the dialogue view (shown below)

![Image of Architecture](/images/initialdialogue.png)

The first thing to do here is select the intent you just created in the box shown below.
Then turn on the webhook feature. Click on the Customise gear icon at the top right

![Image of Architecture](/images/initialdialoguesettings.png)

The following dialogue will be shown.
Click the Webhooks slider to on 

![Image of Architecture](/images/turnonwebhooks.png)

Now, back in the dialogue node settings you will see some additional fields have appeared...

![Image of Architecture](/images/webhookdialoguesettings.png)

The parameters section allows you to send data with your webhook call. We will be creating an API that accepts a "type" parameter later. For this demo lets add the type parameter and give it a value of "getIPAddress". Any number of parameters can be sent.
The Return variable is auto populated with a name, but can be changed to anything you want. This becomes a Context Variable and will be filled with all the data that is returned by the webhook call later and can be used in your dialogues.
The "Assistant responds" section is also pre-populated with a couple of values. If you change the variable name above, you'll need to change the name in this section too.
The first lines basically says "if we get a result back from the webhook (ie the variable is populated) then say x".
    This is the whole line: If assistant recognises `$webhook_result_1` then respond with `My ip address is <? $webhook_result_1.reply ?>".`
    The <? ?> format basically inserts a variable into the dialogue reply and can be used with any variable used within Watson Assistant. 
The second line handles errors and Watson Assistant returns errors in a particular format, shown below.
    If assistant recognises `anything else` (ie an error) then reply with `The callout generated this error: <? output.webhook_error.webhook_result_1 ?>.`
    
The last part to complete in Watson Assistant is setting the webhook, but we need to create the API to call first!

We will use IBM App Connect to create the API. Within APP Connect we can then create as many integrations as we want (each called via a different "type" parameter, that can be called via that one API.

#Create an instance of IBM App Connect

To create a new instance of APP Connect, go to your cloud account and search the catalogue for App Connect. Follow the setup steps and choose the Lite plan.

Once the setup completes open App Connect, go to Manage and then Launch App Connect.

Go to the Dashboard section and then click the New button and select Flows for an API.
Then give you API a name and give your model a name as shown below.

![Image of Architecture](/images/apicreation.png)

Click Create Model and set your API properties. I've set mine as shown below.

![Image of Architecture](/images/apiproperties.png)

![Image of Architecture](/images/addanoperation.png)

![Image of Architecture](/images/operations.png)

Click the Implement Flow button. 
You will be presented with a drag and drop flow editor. Click the + between the Request and Response objects, click on the Toolbox option and add an "If (conditional)" block. 

![Image of Architecture](/images/initialifflow.png)

To do... document the API flow...

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
