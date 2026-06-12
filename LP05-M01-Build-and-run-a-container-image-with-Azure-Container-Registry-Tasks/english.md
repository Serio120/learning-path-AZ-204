# LP05 M01: Build and run a container image with Azure Container Registry Tasks

[To keep in mind](#)

<h2 align="center">******************   Unformatted text *********************</h2>

In this exercise, you build a container image from your application code and push it to Azure Container Registry using Azure CLI. You learn how to prepare your app for containerization, create an ACR instance, and store your container image in Azure.

Tasks performed in this exercise:

Create an Azure Container Registry resource
Build and push an image from a Dockerfile
Verify the results
Run the image in the Azure Container Registry
This exercise takes approximately 20 minutes to complete.

Create an Azure Container Registry resource

Open a web browser and navigate to the Azure portal, https://portal.azure.com.

In the Sign in dialog box, copy, and paste in the username studentDX9BU@t008.gdazcs.com and then select Next.

In the Enter password dialog box, copy, and paste in the password xqBUXO2oR0223Z3R then select Sign in.

Note: You will be prompted to enter an authentication token.

Select Generate Authentication Code below.

You will have 60 seconds to sign in using this code before having to generate a new one.

In the Code dialog box, enter or copy, and paste in the Authentication code then select Verify

Important: You will have 60 seconds to sign in using the code before having to generate a new one.

On the Welcome to Microsoft Azure dialogue box click Maybe Later. If an Azure recommendations dialog box pops up click X.

Use the [>_] button to the right of the search bar at the top of the page to create a new cloud shell in the Azure portal, selecting a Bash environment. The cloud shell provides a command line interface in a pane at the bottom of the Azure portal.

Note: If you have previously created a cloud shell that uses a PowerShell environment, switch it to Bash.

The resource group for the resources needed for this exercise is created for you. This is the script you would type create a Resource Group called myResourceGroup.

az group create --location eastus2 --name myResourceGroup
Run the following command to create a basic container registry. The registry name must be unique within Azure, and contain 5-50 numeric and lowercase characters. Replace myResourceGroup with the name you used earlier, and myContainerRegistry with a unique value.

```
az acr create --resource-group LP5M1-UK0OUSUMDE\
    --name mycontainerregistryxxxxx --sku Basic
```

Note: The command creates a Basic registry, a cost-optimized option for developers learning about Azure Container Registry.

Build and push an image from a Dockerfile

Next, you build and push an image based on a Dockerfile.

Run the following command to create the Dockerfile. The Dockerfile contains a single line that references the hello-world image hosted at the Microsoft Container Registry.

echo FROM mcr.microsoft.com/hello-world > Dockerfile
Run the following az acr build command, which builds the image and, after the image is successfully built, pushes it to your registry. Replace mycontainerregistryxxxxx with the name you created earlier.

```
az acr build --image sample/hello-world:v1 --registry mycontainerregistryxxxxx --file Dockerfile .
```

Following is a shortened sample of the output from the previous command showing the last few lines with the final results. You can see in the repository field the sample/hello-word image is listed.

- image:
    registry: mycontainerregistryxxxxx.azurecr.io
    repository: sample/hello-world
    tag: v1
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: mcr.microsoft.com
    repository: hello-world
    tag: latest
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  git: {}

Run ID: cf1 was successful after 11s
Verify the results

Run the following command to list the repositories in your registry. Replace mycontainerregistryxxxxx with the name you created earlier.

az acr repository list --name mycontainerregistryxxxxx --output table
Output:

Result
----------------
sample/hello-world
Run the following command to list the tags on the sample/hello-world repository. Replace mycontainerregistryxxxxx with the name you used earlier.

az acr repository show-tags --name mycontainerregistryxxxxx\
    --repository sample/hello-world --output table
Output:

Result
--------
v1
Run the image in the ACR

Run the sample/hello-world:v1 container image from your container registry with the az acr run command. The following example uses $Registry to specify the registry where you run the command. Replace mycontainerregistryxxxxx with the name you used earlier.

az acr run --registry mycontainerregistryxxxxx\
    --cmd '$Registry/sample/hello-world:v1' /dev/null
The cmd parameter in this example runs the container in its default configuration, but cmd supports other docker run parameters or even other docker commands.

The following sample output is shortened:

Packing source code into tar to upload...
Uploading archived source code from '/tmp/run_archive_ebf74da7fcb04683867b129e2ccad5e1.tar.gz'...
Sending context (1.855 KiB) to registry: mycontainerre...
Queued a run with ID: cab
Waiting for an agent...
2019/03/19 19:01:53 Using acb_vol_60e9a538-b466-475f-9565-80c5b93eaa15 as the home volume
2019/03/19 19:01:53 Creating Docker network: acb_default_network, driver: 'bridge'
2019/03/19 19:01:53 Successfully set up Docker network: acb_default_network
2019/03/19 19:01:53 Setting up Docker configuration...
2019/03/19 19:01:54 Successfully set up Docker configuration
2019/03/19 19:01:54 Logging in to registry: mycontainerregistryxxxxx008.azurecr.io
2019/03/19 19:01:55 Successfully logged into mycontainerregistryxxxxx008.azurecr.io
2019/03/19 19:01:55 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/03/19 19:01:55 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.

2019/03/19 19:01:56 Successfully executed container: acb_step_0
2019/03/19 19:01:56 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 0.843801)

Run ID: cab was successful after 6s


#### In Azure Portal
GRUPO DE RECURSOS > CONTAINER REGISTRY > SERVICES >
