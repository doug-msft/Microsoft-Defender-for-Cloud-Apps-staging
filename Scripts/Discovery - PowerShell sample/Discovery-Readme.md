This sample guide/scripts will help you automate uploading firewall logs
to Defender for Cloud Apps using API.

This helps eliminate the need to deploy a log collector, and makes it
more flexible to deploy and maintain.

 

In addition, this guide is using the Microsoft Graph API, instead of the
legacy Defender for Cloud Apps APIs, which is in a path to being
deprecated in the future.

 

Note from [[Managing API tokens - Microsoft Defender for Cloud Apps \|
Microsoft
Learn]{.underline}](https://learn.microsoft.com/en-us/defender-cloud-apps/api-authentication)

 

![Text Description automatically
generated](media/image1.png){width="6.5in"
height="1.0986111111111112in"}

 

This guide is based on the following documentation, but some
customization is needed as described below to use it for Shadow IT
Discovery purposes .

 

  ----------------------------------------------------------------------------------------------------------------
  Intro:           [[REST API - Microsoft Defender for Cloud Apps \| Microsoft
                   Learn]{.underline}](https://learn.microsoft.com/en-us/defender-cloud-apps/api-introduction)
  ---------------- -----------------------------------------------------------------------------------------------
  Authentication   [[Managing API tokens - Microsoft Defender for Cloud Apps \| Microsoft
                   Learn]{.underline}](https://learn.microsoft.com/en-us/defender-cloud-apps/api-authentication)

  Discovery API    [[Defender for Cloud Apps Cloud Discovery API - Microsoft Defender for Cloud Apps \| Microsoft
                   Learn]{.underline}](https://learn.microsoft.com/en-us/defender-cloud-apps/api-discovery)
  ----------------------------------------------------------------------------------------------------------------

 

Here are the main tasks:

 

1: Create an Azure AD App

2: Give the App permissions over Defender for Cloud Apps APIs

3: Document key elements from the App, such as App ID, Tenant ID and App
Secret

4: Document the Defender for Cloud Apps API URL

5: Create a new Data Source in Defender for Cloud Apps to receive the
logs

6: Configure the PowerShell scripts with the data collected above and
test it.

7: Schedule the script to run according to your preferences.

 

Here are detailed steps:

 

Step 1-Create an App using either [[Application
Context]{.underline}](https://learn.microsoft.com/en-us/defender-cloud-apps/api-authentication-application)
or [[User
Context]{.underline}](https://learn.microsoft.com/en-us/defender-cloud-apps/api-authentication-user)
and give it consent.

In general, you'll need to take the following steps to use the APIs:

 

-   Create an Azure Active Directory (Azure AD) application

-   Get an access token using this application

-   Use the token to access the Defender for Cloud Apps API

 

 

 

To create an App in Azure AD, follow the steps 1, 2 and 3 on this
documentation

[[https://learn.microsoft.com/en-us/defender-cloud-apps/api-authentication-application#create-an-app]{.underline}](https://learn.microsoft.com/en-us/defender-cloud-apps/api-authentication-application#create-an-app)

 

When you get to step 4 on, use the instructions below instead of the
steps in the public doc.

You will need to customize the permissions needed for managing Cloud
Discovery

 

Step 4: Give Permissions to the \"Microsoft Cloud App Security\"
(Application ID 05a65629-4c1b-48c1-a78b-804c4abdd4af)

![Graphical user interface, text, application, email Description
automatically generated](media/image2.png){width="6.5in"
height="2.6375in"}

 

Step 5: Give it permissions over Discovery.manage and discovery.read

![Graphical user interface, text, application, email Description
automatically generated](media/image3.png){width="6.5in"
height="3.745138888888889in"}

 

Step 6: Grant Admin Consent

![Graphical user interface, text Description automatically
generated](media/image4.png){width="5.239583333333333in"
height="1.2395833333333333in"}

 

Step 7: Get an app secret under \"Certificates & Secrets\" and copy the
value

![Graphical user interface, text, application, email Description
automatically generated](media/image5.png){width="6.5in"
height="2.886111111111111in"}

 

Step 8: Go to \"Overview and copy the Application (Client ID) /
Directory (Tenant ID)

![Graphical user interface, text, application, email Description
automatically generated](media/image6.png){width="6.5in"
height="2.8875in"}

 

Step 9: Copy your Tenant API URL from the Defender for Cloud Apps
settings/Settings \> System \> About page

[[https://security.microsoft.com/cloudapps/settings?tabid=about]{.underline}](https://security.microsoft.com/cloudapps/settings?tabid=about)

 

![Graphical user interface, application, Word Description automatically
generated](media/image7.png){width="6.5in"
height="2.9138888888888888in"}

 

 

Step 10: Create a Data Source in the Defender for Cloud Apps / Settings
\> Cloud apps \> Cloud Discovery \> Automatic Log upload

Copy the data source name

[[https://security.microsoft.com/cloudapps/settings?tabid=discovery-autoUpload&innertab=dataSources]{.underline}](https://security.microsoft.com/cloudapps/settings?tabid=discovery-autoUpload&innertab=dataSources)

 

![Graphical user interface, application Description automatically
generated](media/image8.png){width="6.5in" height="3.859027777777778in"}

 

Make sure to copy these values to add to the script later:

  -----------------------------------------------------------------------
  Data               From                       Value
  ------------------ -------------------------- -------------------------
  Application        Azure AD App / Overview     
  (Client ID)                                   

  Directory (Tenant  Azure AD App / Overview     
  ID)                                           

  Application Secret Azure AD App /              
  (value)            Certificates & Secrets     

  Tenant API URL     Defender for Cloud Apps     

  Data Source Name   Defender for Cloud Apps     
  -----------------------------------------------------------------------

 

Step 11: Download the Discovery-sample.zip folder and extract to a place
of your preference and modify these two files:

 

Get-AppToken-MDA.ps1

+-----------------------------------------------------------------------+
| \$tenantId = \'\<Your Tenant ID\>\' \### Paste your tenant ID here    |
|                                                                       |
| \$appId = \'\<Your app ID\>\' \### Paste your Application ID here     |
|                                                                       |
| \$appSecret = \'\<Your App secret key value\>\' \### Paste your       |
| Application key here                                                  |
+=======================================================================+
+-----------------------------------------------------------------------+

 

Upload-DiscoveryLogs-MDA-sample.ps1

 

+-----------------------------------------------------------------------+
| \$TenantURL = \"\<Your Tenant URL\>\" \# you can find this at         |
| ([[link]{.underl                                                      |
| ine}](https://security.microsoft.com/cloudapps/settings?tabid=about)) |
|                                                                       |
| \$LogSource = \"PALO_ALTO\" #Change to your log source                |
| ([[link]{.underline}](https://learn.microsoft.com/en-u                |
| s/defender-cloud-apps/api-discovery-initiate#request-url-parameters)) |
|                                                                       |
| \$DataSourceName = \"PaloAltoDemo\" #Create a Data Source in          |
| \"Automatic log upload\" in                                           |
| ([[link]{.underline}](https://security.microsoft.com                  |
| /cloudapps/settings?tabid=discovery-autoUpload&innertab=dataSources)) |
|                                                                       |
| \$sourceScriptFolder = \"\<Ex:C:\\Scripts\\Discover\>\" #This is the  |
| path to the Discover folder where the PowerShell scripts are          |
+=======================================================================+
+-----------------------------------------------------------------------+

 

Step 12: Once the variables are configured, it\'s time to test the
scripts.

a.  Copy some sample logs to the \"New Logs\" folder

b.  Run the Upload-DiscoveryLogs-MDA.ps1 script.

c.  Verify if the logs were uploaded successfully by running the
    List-ContinuousReports-MDA.ps1 and comparing the numbers for the
    data source you are using.

d.  Verify in the Defender for Cloud Apps portal if you can see a new
    report in Cloud Discovery for the data source you created.

 

Step 13: Configure your firewall/proxy logs to save new logs to the
\"New logs\" folder of the machine running the script, or to a location
the machine can read from.

 

Step 14: If everything works well, you can schedule the
Upload-DiscoveryLogs-MDA.ps1 script to run a couple of times a day,
depending how often you generate and save your firewall logs to the
\"New Logs\" folder.
