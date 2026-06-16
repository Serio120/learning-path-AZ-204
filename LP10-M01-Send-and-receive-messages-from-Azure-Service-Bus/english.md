# LP10 M01: Send and receive messages from Azure Service Bus

[To keep in mind](https://github.com/Serio120/learning-path-AZ-204/blob/main/LP09-M01-Route-events-to-a-custom-endpoint-with-Azure-Event-Grid/notes.md#-1-ver-cada-variable-individualmente)

<h2 align="center">******************   Unformatted text *********************</h2>

Send and receive messages from Azure Service Bus

In this exercise, you create and configure Azure Service Bus resources, then build a .NET app to send and receive messages using the Azure.Messaging.ServiceBus SDK. You learn how to provision a Service Bus namespace and queue, assign permissions, and interact with messages programmatically.

Tasks performed in this exercise:

Create Azure Service Bus resources
Assign a role to your Microsoft Entra user name
Create a .NET console app to send and receive messages
Clean up resources
This exercise takes approximately 30 minutes to complete.

Create Azure Event Hubs resources

In this section of the exercise you create the needed resources in Azure with the Azure CLI.

Open a web browser and navigate to the Azure portal, https://portal.azure.com.

In the Sign in dialog box, copy, and paste in the username student1ZPD7@t008.gdazcs.com and then select Next.

In the Enter password dialog box, copy, and paste in the password iXJeCXi6ZywCC8Ny then select Sign in.

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

Many of the commands require unique names and use the same parameters. Creating some variables will reduce the changes needed to the commands that create resources. Run the following commands to create the needed variables. Replace myResourceGroup with the name you you're using for this exercise. If you changed the location in the previous step, make the same change in the location variable.

resourceGroup=LP11M1-6WFCM0ZC1R
location=eastus
namespaceName=svcbusns$RANDOM
You will need the name assigned to the namespace later in this exercise. Run the following command and record output.

echo $namespaceName
Create an Azure Service Bus namespace and queue

Create a Service Bus messaging namespace. The following command creates a namespace using the variable you created earlier. The operation takes a few minutes to complete.

az servicebus namespace create\
    --resource-group $resourceGroup\
    --name $namespaceName\
    --location $location
Now that a namespace is created, you need to create a queue to hold the messages. Run the following command to create a queue named myqueue.

az servicebus queue create --resource-group $resourceGroup\
    --namespace-name $namespaceName\
    --name myqueue
Assign a role to your Microsoft Entra user name

To allow your app to send and receive messages, assign your Microsoft Entra user to the Azure Service Bus Data Owner role at the Service Bus namespace level. This gives your user account permission to manage and access queues and topics using Azure RBAC. Perform the following steps in the cloud shell.

Run the following command to retrieve the userPrincipalName from your account. This represents who the role will be assigned to.

userPrincipal=$(az rest --method GET --url https://graph.microsoft.com/v1.0/me\
    --headers 'Content-Type=application/json'\
    --query userPrincipalName --output tsv)
Run the following command to retrieve the resource ID of the Service Bus namespace. The resource ID sets the scope for the role assignment to a specific namespace.

resourceID=$(az servicebus namespace show --name $namespaceName\
    --resource-group $resourceGroup\
    --query id --output tsv)
Run the following command to create and assign the Azure Service Bus Data Owner role.

az role assignment create --assignee $userPrincipal\
    --role "Azure Service Bus Data Owner"\
    --scope $resourceID
Create a .NET console app to send and receive messages

Now that the needed resources are deployed to Azure the next step is to set up the console application. The following steps are performed in the cloud shell.

Run the following commands to create a directory to contain the project and change into the project directory.

mkdir svcbus
cd svcbus
Create the .NET console application.

dotnet new console
Run the following commands to add the Azure.Messaging.ServiceBus and Azure.Identity packages to the project.

dotnet add package Azure.Messaging.ServiceBus
dotnet add package Azure.Identity
Add the starter code for the project

Run the following command in the cloud shell to begin editing the application.

code Program.cs
Replace any existing contents with the following code. Be sure to review the comments in the code, and replace with the Service Bus namespace you recorded earlier.

using Azure.Messaging.ServiceBus;
using Azure.Identity;
using System.Timers;

// TODO: Replace <YOUR-NAMESPACE> with your Service Bus namespace
string svcbusNameSpace = "<YOUR-NAMESPACE>.servicebus.windows.net";
string queueName = "myQueue";

// ADD CODE TO CREATE A SERVICE BUS CLIENT

// ADD CODE TO SEND MESSAGES TO THE QUEUE

// ADD CODE TO PROCESS MESSAGES FROM THE QUEUE

// Dispose client after use
await client.DisposeAsync();
Press ctrl+s to save your changes.

Add code to send messages to queue

Now it's time to add code to create the Service Bus client and send a batch of messages to the queue.

Locate the // ADD CODE TO CREATE A SERVICE BUS CLIENT comment and add the following code directly after the comment. Be sure to review the code and comments.

// Create a DefaultAzureCredentialOptions object to configure the DefaultAzureCredential
DefaultAzureCredentialOptions options = new()
{
    ExcludeEnvironmentCredential = true,
    ExcludeManagedIdentityCredential = true
};

// Create a Service Bus client using the namespace and DefaultAzureCredential
// The DefaultAzureCredential will use the Azure CLI credentials, so ensure you are logged in
ServiceBusClient client = new(svcbusNameSpace, new DefaultAzureCredential(options));
Locate the // ADD CODE TO SEND MESSAGES TO THE QUEUE comment and add the following code directly after the comment. Be sure to review the code and comments.

// Create a sender for the specified queue
ServiceBusSender sender = client.CreateSender(queueName);

// create a batch
using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();

// number of messages to be sent to the queue
const int numOfMessages = 3;

for (int i = 1; i <= numOfMessages; i++)
{
    // try adding a message to the batch
    if (!messageBatch.TryAddMessage(new ServiceBusMessage($"Message {i}")))
    {
        // if it is too large for the batch
        throw new Exception($"The message {i} is too large to fit in the batch.");
    }
}

try
{
    // Use the producer client to send the batch of messages to the Service Bus queue
    await sender.SendMessagesAsync(messageBatch);
    Console.WriteLine($"A batch of {numOfMessages} messages has been published to the queue.");
}
finally
{
    // Calling DisposeAsync on client types is required to ensure that network
    // resources and other unmanaged objects are properly cleaned up.
    await sender.DisposeAsync();
}

Console.WriteLine("Press any key to continue");
Console.ReadKey();
Press ctrl+s to save the file, then continue with the exercise.

Add code to process messages in the queue

Locate the // ADD CODE TO PROCESS MESSAGES FROM THE QUEUE comment and add the following code directly after the comment. Be sure to review the code and comments.

// Create a processor that we can use to process the messages in the queue
ServiceBusProcessor processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());

