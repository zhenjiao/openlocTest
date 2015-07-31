<properties 
	pageTitle="Using Azure PowerShell with Azure Resource Manager" 
	description="Use Azure PowerShell to deploy multiple resources as a resource group to Azure." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/15/2015" 
	ms.author="tomfitz"/>

# Verwendung von himmelblau PowerShell mit himmelblau-Systemressourcen-Manager

> [AZURBLAU. SELEKTOR]
- [Azurblaue PowerShell](powershell-azure-resource-manager.md)
- [Azurblaue CLI](xplat-cli-azure-resource-manager.md)

Himmelblau-Ressourcen-Manager stellt eine völlig neue Art des Denkens über die Azure Ressourcen. Anstatt zu erstellen und Verwalten von individuellen Ressourcen, beginnen Sie durch die Vorstellung von eines komplexen Dienstes wie ein Blog, eine Fotogalerie, ein SharePoint-Portal oder ein Wiki. Sie verwenden eine Vorlage--ein Ressourcenmodell des Dienstes--eine Ressourcengruppe Ressourcen erstellen, die Sie benötigen, um den Service zu unterstützen. Dann können Sie verwalten und Bereitstellen dieser Ressourcengruppe als logische Einheit. 

In diesem Lernprogramm erfahren Sie, wie mithilfe von Azure PowerShell mit Azure-Ressourcen-Manager für Microsoft Azure. Es führt Sie durch den Prozess der Erstellung und Bereitstellung von einer Ressourcengruppe für eine Azure gehostete Web-app mit einer SQL-Datenbank, komplett mit allen Ressourcen, die Sie benötigen, zu unterstützen.

## Voraussetzungen

Um dieses Tutorial zu vervollständigen, muss man Azure PowerShell Version 0.8.0 oder höher. Um die neueste Version installieren und verknüpfen ihn mit Ihrem azurblauen Abonnement, siehe [Zum Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).

