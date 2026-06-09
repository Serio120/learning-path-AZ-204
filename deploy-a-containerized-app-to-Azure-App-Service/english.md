
# LP01 M01: Deploy a containerized app to Azure App Service
Deploy a containerized app to Azure App Service

In this exercise, you create an Azure App Service web app configured to run a containerized application by specifying a container image from Microsoft Container Registry. You learn how to configure container settings, deploy the app, and verify that the containerized application is running successfully in Azure App Service.

Tasks performed in this exercise:

Create an Azure App Service resource and deploy a containerized app
View the results
Clean up resources
This exercise takes approximately 15 minutes to complete.

## Create a web app resource

Open a web browser and navigate to the Azure portal, https://portal.azure.com.

In the Sign in dialog box, copy, and paste in the username studentMMUK9@t008.gdazcs.com and then select Next.

In the Enter password dialog box, copy, and paste in the password YEmfD3vS0gkeKaXq then select Sign in.

> Note: You will be prompted to enter an authentication token.

Select Generate Authentication Code below.

You will have 60 seconds to sign in using this code before having to generate a new one.

In the Code dialog box, enter or copy, and paste in the Authentication code then select Verify

Important: You will have 60 seconds to sign in using the code before having to generate a new one.

On the Welcome to Microsoft Azure dialogue box click Maybe Later. If an Azure recommendations dialog box pops up click X.

Select the + Create a resource located in the Azure Services heading near the top of the homepage.

In the Search the Marketplace search bar, enter web app and press enter to start searching.

In the Web App tile, select the Create drop-down and then select Web App.

Screenshot of the Web App tile.

Selecting Create will open a template with a few tabs to fill out with information about your deployment. The following steps walk you through what changes to make in the relevant tabs.

Fill out the Basics tab with the information in the following table:

Setting	Action

Subscription	Retain the default value.
Resource group	Select the existing Resource Group created for you.
Name	Enter a unique name, for example your-initials-containerwebapp. Replace your-initials with your initials, or some other value. The name needs to be unique, so it may require a few changes.
Slider under Name setting	Select the slider to turn it off. This slider only appears in some Azure configurations.
Publish	Select the Container option.
Operating System	Ensure Linux is selected.
Region	Retain the default selection, or choose a region near you.
Linux Plan	Retain the default selection.
Pricing plan	Select the drop-down and choose the Free F1 plan.
Select, or navigate to, the Container tab, and enter the information in the following table:

Setting	Action

Sidecar support	Retain default off position.
Image Source	Select Other container registries.
Access Type	Retain default Public selection.
Registry server URL	Enter mcr.microsoft.com/k8se
Image and Tag	Enter quickstart:latest.
Startup Command	Leave blank.
NOTE: You may need to change the region in which you deploy the Web App away from East US. For example East US 2 etc.

Select the Review + create tab.

Review the selections you made, and then select the Create button.

It may take a few minutes for the deployment to complete. When it is finished select the Go to resource button.

Now that your deployment has finished it's time to view the web app. Select the link to your web app located next to the Default domain field in the Essentials section. The link will open the site in a new tab.

> Note: It may take a few minutes for the deployed container app to run and display in the new tab.
