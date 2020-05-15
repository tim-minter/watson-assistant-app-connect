# No Code Watson Assistant and App Connect integration sample

This is a sample Watson Assistant bot which demonstrates how to call App Connect "natively" to run any number of integrations eg business processes, automations or external API calls and more. Not long ago, you would have needed to write code to achieve this, however that is no longer the case. You can easily create a chatbot/virtual assistant from "out of the box" IBM Cloud services with no custom code to manage, secure or maintain. The killer feature of the whole solution is that, once built, the end to end system can be managed and added to by business users without the need for developer time.

This is a write up of a real project I completed and, as an example, I was able to add a ServiceNow problem creation conversation to my Watson Assistant in less than 15 minutes (including the creation of the ServiceNow sandbox from https://developer.servicenow.com).

The sample IBM App Connect API flow exposes an API which acts as a router for incomming calls from Watson Assistant. Watson Assistant has a powerful Webhook feature but is limited in that only one web hook can be configured per assistant skill. It's entirely possible to have a number of skills within a Watson Assistant but even within a single skill you may want to be able to call a number of integrations, so a router API makes sense. 

# Achitecture

![Image of Architecture](/images/Architecture.png)

# Setup




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
