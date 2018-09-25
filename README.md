# A Demo Project of Azure Logic App CD/CI with [Azure DevOps](https://azure.microsoft.com/en-us/services/devops/)
> This is a demo project to show how to use [Azure DevOps](https://azure.microsoft.com/en-us/services/devops/)

It includes the following sub projects:
- **A Logic App** project
In this demo, it has a very simple Logic App triggered by HTTP Request and then invoke an Azure Function and write the output of the Function to Azure Blob Storage.

- **An Azure Function**
The function app is to add a timestamp in the reqeust body and written by Node.

- **Azure Devops build/release pipeline**
We create an Azure DevOps project, which has two git repository, one for Logic App, the other for Azure Function. Plus a build pipline and release pipeline to deploy the Logic App and Function from Dev Stage to QA Stage and Production Stage, the deployment is triggered by code cimmit.

## Technical background 

- ### What is Logic App & Why Logic App need CD/CI
Azure Logic Apps simplifies how you build automated scalable workflows that integrate apps and data across cloud services and on-premises systems. 

Here are some links for your reference
https://azure.microsoft.com/en-us/services/logic-apps/
https://docs.microsoft.com/en-us/azure/logic-apps/
https://blog.mexia.com.au/microsoft-azure-ipaas

Logic App can be created in Azure Portal or Visual Studio and it can be deployed to Azure manually, while in real business senario, it is need to delpoy the logic to production azure environment by tool after tests run successfully.

- ### What is Azure DevOps
With Azure DevOps Services and TFS, you gain an integrated set of services and tools to manage your software projects, from planning and development through testing and deployment. 

Rapid and reliable release of software comes from automating as many processes as possible. Our systems support build, test, and release automation.
You can define builds to automatically run whenever a team member checks in code changes
Your build pipelines can include instructions to run tests after the build runs
Release pipelines support managing deployment of your software builds to staging or production environments

https://docs.microsoft.com/en-us/azure/devops/user-guide/?view=vsts


## Implementation

### 1. Create DevOps Project in portal
We create a project in Azure DevOps named **"logicapp-devops-demo"**. Here are the screenshots of how to do it in Azure DevOps portal.
![create project](/images/create-azuredevops-project.gif)

### 2. Create Azure Function in VS Code
Here is a VS Code Extension for Azure Function, it support to create and deploy Azure Function in VS Code.

https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions

Here are the screenshots of how to do it in VS Code.
It created another project named **azure-devops-logicapp-apim-func**
![create function](/images/create-function-vscode.gif)

Replace code of index.js with the code below. It get the value of parameter 'name' and return a JSON object with a timestamp, named 'ts'

```javascript
module.exports = async function (context, req) {
    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: {
                 name: (req.query.name || req.body.name),
                 ts: new Date()
            },
            headers: {
                'Content-Type': 'application/json'
            }
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};
```

Now you can test the Azure Function locally, by press the key **F5**  in VS code. Here are the screenshots.
![run function](/images/run-function-vscode.gif)

### 3. Deploy Function to Azure Manually and test it

In VSTS you can deploy the Azure Function to Azure and test it.

Here is the screenshots to deploy
![deploy function](/images/deploy-function-vscode.gif)

Test the function
![test function](/images/test-function-azure.gif)

### 4. Commit Azure Function to Azure DevOps Repository

It need to add source control for this function, in this demo we use Azure DevOps. It need to create a new repository named **azure-devops-logicapp-apim-func** in the project **"logicapp-devops-demo"** it is created in step 1.

Create the repository and commit code:
![commit function](/images/commit-function-vsts.gif)

You need the git commands below to push your code to the repository.

```
cd <path/to/function>
git init
git add --all
git commit -m "Initial commit"
git remote add origin <remote repo URL>
git push -u origin master
```

#### Now the Azure Function is ready.

### 5. Create Logic App in Visual Studio

### 6. Test in Dev Envrivonment

### 7. Create QA Envrivonment and Deploy

### 8. Create Production Envrivonment and Deploy

