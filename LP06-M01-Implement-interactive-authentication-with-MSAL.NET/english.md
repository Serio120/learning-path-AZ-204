# LP06 M01: Implement interactive authentication with MSAL.NET 

[To keep in mind](#)

<h2 align="center">******************   Unformatted text *********************</h2>

In this exercise, you register an application in Microsoft Entra ID, then create a .NET console application that uses MSAL.NET to perform interactive authentication and acquire an access token for Microsoft Graph. You learn how to configure authentication scopes, handle user consent, and see how tokens are cached for subsequent runs.

Tasks performed in this exercise:

Register an application with the Microsoft identity platform
Create a .NET console app that implements the PublicClientApplicationBuilder class to configure authentication.
Acquire a token interactively using the user.read Microsoft Graph permission.
This exercise takes approximately 15 minutes to complete.

Before you start

To complete the exercise, you need:

An Azure subscription. If you don't already have one, you can sign up for one here: https://azure.microsoft.com/.

Visual Studio Code on one of the supported platforms.

.NET 8 or greater.

C# Dev Kit for Visual Studio Code.

Register a new application

Open a web browser and navigate to the Azure portal, https://portal.azure.com.

In the Sign in dialog box, copy, and paste in the username studentLYFKN@t008.gdazcs.com and then select Next.

In the Enter password dialog box, copy, and paste in the password ZTSkR7W5pUeAzzmg then select Sign in.

Note: You will be prompted to enter an authentication token.

Select Generate Authentication Code below.

You will have 60 seconds to sign in using this code before having to generate a new one.

In the Code dialog box, enter or copy, and paste in the Authentication code then select Verify

Important: You will have 60 seconds to sign in using the code before having to generate a new one.

On the Welcome to Microsoft Azure dialogue box click Maybe Later. If an Azure recommendations dialog box pops up click X.

In the portal, search for and select App registrations.

Select + New registration, and when the Register an application page appears, enter your application's registration information:

Field	Value
Name	Enter myMsalApplication
Supported account types	Select Accounts in this organizational directory only
Redirect URI (optional)	Select Public client/native (mobile & desktop) and enter http://localhost in the box to the right.
Select Register. Microsoft Entra ID assigns a unique application (client) ID to your app, and you're taken to your application's Overview page.

In the Essentials section of the Overview page record the Application (client) ID and the Directory (tenant) ID. The information is needed for the application.

Screenshot showing the location of the fields to copy.

Create a .NET console app to acquire a token

Now that the needed resources are deployed to Azure the next step is to set up the console application. The following steps are performed in your local environment.

Create a folder named authapp, or a name of your choosing, for the project.

Launch Visual Studio Code and select File > Open folder... and select the project folder.

Select View > Terminal to open a terminal.

Run the following command in the VS Code terminal to create the .NET console application.

dotnet new console
Run the following commands to add the Microsoft.Identity.Client and dotenv.net packages to the project.

dotnet add package Microsoft.Identity.Client
dotnet add package dotenv.net
Note: If you are facing issues, enter the following script to ensure the packages are available and try step 5 again:

dotnet nuget add source https://api.nuget.org/v3/index.json -n nuget.org

Configure the console application

In this section you create, and edit, a .env file to hold the secrets you recorded earlier.

Select File > New file... and create a file named .env in the project folder.

Open the .env file and add the following code. Replace YOUR_CLIENT_ID, and YOUR_TENANT_ID with the values you recorded earlier.

CLIENT_ID="YOUR_CLIENT_ID"
TENANT_ID="YOUR_TENANT_ID"
Press ctrl+s to save your changes.

Add the starter code for the project

Open Program.cs file and replace any existing contents with the following code. Be sure to review the comments in the code.

using Microsoft.Identity.Client;
using dotenv.net;

// Load environment variables from .env file
DotEnv.Load();
var envVars = DotEnv.Read();

// Retrieve Azure AD Application ID and tenant ID from environment variables
string _clientId = envVars["CLIENT_ID"];
string _tenantId = envVars["TENANT_ID"];

// ADD CODE TO DEFINE SCOPES AND CREATE CLIENT

// ADD CODE TO ACQUIRE AN ACCESS TOKEN
Press ctrl+s to save your changes.

Add code to complete the application

Locate the // ADD CODE TO DEFINE SCOPES AND CREATE CLIENT comment and add the following code directly after the comment. Be sure to review the comments in the code.

// Define the scopes required for authentication
string[] _scopes = { "User.Read" };

// Build the MSAL public client application with authority and redirect URI
var app = PublicClientApplicationBuilder.Create(_clientId)
    .WithAuthority(AzureCloudInstance.AzurePublic, _tenantId)
    .WithDefaultRedirectUri()
    .Build();
Locate the // ADD CODE TO ACQUIRE AN ACCESS TOKEN comment and add the following code directly after the comment. Be sure to review the comments in the code.

// Attempt to acquire an access token silently or interactively
AuthenticationResult result;
try
{
    // Try to acquire token silently from cache for the first available account
    var accounts = await app.GetAccountsAsync();
    result = await app.AcquireTokenSilent(_scopes, accounts.FirstOrDefault())
                .ExecuteAsync();
}
catch (MsalUiRequiredException)
{
    // If silent token acquisition fails, prompt the user interactively
    result = await app.AcquireTokenInteractive(_scopes)
                .ExecuteAsync();
}

// Output the acquired access token to the console
Console.WriteLine($"Access Token:\n{result.AccessToken}");
Press ctrl+s to save the file, then ctrl+q to exit the editor.

Run the application

Now that the app is complete it's time to run it.

Start the application by running the following command:

dotnet run
The app opens the default browser prompting you to select the account you want to authenticate with. If there are multiple accounts listed select the one associated with the tenant used in the app.

If this is the first time you've authenticated to the registered app you receive a Permissions requested notification asking you to approve the app to sign you in and read your profile, and maintain access to data you have given it access to. Select Accept.

Screenshot showing the permissions requested notification

You should see the results similar to the example below in the console.

Access Token:
eyJ0eXAiOiJKV1QiLCJub25jZSI6IlZF.........

Start the application a second time and notice you no longer receive the Permissions requested notification. The permission you granted earlier was cached.

Note: If you have mutliple accounts, and With some account configurations, you might see the notification again.
