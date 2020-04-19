---
layout: post
title: Working with AzureDevops
published: false
---

#Authentication

https://docs.microsoft.com/en-us/azure/devops/repos/git/auth-overview?view=azure-devops

https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page

# Pipelines

## Update CDN

az cdn endpoint purge -g Portal-SimanG8 -n siman-portal --profile-name simanportal-cdn --content-paths "/\*"

You have to use a Windows agent, because Linux doesnt support it