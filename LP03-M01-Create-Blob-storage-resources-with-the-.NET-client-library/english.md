# LP03 M01: Create Blob storage resources with the .NET client library

[To keep in mind](https://github.com/Serio120/learning-path-AZ-204/blob/main/LP03-M01-Create-Blob-storage-resources-with-the-.NET-client-library/notes.md#)

<h2 align="center">******************   Unformatted text *********************</h2>

In this exercise, you create an Azure Storage account and build a .NET console application using the Azure Storage Blob client library to create containers, upload files to blob storage, list blobs, and download files. You learn how to authenticate with Azure, perform blob storage operations programmatically, and verify results in the Azure portal.

Tasks performed in this exercise:

Prepare the Azure resources
Create a console app to create and download data
Run the app and verify results
Clean up resources
This exercise takes approximately 30 minutes to complete.

Create an Azure Storage account

In this section of the exercise you create the needed resources in Azure with the Azure CLI.

Open a web browser and navigate to the Azure portal, https://portal.azure.com.

In the Sign in dialog box, copy, and paste in the username student4807P@t008.gdazcs.com and then select Next.

In the Enter password dialog box, copy, and paste in the password dpaCO6R6W0hucruG then select Sign in.

Note: You will be prompted to enter an authentication token.

Select Generate Authentication Code below.

You will have 60 seconds to sign in using this code before having to generate a new one.

In the Code dialog box, enter or copy, and paste in the Authentication code then select Verify

Important: You will have 60 seconds to sign in using the code before having to generate a new one.

On the Welcome to Microsoft Azure dialogue box click Maybe Later. If an Azure recommendations dialog box pops up click X.

Use the [>_] button to the right of the search bar at the top of the page to create a new cloud shell in the Azure portal, selecting a Bash environment. The cloud shell provides a command line interface in a pane at the bottom of the Azure portal.

Note: If you have previously created a cloud shell that uses a PowerShell environment, switch it to Bash.

In the cloud shell toolbar, in the Settings menu, select Go to Classic version (this is required to use the code editor).

The resource group for the resources needed for this exercise is created for you. This is the script you would type create a Resource Group called myResourceGroup.

az group create --location eastus2 --name myResourceGroup
Many of the commands require unique names and use the same parameters. Creating some variables will reduce the changes needed to the commands that create resources. Run the following commands to create the needed variables. Replace myResourceGroup with the name you you're using for this exercise.

resourceGroup=myResourceGroup-23F1HLFCWC
location=eastus
accountName=storageacct$RANDOM
Run the following commands to create the Azure Storage account, each account name must be unique. The first command creates a variable with a unique name for your storage account. Record the name of your account from the output of the echo command.

az storage account create --name $accountName\
    --resource-group $resourceGroup\
    --location $location\
    --sku Standard_LRS
echo $accountName
Assign a role to your Microsoft Entra user name

To allow your app to send and receive messages, assign your Microsoft Entra user to the Azure Service Bus Data Owner role at the Service Bus namespace level. This gives your user account permission to manage and access queues and topics using Azure RBAC. Perform the following steps in the cloud shell.

Run the following command to retrieve the userPrincipalName from your account. This represents who the role will be assigned to.

userPrincipal=$(az rest --method GET --url https://graph.microsoft.com/v1.0/me\
    --headers 'Content-Type=application/json'\
    --query userPrincipalName --output tsv)
Run the following command to retrieve the resource ID of the storage account. The resource ID sets the scope for the role assignment to a specific namespace.

resourceID=$(az storage account show --name $accountName\
    --resource-group $resourceGroup\
    --query id --output tsv)
Run the following command to create and assign the Storage Blob Data Owner role. This role gives you the permissions to manage containers and items.

az role assignment create --assignee $userPrincipal \
    --role "Storage Blob Data Owner" \
    --scope $resourceID
Create a .NET console app to create containers and items

Now that the needed resources are deployed to Azure the next step is to set up the console application. The following steps are performed in the cloud shell.

Run the following commands to create a directory to contain the project and change into the project directory.

mkdir azstor
cd azstor
Create the .NET console application.

dotnet new console
Run the following commands to add the required packages in the application.

dotnet add package Azure.Storage.Blobs
dotnet add package Azure.Identity
Run the following command to create a data folder in your project.

mkdir data
Now it's time to add the code for the project.

Add the starter code for the project

Run the following command in the cloud shell to begin editing the application.

code Program.cs
Replace any existing contents with the following code. Be sure to review the comments in the code.

using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Identity;

Console.WriteLine("Azure Blob Storage exercise\n");

// Create a DefaultAzureCredentialOptions object to configure the DefaultAzureCredential
DefaultAzureCredentialOptions options = new()
{
    ExcludeEnvironmentCredential = true,
    ExcludeManagedIdentityCredential = true
};

// Run the examples asynchronously, wait for the results before proceeding
ProcessAsync().GetAwaiter().GetResult();

Console.WriteLine("\nPress enter to exit the sample application.");
Console.ReadLine();

async Task ProcessAsync()
{
    // CREATE A BLOB STORAGE CLIENT

    // CREATE A CONTAINER

    // CREATE A LOCAL FILE FOR UPLOAD TO BLOB STORAGE

    // UPLOAD THE FILE TO BLOB STORAGE

    // LIST BLOBS IN THE CONTAINER

    // DOWNLOAD THE BLOB TO A LOCAL FILE
}
Press ctrl+s to save your changes, and continue to the next step.

Add code to complete the project

Throughout the rest of the exercise you add code in specified areas to create the full application.

Locate the // CREATE A BLOB STORAGE CLIENT comment, then add the following code directly beneath the comment. The BlobServiceClient acts as the primary entry point for managing containers and blobs in a storage account. The client uses the DefaultAzureCredential for authentication. Be sure to replace YOUR_ACCOUNT_NAME with the name you recorded earlier.

// Create a credential using DefaultAzureCredential with configured options
string accountName = "YOUR_ACCOUNT_NAME"; // Replace with your storage account name

// Use the DefaultAzureCredential with the options configured at the top of the program
DefaultAzureCredential credential = new DefaultAzureCredential(options);

// Create the BlobServiceClient using the endpoint and DefaultAzureCredential
string blobServiceEndpoint = $"https://{accountName}.blob.core.windows.net";
BlobServiceClient blobServiceClient = new BlobServiceClient(new Uri(blobServiceEndpoint), credential);
Press ctrl+s to save your changes, and continue to the next step.

Locate the // CREATE A CONTAINER comment, then add the following code directly beneath the comment. Creating a container includes creating an instance of the BlobServiceClient class, and then calling the CreateBlobContainerAsync method to create the container in your storage account. A GUID value is appended to the container name to ensure that it's unique. The CreateBlobContainerAsync method fails if the container already exists.

//Create a unique name for the container
string containerName = "wtblob" + Guid.NewGuid().ToString();

// Create the container and return a container client object
Console.WriteLine("Creating container: " + containerName);
BlobContainerClient containerClient =
    await blobServiceClient.CreateBlobContainerAsync(containerName);

// Check if the container was created successfully
if (containerClient != null)
{
    Console.WriteLine("Container created successfully, press 'Enter' to continue.");
    Console.ReadLine();
}
else
{
    Console.WriteLine("Failed to create the container, exiting program.");
    return;
}
Press ctrl+s to save your changes, and continue to the next step.

Find the // CREATE A LOCAL FILE FOR UPLOAD TO BLOB STORAGE comment, then add the following code directly beneath the comment. This creates a file in the data directory that is uploaded to the container.

// Create a local file in the ./data/ directory for uploading and downloading
Console.WriteLine("Creating a local file for upload to Blob storage...");
string localPath = "./data/";
string fileName = "wtfile" + Guid.NewGuid().ToString() + ".txt";
string localFilePath = Path.Combine(localPath, fileName);

// Write text to the file
await File.WriteAllTextAsync(localFilePath, "Hello, World!");
Console.WriteLine("Local file created, press 'Enter' to continue.");
Console.ReadLine();
Press ctrl+s to save your changes, and continue to the next step.

Locate the // UPLOAD THE FILE TO BLOB STORAGE comment, then add the following code directly beneath the comment. The code gets a reference to a BlobClient object by calling the GetBlobClient method on the container created in the previous section. It then uploads a generated local file using the UploadAsync method. This method creates the blob if it doesn't already exist, and overwrites it if it does.

// Get a reference to the blob and upload the file
BlobClient blobClient = containerClient.GetBlobClient(fileName);

Console.WriteLine("Uploading to Blob storage as blob:\n\t {0}", blobClient.Uri);

// Open the file and upload its data
using (FileStream uploadFileStream = File.OpenRead(localFilePath))
{
    await blobClient.UploadAsync(uploadFileStream);
    uploadFileStream.Close();
}

// Verify if the file was uploaded successfully
bool blobExists = await blobClient.ExistsAsync();
if (blobExists)
{
    Console.WriteLine("File uploaded successfully, press 'Enter' to continue.");
    Console.ReadLine();
}
else
{
    Console.WriteLine("File upload failed, exiting program..");
    return;
}
Press ctrl+s to save your changes, and continue to the next step.

Locate the // LIST BLOBS IN THE CONTAINER comment, then add the following code directly beneath the comment. You list the blobs in the container with the GetBlobsAsync method. In this case, only one blob was added to the container, so the listing operation returns just that one blob.

Console.WriteLine("Listing blobs in container...");
await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
{
    Console.WriteLine("\t" + blobItem.Name);
}

Console.WriteLine("Press 'Enter' to continue.");
Console.ReadLine();
Press ctrl+s to save your changes, and continue to the next step.

Locate the // DOWNLOAD THE BLOB TO A LOCAL FILE comment, then add the following code directly beneath the comment. The code uses the DownloadAsync method to download the blob created previously to your local file system. The example code adds a suffix of "DOWNLOADED" to the blob name so that you can see both files in local file system.

// Adds the string "DOWNLOADED" before the .txt extension so it doesn't
// overwrite the original file

string downloadFilePath = localFilePath.Replace(".txt", "DOWNLOADED.txt");

Console.WriteLine("Downloading blob to: {0}", downloadFilePath);

// Download the blob's contents and save it to a file
BlobDownloadInfo download = await blobClient.DownloadAsync();

using (FileStream downloadFileStream = File.OpenWrite(downloadFilePath))
{
    await download.Content.CopyToAsync(downloadFileStream);
}

Console.WriteLine("Blob downloaded successfully to: {0}", downloadFilePath);
Press ctrl+s to save the file, then ctrl+q to exit the editor.

Sign into Azure and run the app

In the cloud shell command-line pane, enter the following command to sign into Azure.

az login
You must sign into Azure - even though the cloud shell session is already authenticated.

Note: In most scenarios, just using az login will be sufficient. However, if you have subscriptions in multiple tenants, you may need to specify the tenant by using the --tenant parameter. See Sign into Azure interactively using Azure CLI for details.

Run the following command to start the console app. The app will pause many times during execution waiting for you to press any key to continue. This gives you an opportunity to view the messages in the Azure portal.

dotnet run
In the Azure portal, navigate to the Azure Storage account you created.

Expand Data storage in the left navigation and select Containers.

Select the container the application created and you can view the blob that was uploaded.

Run the two commands below to change into the data directory and list the files that were uploaded and downloaded.

cd data
ls
Clean up resources

Now that you finished the exercise, you should delete the cloud resources you created to avoid unnecessary resource usage.

Navigate to the resource group you created and view the contents of the resources used in this exercise.

On the toolbar, select Delete resource group.

Enter the resource group name and confirm that you want to delete it.

CAUTION: Deleting a resource group deletes all resources contained within it. If you chose an existing resource group for this exercise, any existing resources outside the scope of this
