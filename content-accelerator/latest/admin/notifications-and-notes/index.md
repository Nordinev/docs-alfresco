---
title: Notifications and Notes
---

# Notifications

## Notification Overview
Sending notifications is an easy way to alert users or users in groups about a folder or document in an arbitrary fashion. There are no predefined events that send off a notification - when enabled, the user can click the Send Notification action at any time. The user selects the user to notify, an optional due date, and can write a message in a WYSIWYG editor. Separate notifications are created for each user, and an email is sent to each user containing a link to the document or folder and the message.

Users who have received a notification can either click the link in the email received to view the item or log in to ACA to view all notifications by clicking the appropriate icon in the header of the application. They can then delete the notification.



## Configuring Notifications
As of ACA 3.4, notifications require an Ad-hoc form and workflow configurations.  The following steps should be used to set up notifications:

1. Configure an Ad-Hoc Form for sending a notification.  For example:
    - Form Name: `sendNotification`
    - Attrs:
      - `bpm_assignees` - User Assignees - Autocomplete, repeating
      - `bpm_groupAssignee` - Group Assignees - Autocomplete, repeating
      - `bpm_workflowDueDate` - Due Date - Date, single, optional
      - `bpm_comment` - Comments - Textarea (with wysiwyg option checked if desired), single, optional
    - Additionally any other attributes can be added in an ad-hoc manner.  Example: `notification_type`, etc
2. In the Workflow section, set the workflow form created in the previous step as the Start Form for the `HPI Notification` workflow.
3. When configuring the Send Notification action, choose the form created in step 1 in the action config.

Note - currently only one `sendNotification` form is supported.  Future releases may enable trac-specific overrides.



# External Notifications

External notifications are available starting with the ACA 3.4 release.  Basic workflow task notification support on both Slack and MS Teams is supported.

## OC Setup
By default External Notifications are off by default.  To turn this on, set the following OC properties within your override properties file.

```properties
send.external.notifications=true
```

## Slack Setup
### Step 1: Navigate to https://api.slack.com and sign in
Click on the tab **_Your Apps_** in the top right corner. If you have not created an app yet, then hit the **_Create New App_** button. Give it a name and select the workspace where you want the application to live. If you already have an app created, then click on it.

### Step 2: Customize app Display Information (optional)
When the app was clicked on, the basic information should have been loaded. At the bottom of this page there is the Display Information section. This gives the ability to customize what the particular app will look like to the user. Go ahead and customize this section now or skip to Step 3.

### Step 3: Add a Bot User
Find the submenu under **_Features_** called **_Bot Users_** and click on it. This will bring up an option to turn on and add a bot user. You can customize the display name and default username if you choose. Also, it gives the option to show that the bot user is always online. 

### Step 4: Reinstall/Install the app
If you have not installed the application, now would be a good time to do that to the particular workspace. You can do that by clicking on the **_Install App_** submenu under the **_Settings_** menu. It will also have you choose a channel for the bot to have permissions to post. This is a default channel the bot will post to if a channel is not specified within the post message API call.

### Step 5: Set up the app's scopes
OAuth scopes let you specify exactly how your app needs to access a Slack user's account. Different Slack API calls require different scope permissions. The scope permissions that you need to add are the following: `channels:read, chat:write:bot, users:read, users:read:email`. Once adding these, it will need you to reinstall your app.

### Step 6: Grab the Bot User OAuth Access Token
Click on the submenu **_OAuth & Permissions_** under the **_Features_** menu. You should be able to see a **_Bot User OAuth Access Token_**. This will be needed to override the `slack.auth.token` property value.

### Step 7: Override the Slack Properties
Now that we have the bot user set up, you can now successfully override the properties to get Slack external notifications connected. 
* In your environment's overlay folder, find or create the override-placeholders.properties file. 
* If you need more information about overlay folder in OC follow the steps in this link https://github.com/tsgrp/OpenContent/wiki/Building-OpenContent#configure-your-build-environment. 
* Put the properties `send.external.notifications=true` and `slack.auth.token=xxxx-yourAuthToken`. 
* Re-build OC and the Slack external notification connection should be all set up!

