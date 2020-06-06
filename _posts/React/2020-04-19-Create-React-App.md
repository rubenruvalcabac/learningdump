---
layout: post
title: Working with AzureDevops
published: false
---

1. Create repository
2. Create RectApp

## Create repository (AzureDevOps)

### Create the repository

#### Create alternate credentials

This will create your username and password for clonning

Clic on "Clone" button
Copy the url

### Create the repository directory and clone it

mkdir myrepository
cd myrepository
mkdir myproject
cd myproject
git clone urlToMyRepository
git config user.name "username"
git config user.password "password"
cd ..
npx create-react-app myproject
cd myproject
npm start
