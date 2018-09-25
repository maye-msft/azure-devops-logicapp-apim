# A Demo Project of Azure Logic App CD/CI with [Azure DevOps](https://azure.microsoft.com/en-us/services/devops/)
This is a demo project to show how to use [Azure DevOps](https://azure.microsoft.com/en-us/services/devops/)

It includes the following sub projects:
- **A Logic App** project
In this demo, it has a very simple Logic App triggered by HTTP Request and then invoke an Azure Function and write the output of the Function to Azure Blob Storage.

- **An Azure Function**
The function app is to add a timestamp in the reqeust body and written by Node.

- **Azure Devops build/release pipeline**
We create an Azure DevOps project, which has two git repository, one for Logic App, the other for Azure Function. Plus a build pipline and release pipeline to deploy the Logic App and Function from Dev Stage to QA Stage and Production Stage, the deployment is triggered by code cimmit.

## Technical background 

- ### What is Logic App & Why Logic App need CD/CI

- ### What is Azure DevOps

## Implementation

1. ### Create DevOps Project in portal

2. ### Create Azure Function in VS Code

3. ### Create Logic App in Visual Studio

4. ### Test in Dev Envrivonment

5. ### Create QA Envrivonment and Deploy

6. ### Create Production Envrivonment and Deploy