## MS Teams Setup
### Step 1: Navigate to https://portal.azure.com/ and sign in
In the search bar, search for **_App Registrations_**. Click on the **_New registration_** plus button in the top left corner of the view.

### Step 2: Register the application (API)
* Fill out the name field with whatever name you want to give this application. 
* Then, fill out who can use this application or access this API. This will depend on use-cases, but typically should be restricted to Single tenant (the company org account EX: tsgrplab). 
* Leave the Redirect URI empty, this will not be used for our external notification API.

### Step 3: App Overview
The **_Overview_** tab should showing right now. Some important variables to note here are the **Application (client) ID** and the **Directory (tenant) ID**. Take note of these two variables, they will be used below.

### Step 4: API Permissions
Navigate to the **_API permissions_** tab on the left menu bar. Click the plus button to **_Add a permission_**. These permissions are the access/scopes you are giving to this API. Good practice is to not give your API more permissions than it needs, these can be added/subtracted at anytime. Go ahead and add the following permissions: 

* Chat.Read
* Chat.ReadWrite
* Group.Read.All
* Group.ReadWrite.All
* User.Read
* User.Read.All
* User.ReadBasic.All
* User.ReadWrite
* User.ReadWrite.All

 ### Step 5: Grant/Request Admin Consent
You may notice there are some permissions that need Admin Consent. These are the permissions of `Group.Read.All`, `Group.ReadWrite.All`, `User.Read.All`, `User.ReadWrite.All`. If you are not the admin, you will have to get them to grant these permissions. To grant the permissions, follow this documentation link: https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/grant-admin-consent#grant-admin-consent-in-app-registrations

 ### Step 6: Authentication
Click on the **_Authentication_** tab on the left side menu. There are 2 different ways the following configurations can be filled out on this screen depending on if your azure account loads their new or old view for this section.