// Idle timeout in milliseconds, the idle timer will stop the processor if there are no more
// messages in the queue to process
const int idleTimeoutMs = 3000;
System.Timers.Timer idleTimer = new(idleTimeoutMs);
idleTimer.Elapsed += async (s, e) =>
{
    Console.WriteLine($"No messages received for {idleTimeoutMs / 1000} seconds. Stopping processor...");
    await processor.StopProcessingAsync();
};

try
{
    // add handler to process messages
    processor.ProcessMessageAsync += MessageHandler;

    // add handler to process any errors
    processor.ProcessErrorAsync += ErrorHandler;

    // start processing
    idleTimer.Start();
    await processor.StartProcessingAsync();

    Console.WriteLine($"Processor started. Will stop after {idleTimeoutMs / 1000} seconds of inactivity.");
    // Wait for the processor to stop
    while (processor.IsProcessing)
    {
        await Task.Delay(500);
    }
    idleTimer.Stop();
    Console.WriteLine("Stopped receiving messages");
}
finally
{
    // Dispose processor after use
    await processor.DisposeAsync();
}

// handle received messages
async Task MessageHandler(ProcessMessageEventArgs args)
{
    string body = args.Message.Body.ToString();
    Console.WriteLine($"Received: {body}");

    // Reset the idle timer on each message
    idleTimer.Stop();
    idleTimer.Start();

    // complete the message. message is deleted from the queue.
    await args.CompleteMessageAsync(args.Message);
}

// handle any errors when receiving messages
Task ErrorHandler(ProcessErrorEventArgs args)
{
    Console.WriteLine(args.Exception.ToString());
    return Task.CompletedTask;
}
Press ctrl+s to save the file, then ctrl+q to exit the editor.

Sign into Azure and run the app

In the cloud shell command-line pane, enter the following command to sign into Azure.

az login
You must sign into Azure - even though the cloud shell session is already authenticated.

Note: In most scenarios, just using az login will be sufficient. However, if you have subscriptions in multiple tenants, you may need to specify the tenant by using the --tenant parameter. See Sign into Azure interactively using Azure CLI for details.

Run the following command to start the console app. The app will pause a various stages and prompt you to press a key to continue. This gives you an opportunity to view the messages in the Azure portal.

dotnet run
In the Azure portal, navigate to the Service Bus namespace you created.

Select myqueue at the bottom of the Overview window.

Select Service Bus Explorer in the left navigation pane.

Select Peek from start and the three messages should appear after a few seconds.

In the cloud shell, press any key to continue and the application will process the three message.

Return to the portal after the application has completed processing the messages. Select Peek from start again and notice there are no messages in the queue.

