# Azure Logic App CD/CI with [Azure DevOps](https://azure.microsoft.com/en-us/services/devops/)
> This is a demo project to show how to use [Azure DevOps](https://azure.microsoft.com/en-us/services/devops/)

Here is the screenshots of Logic App and release pipeline:
![azure function](/images/azure-function.jpg)

![release pipeline](/images/release-pipeline.jpg)

The demo includes the following sub projects:
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

Logic App can be created in Azure Portal or Visual Studio and it can be deployed to Azure manually, while in real business senario, it is need to deploy the logic to production azure environment by tool after tests run successfully.

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

The function will run on the URL http://localhost:7071/api/addtimestamp?name=hello
and it will return the result
```
{
  "name": "hello",
  "ts": "2018-09-25T07:09:50.885Z"
}
```

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
git push -uf origin master
```

#### Now the Azure Function is ready.

### 5. Create Logic App in Visual Studio 2017
In this demo, we will write the result into Azure Blob Storage, so we create the Storage container first. and the storage account name is "mayemsftlogicappstorage" and the container's name is "resultfolder".
![create storage](/images/create-blob-storage.gif)

Here is a document of how to create Logic App in Visual Studio 2017
https://docs.microsoft.com/en-us/azure/logic-apps/quickstart-create-logic-apps-with-visual-studio

And here are screenshots
Create Logic App project in VS2017
![create logicapp project](/images/create-logicapp-project.gif)

Create a Logic App and using the Azure Function
![build logicapp function](/images/build-logicapp-function.gif)

Write the result to Storage
![build logicapp function](/images/build-logicapp-storage.gif)

Deploy the Logic App in VS2017 manually
![deploy logicapp manually](/images/deploy-logicapp-manually.gif)

Test Logic App in Postman and check if the result written into the blob storage.
![test logicapp in browser](/images/test-logicapp.gif)

Commit Logic App to Azure DevOps Repository
![commit logicapp](/images/commit-logicapp-vsts.gif)


### 6. Create Release Pipeline
Now we need to create build and release pipeline for the logic app.

1. Create the build pipeline of the Azure Function **azure-devops-logicapp-apim-func**
![create function build](/images/create-function-build-pipeline.gif)

2. Create the build pipeline of the Logic App 
![create function build](/images/create-logicapp-build-pipeline.gif)

3. Create new storage containers for stages.
The storage account name are 
- "mayemsftlogicappdemodev" for Dev stage 
- "mayemsftlogicappdemoqa" for QA stage 
- "mayemsftlogicappdemoprod" for Production stage 

And the container's name for those accounts is "resultfolder".

4. Create Stages in the release pipeline
![create function build](/images/create-release-pipeline-dev.gif)

5. Config blob connection and parameters for different Stage.
As we will have 3 stages with different storages, we will use pipeline variable to store the access key and put them into Logic App ARM template during release.

Here is the screenshots to show the variable settings of pipeline.
![create variable group](/images/create-release-pipeline-variable.gif)

6. After the pipeline run successully, it will create logic app and Azure Function, (Function App need to be created manually). And the logic app trigger url can be found from portal. Postman can be used to test this. the screenshots below is to check the production deployment.
![postman check prod](/images/postman-check-prod.gif)


### TODO
- Java based Function.
- .Net based Function.
- Function Test in Build/Release Pipeline.
- Postman test in Release Pipeline.
- Manual trigger to Production Stage.
- Rollback and HA 
- Implement A Logic App Pattern including Service Bus.
- Using APIM to protect the API.
- APIM CD/CI.
- Monitor Logic App with OMS



