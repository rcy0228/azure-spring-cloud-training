# Exercise 3 - Configure application logs

Now that we have an application deployed, we'll configure Log Analytics, so that we can quickly search the application's logs if something goes wrong. We'll see how to take advantage of Log Analytics in a later section, but because it takes time for log entries to start coming in, we'll do the configuration steps here before moving on with the training.

---

## Task 1 : Configure log aggregation

There are actually three ways to access your application's logs: [Azure Storage](https://docs.microsoft.com/en-us/azure/storage/common/storage-introduction/?WT.mc_id=azurespringcloud-github-judubois), [Azure Events Hub](https://docs.microsoft.com/en-us/azure/event-hubs/?WT.mc_id=azurespringcloud-github-judubois), and [Log Analytics](https://docs.microsoft.com/en-us/azure/azure-monitor/log-query/get-started-portal/?WT.mc_id=azurespringcloud-github-judubois). We will focus here on Log Analytics as it's the most common one, and as it's integrated into Azure Spring Cloud.

[Log Analytics](https://docs.microsoft.com/en-us/azure/azure-monitor/log-query/get-started-portal/?WT.mc_id=azurespringcloud-github-judubois) is part of [Azure Monitor](https://azure.microsoft.com/en-us/services/monitor/?WT.mc_id=azurespringcloud-github-judubois), which is well-integrated into Azure Spring Cloud and which we will also use for metrics monitoring.

>💡 __Note:__ You should have a Log Analytics workspace pre-created named **sclab-la-<inject key="DeploymentID" enableCopy="false"/>** in your resource group for this workshop.

1. Navigate to the Azure Spring Cloud instance named **azure-spring-cloud-lab-<inject key="DeploymentID" enableCopy="false"/>** in Azure Portal and select "Diagnostic settings" in the "Monitoring" section of the navigation pane:

   ![Diagnostic Settings](media/01-diagnostic-settings.png)

2. Click on **+ Add diagnostic setting** and configure your instance to send all its logs to the Log analytics workspace that we just created.

    ![Add Diagnostic settings](media/add-diag.png)
   
3. Under **Diagnostic Settings** page, follow the below mentioned steps:


  - Name : **write-to-log-analytics (1)**
  - Under **Logs**, check the boxes next to **ApplicationConsole** and **SystemLogs** **(2)**
  - Under **Metrics**, check the box next to **AllMetrics** **(3)**
  - Under **Destination details**, check the box next to **Send to Log Analytics workspace** **(4)**
  - Click **Save** **(5)**.
 
    ![Send logs to the log analytics workspace](media/diagsett.png)

---
