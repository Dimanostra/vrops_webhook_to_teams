# vrops_webhook_to_teams

VMware Aria Operations (vROps) Notifications to MS Teams via Power Automate
Hi everyone! In this guide, I want to share my experience setting up a workflow to send vROps Alerts directly to Microsoft Teams. This integration allows your team to receive critical infrastructure alerts in a collaborative environment in real-time.

Prerequisites: Microsoft Teams Setup
First, you need to prepare your workspace in Microsoft Teams. I'll assume you already know how to create a Team and a Channel, so we’ll skip those basics and get straight to the integration requirements.

1. Prepare the Excel Database
For this flow to work efficiently (and for potential logging/tracking), we will use an Excel file as an intermediate data point.

Navigate to the Files tab (or click Share) within your chosen Teams channel.

Create a new Excel Workook (give it a recognizable name, e.g., vrops_alerts_log.xlsx).

Define your table headers. These columns will store the data sent from vROps. Recommended columns include:

AlertId  |  MessageId

Make sure to format your headers as a Table (Insert > Table) within the Excel file so that Power Automate can recognize the rows.

Power Automate Configuration
Now, let's set up the logic that will bridge vROps and Teams. To make things easier, I have prepared a pre-configured flow template.

2. Import the Flow Template
Instead of building the workflow from scratch, you can import the provided package:

Log in to your Power Automate portal.

Navigate to My flows in the left-hand sidebar.

Click on Import and select Import Package (Legacy).

Upload the file: vROPSWebHookToTeamsChannel_20260130195249.zip.

3. Connection Setup
After uploading the package, you will need to map the connections to your own environment:

Review the connections: You will likely see red icons indicating that the connections (for Teams and Excel Online) need to be updated.

Select your account: For each connection, click Select during import and choose your existing connection or create a new one using your Microsoft 365 credentials.

Finalize: Once all indicators are green, click the Import button.

4. Configuring the Imported Flow
Once the import is successful, you need to link the Flow to your specific Teams environment and the Excel file you created earlier.

Click the Edit button on the imported workflow.

Expand all the steps in the process tree to ensure you don't miss any settings.

Teams Notifications Setup
In every step titled "Post card in a chat or channel" and "Reply with an adaptive card in a channel", configure the following:

Post as: Flow bot (usually default).

Post in: Channel.

Team: Select your specific Team from the dropdown.

Channel: Select the target Channel.

Excel Integration (Lookup Logic)
In all steps titled "List rows present in a table", set these values:

Location: Select your Team.

Document Library: Usually Documents.

File: Browse and select the vrops_alerts_log.xlsx file.

Table: Select the table name from the dropdown.

Extract Sensitivity Label: Yes.

Advanced parameters (Filter Query): AlertId eq '@{triggerBody()?['alertId']}'

Excel Integration (Logging Logic)
In the "Add a row into a table" step, fill in the following:

Location: Select your Team.

Document Library: Documents.

File: Select vrops_alerts_log.xlsx.

Table: Select the table name.

Advanced parameters (Mapping):

AlertId: @{variables('Body')?['alertId']}

MessageId: @{outputs('Post_card_in_a_chat_or_channel')?['body/id']}


Финальный штрих! Оформим завершение настройки: получение URL-адреса и конфигурацию на стороне VMware Aria Operations (vROps).

Вот английская версия для твоего README:

5. Obtaining the Webhook URL
Now that the Flow is configured, you need to get the unique endpoint address that vROps will use to send data.

Save your Flow by clicking the Save button in the top right corner.

Go to the very first step of the workflow: "When a HTTP request is received".

After saving, the HTTP POST URL field will be populated.

Copy this URL — you will need it for the vROps Outbound Settings in the next step.

VMware Aria Operations (vROps) Configuration
With the Power Automate side ready, we now need to tell vROps how to format and where to send the alerts.

6. Import Payload Template
To ensure the data is sent in a format Power Automate understands, import the pre-defined template:

Log in to the vROps console.

Navigate to Configure > Alerts > Payload Templates.

Click Import and upload the file: Payload Template-2026-01-31 01-13-59 AM.json.

7. Configure Outbound Settings
Navigate to Configure > Alerts > Outbound Settings.

Create a new Webhook instance.

In the URL field, paste the HTTP POST URL you copied from Power Automate.

Select the newly imported Payload Template for this instance.

Save the settings and use the Test button to ensure the connection is working.

8. Final Step: Notifications
Everything is now integrated! The final step is to define which alerts should be sent to Teams:

Go to Configure > Alerts > Notifications.

Create a new notification rule.

Select the criteria for the alerts you want to track (e.g., Critical/Immediate alerts for specific objects).

Select your newly created Webhook Outbound Instance.

Congratulations! Your vROps alerts will now be automatically pushed to your Microsoft Teams channel via Power Automate, with a history log kept in your Excel file.