1. The _"new"_ experience, which should be loaded by default (this is Azure's update to some of these views). 
     - Click on the Add a platform. It will ask you to add a redirect URI. We don't necessarily need one for this API, but it's a required field. So give it your application url root/homepage EX: http://localhost:8080. 
     - Leave the Logout Url section blank
     - Select the check-boxes for the **Access token** and **ID tokens** (this is very important because it lets the application generate a access token to be used within the API calls)
     - Once that is created, scroll to the bottom of the page to the **_Advanced Settings_** header. Turn this on. We are not using re-direct URI's. That means we are grabbing tokens without user involvement/sign-in window.
     - Click Save at the top to save all these changes. 

2. The _"old"_ experience. You can tell if you are on the old, if the button to the right of the Discard says _Switch to the new experience_. 
     - Skip the Redirect URIs section and do not input a logout url. 
     - Under the **_Implicit grant_** section, select the check-boxes for the **Access token** and **ID tokens** (this is very important because it lets the application generate a access token to be used within the API calls)
     - Click Yes for the **_Default client type_** to treat application as a public client.
     - Click save at the top to save all these changes.

 ### Step 7: Creation of a Service Account User
A service account user is needed to send a direct 1:1 message to the user that is receiving the task notification.
* The admin needs to add an account to the organization/team that should have a name that signifies its purpose. EX: Service Account (https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/add-users-azure-active-directory)
* >**Important:** This user does not need elevated permissions and needs to be apart of the team that the user who is receiving the task notification is in.**
* Note the username and password for the account this will be used in the next step.

 ### Step 8: Encrypt the Service Account User's Password
This is a valid user within your organization/team's azure directory. So, we need to encrypt the password before adding to our override property file.
* The steps to achieving the encryption can be followed here: https://github.com/tsgrp/OpenContent/wiki/Encrypting-a-Password

 ### Step 9: Override the Microsoft Teams Properties
Now that we have the App set up, you can now successfully override the properties to get Teams external notifications connected. 
* In your environment's overlay folder, find or create the override-placeholders.properties file. 
* If you need more information about overlay folder in OC follow the steps in this link https://github.com/tsgrp/OpenContent/wiki/Building-OpenContent#configure-your-build-environment. 
* Put the following properties in this file: 
     - `teams.team.id= ` this is the group id for the Microsoft Teams team. Follow this article, but instead of tenant id in the article, grab the **GROUP ID** from the url they show you how to get. https://teams.handsontek.net/2019/04/09/how-to-get-microsoft-teams-tenant-id/
     - `teams.app.id=` this is the Application (client) ID from **Step 3**, https://github.com/tsgrp/HPI/wiki/External-Notifications#step-3-app-overview, in the **_Overview_** tab.
     - `teams.service.account.username=` this is the username from **Step 7**, https://github.com/tsgrp/HPI/wiki/External-Notifications#step-7-creation-of-a-service-account-user, for the service account user
     - `teams.service.account.password=` this is the encrypted password surrounded by "@{}" from **Step 8**, https://github.com/tsgrp/HPI/wiki/External-Notifications#step-8-encrypt-the-service-account-users-password
* Re-build OC and the Microsoft Teams external notification connection should be all set up!

 ### Microsoft Graph API Limitations (https://docs.microsoft.com/en-us/graph/overview?view=graph-rest-beta)
* Currently, the API calls utilized within this integration are limited and in beta. 
* The API call to send a direct message is only supported for work/school accounts (https://docs.microsoft.com/en-us/graph/api/chat-post-messages?view=graph-rest-beta&tabs=http) and is of the permission type delegated. 
     - The "Delegated" permission type defines a on-behalf-of-user permission. Essentially a user can only send a direct message to another user within the team (https://docs.microsoft.com/en-us/azure/active-directory/develop/developer-glossary#permissions). 
     - In the future, we hope this expands to include the "Application" permission type. The "Application" permission type would allow our application to direct message a user directly, without having to create a Service Account User that will be shown below.




# Folder Notes and Document Notes

## What are Folder and Document Notes?

### Folder Notes

Folder notes are notes attached to a folder. The default note type is hpi_note. An hpi_note has the following attributes:

* note_detail: abbreviated note_content <= 3000 characters
* note_type: a classification given to each note, such as "Authority", "Corrspondance, "Procedural", etc. pulled from form support in a note_type picklist
* note_attachment: if the note was created from Document Notes, note_attachment is the objectId of the parent document. Otherwise, it is undefined

These notes are stored in a hidden folder inside the parent container called .folder-notes (originally called .hpi-info).

### Document Notes

Notes can also be attached to individual documents with the Document Notes action. All document notes are stored in the .folder-notes folder of the parent folder of the document.

## Configuration

To configure Folder Notes, navigate to the admin panel > Stage Config > Folder Actions, and find the Folder Notes action in the list of available actions. This action can be configured as either a *Modal* action or a *Right-Side* action.

To configure Document Notes, navigate to the admin panel > Stage Config > DocViewer, and find the Document Notes action in the list of available actions.

Notes on some of the available configurations are below.

### Form to Display
This is the form that will display to the user when editing or adding a note.  It's recommended to have a 'createHPINote' form configured that you can use throughout the application (it's not trac-dependent).  The object type on the form should be `HPI Note`. Typically, we configure the following properties on the form:

*  Note Type (optional) - sometimes configured as a picklist
* note_content (required) - usually a hidden textarea
* note_detail (required) - usually a hidden textarea

### Note Object Type
The default note object type is "hpi_note". To configure otherwise, replace this value with the appropriate object type. 

### Note Relationship (required)
Ensure that the Note Relationship option is configured to :

**hpi:folder_note (alfresco)**