Dieses Tutorial richtet sich an PowerShell-Anfänger, aber es wird davon ausgegangen, dass Sie die grundlegenden Konzepte, wie Module, Cmdlets und Sitzungen verstehen. Weitere Informationen zu Windows PowerShell finden Sie unter [Erste Schritte mit Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Um ausführliche Hilfe für jedes Cmdlet zu erhalten, die Sie in diesem Tutorial zu sehen, verwenden Sie das Cmdlet Get-Help. 

	Get-Help <cmdlet-name> -Detailed

Geben Sie beispielsweise, um Hilfe für das Cmdlet Add-AzureAccount zu erhalten:

	Get-Help Add-AzureAccount -Detailed

## Über den azurblauen PowerShell-Module
Ab Version 0.8.0 enthält Azure PowerShell-Installation mehr als ein PowerShell-Modul. Sie müssen explizit entscheiden, ob die Befehle verwendet, die in der Azure-Modul oder das Azure-Ressourcen-Manager-Modul zur Verfügung stehen. Damit problemlos zwischen ihnen wechseln können, haben wir einen neuen Cmdlets hinzugefügt, **Switch-AzureMode**, to the Azure Profile module.

When you use Azure PowerShell, the cmdlets in the Azure module are imported by default. To switch to the Azure Resource Manager module, use the Switch-AzureMode cmdlet. It removes the Azure module from your session and imports the Azure Resource Manager and Azure Profile modules.

To switch to the AzureResoureManager module, type:

    PS C:\> Switch-AzureMode -Name AzureResourceManager

To switch back to the Azure module, type:

    PS C:\> Switch-AzureMode -Name AzureServiceManagement

By default, Switch-AzureMode affects only the current session. To make the switch effective in all PowerShell sessions, use the **Global** parameter of Switch-AzureMode.

For help with the Switch-AzureMode cmdlet, type: `Get-Help Switch-AzureMode` or see [Switch-AzureMode](http://go.microsoft.com/fwlink/?LinkID=394398).
  
To get a list of cmdlets in the AzureResourceManager module with a help synopsis, type: 

    PS C:\> Get-Command -Module AzureResourceManager | Get-Help | Format-Table Name, Synopsis

The output will look similar to the following excerpt:

	Name                                   Synopsis
	----                                   --------
	Add-AlertRule                          Adds or updates an alert rule of either metric, event, o...
	Add-AzureAccount                       Adds the Azure account to Windows PowerShell
	Add-AzureEnvironment                   Creates an Azure environment
	Add-AzureKeyVaultKey                   Creates a key in a vault or imports a key into a vault.
        ...

To get full help for a cmdlet, type a command with the format:

	Get-Help <cmdlet-name> -Full

For example, 

	Get-Help Get-AzureLocation -Full

For the full set of Azure Resource Manager commands, see [Azure Resource Manager Cmdlets](http://go.microsoft.com/fwlink/?LinkID=394765). 
  
## Create a resource group

This section of the tutorial guides you through the process of creating and deploying a resource group for a web app with a SQL database. 

You don't need to be an expert in Azure, SQL, web apps, or resource management to do this task. The templates provide a model of the resource group with all of the resources that you're likely to need. And because we're using Windows PowerShell to automate the tasks, you can use these process as a model for scripting large-scale tasks.

### Step 1: Switch to Azure Resource Manager 
1. Start PowerShell. You can use any host program that you like, such as the Azure PowerShell console or Windows PowerShell ISE.

2. Nutzung der **Switch-AzureMode** cmdlet to import the cmdlets in the AzureResourceManager and AzureProfile modules. 

        PS C:\> Switch-AzureMode AzureResourceManager

3. To add your Azure account to the Windows PowerShell session, use the **Add-AzureAccount** cmdlet. 

        PS C:\> Add-AzureAccount

The cmdlet prompts you for the login credentials for your Azure account. After logging in, it downloads your account settings so they are available to Windows PowerShell. 

The account settings expire, so you need to refresh them occasionally. To refresh the account settings, run **Add-AzureAccount** again. 

>[AZURBLAU. HINWEIS] The AzureResourceManager module requires Add-AzureAccount. A Publish Settings file is not sufficient.     

### Step 2: Select a gallery template

There are several ways to create a resource group and its resources, but the easiest way  is to use a resource group template. A *resource group template* is JSON string that defines the resources in a resource group. The string includes placeholders called "parameters" for user-defined values, like names and sizes.

Azure hosts a gallery of resource group templates and you can create your own templates, either from scratch or by editing a gallery template. In this tutorial, we'll use a gallery template. 

To see all of the templates in the Azure resource group template gallery, use the **Get-AzureResourceGroupGalleryTemplate** cmdlet; however, this command returns a large number of templates. To see a more manageable number of templates, specify a publisher parameter. 

At the Powershell prompt, type:
    
    PS C:\> Get-AzureResourceGroupGalleryTemplate -Publisher Microsoft

The cmdlet returns a list of gallery templates with Microsoft as the publisher. You use the **Identity** property to identify the template in the commands.

The Microsoft.WebSiteSQLDatabase.0.2.6-preview template looks interesting. When you run the command, the version of the template may be slightly different because a new version has been released. Use the latest version of the template. To get more information about a gallery template, use the **Identity** parameter. The value of the Identity parameter is Identity of the template.

    PS C:\> Get-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.2.6-preview

The cmdlet returns an object with much more information about the template, including a summary and description.

This template looks like it will meet our needs. Let's save it to disk and look at it more closely.

### Step 3: Examine the Template

Let's save the template to a JSON file on disk. This step is not required, but it makes it easier to view the template. To save the template, use the **Save-AzureResourceGroupGalleryTemplate** cmdlet. Use its **Identity** parameter to specify the template and the **Path** parameter to specify a path on disk.

Save-AzureResourceGroupGalleryTemplate saves the template and returns the path a file name of the JSON template file. 

	PS C:\> Save-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.2.6-preview -Path C:\Azure\Templates\New_WebSite_And_Database.json

	Path
	----
	C:\Azure\Templates\New_WebSite_And_Database.json


You can view the template file in a text editor, such as Notepad. Each template has a **parameters** section and a **resources** Abschnitt.

Die **parameters** section of the template is a collection of the parameters that are defined in all of the resources. It includes property values you can provide when setting up your resource group.

    "parameters": {
      "siteName": {
        "type": "string"
      },
      "hostingPlanName": {
        "type": "string"
      },
      "siteLocation": {
        "type": "string"
      },
      ...
    }

Some parameters have a default value. When you use the template, you are not required to supply values for these parameters. If you do not specify a value, the default value is used. 

    "collation": {
      "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
    },

When parameters have enumerated values, the valid values are listed with the parameter. For example, the **sku** parameter can take values of Free, Shared, Basic, or Standard. If you don't specify a value for the **sku** parameter, it uses the default value, Free.

    "sku": {
      "type": "string",
      "allowedValues": [
        "Free",
        "Shared",
        "Basic",
        "Standard"
      ],
      "defaultValue": "Free"
    },


Note that the **administratorLoginPassword** parameter uses a secure string, not plain text. When you provide a value for a secure string, the value is obscured. 

	"administratorLoginPassword": {
      "type": "securestring"
    },

Die **resources** section of the template lists the resources that the template creates. This template creates a SQL database server and SQL database, a server farm and website, and several management settings.
  
The definition of each resource includes its properties, such as name, type and location, and parameters for user-defined values. For example, this section of the template defines the SQL database. It includes parameters for the database name ([parameters('databaseName')]), the database server location [parameters('serverLocation')], and the collation property [parameters('collation')].

    {
        "name": "[parameters('databaseName')]",
        "type": "databases",
        "location": "[parameters('serverLocation')]",
        "apiVersion": "2.0",
        "dependsOn": [
          "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
        ],
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveId": "[parameters('requestedServiceObjectiveId')]"
        }
    },


We're almost ready to use the template, but before we do, we need to find locations for each of the resources.

### Step 4: Get resource type locations

Most templates ask you to specify a location for each of the resources in a resource group. Every resource is located in an Azure data center, but not every Azure data center supports every resource type. 

Select any location that supports the resource type. You do not have to create all of the resources in a resource group in the same location; however, whenever possible, you will want to create resources in the same location to optimize performance. In particular, you will want to make sure that your database is in the same location as the app accessing it. 

To get the locations that support each resource type, use the **Get-AzureLocation** cmdlet. To limit your output to a specific type of of resource, such as ResourceGroup, use:

    Get-AzureLocation | Where-Object Name -eq "ResourceGroup" | Format-Table Name, LocationsString -Wrap

The output will look similar to:

    Name                                 LocationsString
    ----                                 ---------------
    ResourceGroup                        East Asia, South East Asia, East US, West US, North
                                         Central US, South Central US, Central US, North Europe,
                                         West Europe

Now, we have the information that we need to create the resource group.

### Step 5: Create a resource group
 
In this step, we'll use the resource group template to create the resource group. For reference, open the New_WebSite_And_Database.json file on disk and follow along. The template file can be very helpful for determining parameter values to pass, such as the correct ApiVersion for a resource.

To create a resource group, use the **New-AzureResourceGroup** cmdlet.

The command uses the **Name** parameter to specify a name for the resource group and the **Location** parameter to specify its location. Use the output of **Get-AzureLocation** to select a location for the resource group. It uses the **GalleryTemplateIdentity** parameter to specify the gallery template.

	PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview
            ....

As soon as you type the template name, New-AzureResourceGroup fetches the template, parses it, and adds the template parameters to the command dynamically. This makes it very easy to specify the template parameter values. And, if you forget a required parameter value, Windows PowerShell prompts you for the value.

**Dynamic Template Parameters**

To get the parameters, type a minus sign (-) to indicate a parameter name and then press the TAB key. Or, type the first few letters of a parameter name, such as siteName and then press the TAB key. 

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -si<TAB>

PowerShell completes the parameter name. To cycle through the parameter names, press TAB repeatedly.

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName 

Enter a name for the website and repeat the TAB process for each of the parameters. The parameters with a default value are optional. To accept the default value, omit the parameter from the command. 

When a template parameter has enumerated values, such as the sku parameter in this template, to cycle through the parameter values, press the TAB key.

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku <TAB>

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku Basic<TAB>

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku Free<TAB>

Here is an example of a New-AzureResourceGroup command that specifies only the required template parameters and the **Verbose** common parameter. Note that the **administratorLoginPassword** is omitted.

	PS C:\> New-AzureResourceGroup -Name TestRG -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -hostingPlanName TestPlan -siteLocation "East Asia" -serverName testserver -serverLocation "East Asia" -administratorLogin Admin01 -databaseName TestDB -Verbose

When you enter the command, you are prompted for the missing mandatory parameter, **administratorLoginPassword**. And, when you type the password, the secure string value is obscured. This strategy eliminates the risk of providing a password in plain text.

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	administratorLoginPassword: **********

**New-AzureResourceGroup** returns the resource group that it created and deployed. 

In just a few steps, we created and deployed the resources required for a complex website. 
The gallery template provided almost all of the information that we needed to do this task.
And, the task is easily automated. 

## Get information about your resource groups

After creating a resource group, you can use the cmdlets in the AzureResourceManager module to manage your resource groups.

- To get all of the resource groups in your subscription, use the **Get-AzureResourceGroup** cmdlet:

		PS C:>Get-AzureResourceGroup

		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
		
		...

- To get the resources in the resource group, use the **Get-AzureResource** cmdlet and its ResourceGroupName parameter. Without parameters, Get-AzureResource gets all resources in your Azure subscription.

		PS C:\> Get-AzureResource -ResourceGroupName TestRG
		
		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Tags              :
		
		Resources         :
				Name                   Type                          Location
				----                   ------------                  --------
				ServerErrors-TestSite  microsoft.insights/alertrules         eastasia
	        	TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
	        	TestSite               microsoft.insights/components         centralus
	         	testserver             Microsoft.Sql/servers                 eastasia
	        	TestDB                 Microsoft.Sql/servers/databases       eastasia
	        	TestPlan               Microsoft.Web/serverFarms             eastasia
	        	TestSite               Microsoft.Web/sites                   eastasia
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

## Add to a resource group

- To add a resource to the resource group, use the **New-AzureResource** cmdlet. This command adds a new website to the TestRG resource group. This command is a bit more complex, because it does not use a template. 

        PS C:\>New-AzureResource -Name TestSite2 -Location "North Europe" -ResourceGroupName TestRG -ResourceType "Microsoft.Web/sites" -ApiVersion 2014-06-01 -PropertyObject @{"name" = "TestSite2"; "siteMode"= "Limited"; "computeMode" = "Shared"}

- To add a new template-based deployment to the resource group, use the **New-AzureResourceGroupDeployment** command. 

		PS C:\>New-AzureResourceGroupDeployment ` 
		-ResourceGroupName TestRG `
		-GalleryTemplateIdentity Microsoft.WebSite.0.2.6-preview `
		-siteName TestWeb2 `
		-hostingPlanName TestDeploy2 `
		-siteLocation "North Europe" 

## Move a resource

You can move existing resources to a new resource group. For examples, see [Move Resources to New Resource Group or Subscription](resource-group-move-resources.md).

## Delete a resource group

- To delete a resource from the resource group, use the **Remove-AzureResource** cmdlet. This cmdlet deletes the resource, but does not delete the resource group.

	This command removes the TestSite2 website from the TestRG resource group.

		Remove-AzureResource -Name TestSite2 -ResourceGroupName TestRG -ResourceType "Microsoft.Web/sites" -ApiVersion 2014-06-01

- To delete a resource group, use the **Remove-AzureResourceGroup** cmdlet. This cmdlet deletes the resource group and its resources.

		PS C:\ps-test> Remove-AzureResourceGroup -Name TestRG
		
		Confirm
		Are you sure you want to remove resource group 'TestRG'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## Troubleshoot a resource group
As you experiment with the cmdlets in the AzureResourceManager modules, you are likely to encounter errors. Use the tips in this section to resolve them.

### Preventing errors

The AzureResourceManager module includes cmdlets that help you to prevent errors.


- **Get-AzureLocation**: This cmdlet gets the locations that support each type of resource. Before you enter a location for a resource, use this cmdlet to verify that the location supports the resource type.


- **Test-AzureResourceGroupTemplate**: Test your template and template parameter before you use them. Enter a custom or gallery template and the template parameter values you plan to use. This cmdlet tests whether the template is internally consistent and whether your parameter value set matches the template. 



### Fixing errors

- **Get-AzureResourceGroupLog**: This cmdlet gets the entries in the log for each  deployment of the resource group. If something goes wrong, begin by examining the deployment logs. 

- **Verbose and Debug**:  The cmdlets in the AzureResourceManager module call REST APIs that do the actual work. To see the messages that the APIs return, set the $DebugPreference variable to "Continue" and use the Verbose common parameter in your commands. The messages often provide vital clues about the cause of any failures.

- **Your Azure credentials have not been set up or have expired**:  To refresh the credentials in your Windows PowerShell session, use the Add-AzureAccount cmdlet. The credentials in a publish settings file are not sufficient for the cmdlets in the AzureResourceManager module.


## Die nächsten Schritte
Getting Started

- [Azure Resource Manager Overview](./resource-group-overview.md)
- [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management](./xplat-cli-azure-resource-manager.md)
- [Using the Azure Portal to manage your Azure resources](./resource-group-portal.md)

Creating and Deploying Applications

- [Authoring Azure Resource Manager Templates](./resource-group-authoring-templates.md)
- [Deploy an application with Azure Resource Manager Template](./resource-group-template-deploy.md)
- [Deploy a complex application predictably in Azure](app-service-web/app-service-deploy-complex-application-predictably.md)
- [Troubleshooting Resource Group Deployments in Azure](./resource-group-deploy-debug.md)
- [Azure Resource Manager Template Functions](./resource-group-template-functions.md)
- [Advanced Template Operations](./resource-group-advanced-template.md)

Organizing Resources

- [Using tags to organize your Azure resources](./resource-group-using-tags.md)

Managing and Auditing Access

- [Managing and Auditing Access to Resources](./resource-group-rbac.md)
- [Authenticating a Service Principal with Azure Resource Manager](./resource-group-authenticate-service-principal.md)
- [Create a new Azure Service Principal using the Azure classic portal](./resource-group-create-service-principal-portal.md)

