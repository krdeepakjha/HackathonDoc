# Webapp Repository and Pipeline

# Content

[1.Initialize the repository 2](#_Toc66790278)

[2.Set up the variable group 5](#_Toc66790284)

[3.Set up and run the pipeline 8](#_Toc66790288)

#
## Initialize the repository

# Import Repository

We import an existing repository that already contains the web application we want to deploy


# Import a new repository for you webapp

Clone URL:
# Add a new file to your repository

# Give it a meaningful name with the suffix &quot;.yml&quot;

# Now start writing the webapp pipeline to build and deploy the webapp to the **App Service Plan** in Azure step by step


First we need to set a trigger which will run the pipeline after every commit on this repository:
 (we set it to none to avoid unwanted pipeline runs)

trigger: none

Next, we will make the variable group available to the pipeline. From now on we can reference any variable that is held in the variable group under libraries (described under 2):

variables:
 - group: infravars

 After that, we create two stages, Build and Deploy and define all necessary steps and tasks within steps:

The first stage installs all the application dependencies, builds the web app and uploads the build app

stages:
 - stage: Build
 displayName: Build stage
 jobs:
 - job: Build
 displayName: Build
 pool:
 vmImage: &#39;ubuntu-latest&#39;

steps:
 - task: NodeTool@0
 inputs:
 versionSpec: &#39;10.x&#39;
 displayName: &#39;Install Node.js&#39;
 - script: |
 npm install
 npm run build --if-present
 workingDirectory: &#39;$(System.DefaultWorkingDirectory)/app&#39;
 displayName: &#39;npm install, build and test&#39;

- task: ArchiveFiles@2
 displayName: &#39;Archive files&#39;
 inputs:
 rootFolderOrFile: &#39;$(System.DefaultWorkingDirectory)/app&#39;
 includeRootFolder: false
 archiveType: zip
 archiveFile: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
 replaceExistingArchive: true

- upload: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
 artifact: drop

In the second stage we then run the uploaded web application on the infrastructure we provided earlier

- stage: Deploy
 displayName: Deploy stage
 dependsOn: Build
 condition: succeeded()
 jobs:
 - deployment: Deploy
 displayName: Deploy
 environment: $(wa)
 pool:
 vmImage: &#39;ubuntu-latest&#39;
 strategy:
 runOnce:
 deploy:
 steps:
 - task: AzureWebApp@1
 displayName: &#39;Azure Web App Deploy: $(wa)&#39;
 inputs:
 azureSubscription: $(so)
 appType: webAppLinux
 appName: $(wa)
 runtimeStack: &#39;NODE|10.10&#39;
 package: $(Pipeline.Workspace)/drop/$(Build.BuildId).zip
 startUpCommand: &#39;npm run start&#39;

if we are finished, we need to commit our changes

If you want to learn more about the concept of a pipeline you can do it here:

 https://docs.microsoft.com/de-de/azure/devops/pipelines/get-started/key-pipelines-concepts?view=azure-devops


# Set up the variable group

We will reuse the variable group from infravars ;-)


# Set up and run the pipeline


# Now it is time for some rocket science, therefore click on the rocket and reference it to your pipeline

# After all you can admire your work and launch the pipeline by clicking on RUN (upper right corner)

# Now you can watch your pipeline running by clicking on the Job


# After the pipeline has run you should see only green lights

# Check your WebApp is online

https://\&lt;yourWebAppName\&gt;.azurewebsites.net/

**Congratulations, you have deployed your first WebApp on you own infrastructure.
 Now you can go ahead and change the code of your WebApp.**