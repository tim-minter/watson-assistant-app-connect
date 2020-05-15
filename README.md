# No Code Watson Assistant and App Connect integration sample

This is a sample [Watson Assistant](https://cloud.ibm.com/catalog/services/watson-assistant) bot which demonstrates how to call [IBM App Connect](https://cloud.ibm.com/catalog/services/app-connect) "natively" to run any number of integrations eg business processes, automations or external API calls and more. 

Not long ago, you would have needed to write (and therefore host and secure) code to achieve this, however that is no longer the case. You can easily create a chatbot/virtual assistant from "out of the box" [IBM Cloud](https://cloud.ibm.com) services with no custom code to manage, secure or maintain. The killer feature of the whole solution is that, once assembled, the end to end system can be managed and added to by business users without the need for developer time.

This is a write up of a real project I completed and, as an example, I was able to add a ServiceNow problem creation conversation to my Watson Assistant in less than 15 minutes (including the creation of the ServiceNow sandbox from https://developer.servicenow.com).

IBM App Connect allows you to "connect anything to anything" and build workflows to "do anything" between those connections. We are talking workflow/business logic and transformations basically. This really could be anything from turning an email into a problem ticket to recording the mileage of a fleet of connected vehichles and figuring out which miles should be expenses and then handling the exprense process, to performing automated insurance claims based on images sent by the customer etc etc.

This solution uses an IBM App Connect API flow that exposes an API which acts as a router for incomming calls from Watson Assistant. Watson Assistant has a powerful Webhook feature but is limited in that only one web hook can be configured per assistant skill. It's entirely possible to have a number of skills within a Watson Assistant but even within a single skill you may want to be able to call a number of integrations, so a router API makes sense. 

# Achitecture

![Image of Architecture](/images/architecture.png)

# Setup

1. Sign in to or up for an IBM Cloud account [here](https://cloud.ibm.com)

1. Create a Watson Assistant service on IBM Cloud [here](https://cloud.ibm.com/catalog/services/watson-assistant).
The free version can be used. In my actual solution I used the Plus version that comes with an incedibly simple way to embed the Assistant into a web page.

1. Create an instance of IBM Connect on IBM Cloud [here](https://cloud.ibm.com/catalog/services/app-connect).
The free version is perfectly fine again.

1. Open the [management page](https://us-south.assistant.watson.cloud.ibm.com) (this link may not work depending on where you created your assistant) of your Watson Assistant and click on the Assistants menu (top left) then click on Create Assistant

![Intial WA interface](/images/initialcreateassistant.png)

1. Give the assistant a name

![Assistant creation page](/images/createassistantdetails.png)

1. Then click on the Add Dialogue skill on the next page...

![Assistant page](/images/createassistant.png)

1. Slect the Create Skill option at the top of the page then give the skill a name eg.

![Skill creation page](/images/createskill.png)

1. This page shows the assistant settings including the skill connected to it and any Integations (interfaces) set up. Note the Preview Link integation that was created for you. Click on the Demo Skill you created to edit the contents.

![Skill selection page](/images/editskill.png)

1. Here you edit the intents, entities and dialogue and other settings of your assistant. Click Create intent to create the first intent of your system.

![Create intent intial page](/images/createintentsplash.png)

1. Go to the Intents section. Add an intent called "#get_my_ip" and complete it as shown below
![Image of Architecture](/images/architecture.png)

### License

Copyright 2018 Tim Minter

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
