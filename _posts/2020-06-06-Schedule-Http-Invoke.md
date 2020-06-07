---
layout: post
title: Schedule Http Invoke
---

#Overview
I have a set of reports which should be generated and send by email automatically:

- Monday to Friday at 14:00
- Saturday at 13:00

I already have an end-point which generates the report, exports it as PDF and send it by mail. The

In this article I'll explain how did I achieved to automatically invoke the end-point at the defined days and time.

#Azure LogicApps
The service I'll use for scheduling the API call is [LogicApps](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-overview). This service allows you to automate and schedule tasks and workflows.

In my case just need scheduled triggers. I'll create one LogicApp for Monday to Friday, and another for Saturdays. The following steps shows how I created the first one:
