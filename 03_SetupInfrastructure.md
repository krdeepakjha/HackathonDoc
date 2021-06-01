# Infrastructure Repository and Pipeline

## Introduction

You should now have Completed the Following things:
1. Setup your GitHub Account
2. Setup the Example Code in your Account
3. Added the Repository Secrets to the Example Code

Next you will get an Overview over the Example Code to Setup your first own Website on Azure. This will only be the most basic Infrastructure Setup. In a later Step we will then alter the Websites Content.


## Now start writing the infrastructure pipeline to deploy an **App Service Plan** and a **WebApp** in Azure step by step


_Warning: The formatting of YAML (yml) files is based on spaces and tabs and therefor the following lines should be copied with care.
 It is advised to use Visual Studio Code to validate the copied file._

Starting with making the variable group available to the pipeline. From now on we can reference any variable that is held in the variable group under libraries (described under 2):

variables:
 - group: infravars

Next, we need to set a trigger which will run the pipeline after every commit on this repository:
 (we set it to none to avoid unwanted pipeline runs)

trigger: none

 Then we need to set an operating system in which our pipeline will run:

pool:
 vmImage: ubuntu-latest

 After that we define the smallest building block of a pipeline which is called **step** and

can be a task or a script.

This Task that we want to use is a special Azure CLI task that will execute our commands like would do it on the Azure Portal in the cloud shell:

steps:

- task: AzureCLI@2

inputs:

azureSubscription: &#39;$(so)&#39;

scriptType: &#39;bash&#39;

scriptLocation: &#39;inlineScript&#39;

inlineScript: |

az appservice plan create -g $(rg) -n $(asp) --is-linux --number-of-workers 1 --sku B1

az webapp create -g $(rg) -p $(asp) -n $(wa) --runtime &quot;node|10.14&quot;

If you want to learn more about the concept of a pipeline you can do it here:

[https://docs.github.com/en/actions/quickstart](https://docs.github.com/en/actions/quickstart)


We all are use the same Subscription and same Resource Group but

everybody uses his own App Service Plan and his own Webapp.

Therefore, pick unique names e.g. robsplan21 or robswebapp21 for your App Service Plan

and Webapp variables.

Use for Resource Group (rg) **ws-devops**.

asp = App Service Plan must be unique in each resource group (your own)
 rg = Resource Group must be unique in each subscription **(**use **ws-devops)**
 so = Service Connection Name which you have set in your project (Settings -\&gt; Service connections)

wa = Webapp must be unique worldwide (your own)

Of course, you can use your own variables names, they must correspond with your pipeline.


# Set up and run the pipeline

# Now it is time for some rocket science, therefore click on the rocket and reference it to your pipeline


# After all you can admire your work and launch the pipeline by clicking on RUN (upper right corner)


# Now you can watch your pipeline running by clicking on the Job

# After the pipeline has run you should see only green lights

# Check your WebApp is online after approx. 5 minutes

https://[yourWebAppName].azurewebsites.net/

You should see a &quot;Hey, Node developers&quot; welcome screen.

Congratulations, you have deployed your first WebApp infrastructure.
 Now, you can go ahead and deploy some code to your WebApp.