<properties
	pageTitle="Download the Azure SDK for PHP"
	description="Learn how to download and install the Azure SDK for PHP."
	documentationCenter="php"
	services=""
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="03/20/2015"
	ms.author="tomfitz"/>

#Download 为 PHP Azure SDK

## 概述

对于 PHP Azure SDK 包括允许您开发、 部署和管理 Azure 的 PHP 应用程序的组件。具体而言，对于 PHP Azure SDK 包括以下内容:

* **Azure 的 PHP 客户端库**.这些类库提供了访问蔚蓝的功能，如数据管理服务提供一个接口和云服务。  
* **用于 Mac、 Linux 和 Windows (蔚蓝 CLI) 蔚蓝的命令行界面**.这是一组命令用于部署和管理 Azure 服务，Azure 网站和 Azure 虚拟机等。在任何平台上，包括 Mac、 Linux 和 Windows Azure CLI 工作。
* **天青 PowerShell (仅限 Windows)**.这是一整套的 PowerShell cmdlet 用于部署和管理 Azure 服务，云服务和虚拟机等。
* **蔚蓝的仿真器 (仅限 Windows)**.计算和存储仿真器是本地仿真器的云服务和允许您测试应用程序本地的数据管理服务。Azure 仿真程序只在 Windows 上运行。

以下各节描述如何下载和安装上面描述的组件。

本主题中的说明假定您有 [PHP][install-php] 安装。

> [AZURE。注意]
> 你必须有 PHP 5.3 或更高的 Azure 使用 PHP 客户端库。

Azure 的 #PHP 客户端库

The PHP Client Libraries for Azure provide an interface for accessing Azure features, such as data management services and cloud services, from any operating system. These libraries can be installed via the Composer or PEAR package managers or manually.

For information about how to use the PHP Client Libraries for Azure, see [How to Use the Blob Service][blob-service], [How to Use the Table Service][table-service] 和 [How to Use the Queue Service][queue-service].

###Install via Composer

1. [Install Git][install-git].


	> [AZURE.NOTE]
	> On Windows, you will also need to add the Git executable to your PATH environment variable.

2. Create a file named **composer.json** in the root of your project and add the following code to it:

        {
            "repositories": [
                {
                    "type": "pear",
                    "url": "http://pear.php.net"
                }
            ],
            "require": {
                "pear-pear.php.net/mail_mime" : "*",
                "pear-pear.php.net/http_request2" : "*",
                "pear-pear.php.net/mail_mimedecode" : "*",
                "microsoft/windowsazure": "*"
            }
        }

3. Download **[composer.phar][composer-phar]** in your project root.

4. Open a command prompt and execute this in your project root

		php composer.phar install

###Install as a PEAR package

To install the PHP Client Libraries for Azure as a PEAR package, follow these steps:

1. [Install PEAR][install-pear].
2. Set-up the Azure PEAR channel:

		pear channel-discover pear.windowsazure.com
3. Install the PEAR package:

		pear install pear.windowsazure.com/WindowsAzure-0.4.0

After the installation completes, you can reference class libraries from your application.

###Install manually

To download and install the PHP Client Libraries for Azure manually, follow these steps:

1. Download a .zip archive that contains the libraries from [GitHub][php-sdk-github]. Alternatively, fork the repository and clone it to your local machine. (The latter option requires a GitHub account and having Git installed locally.)

	> [AZURE.NOTE]
	> The PHP Client Libraries for Azure have a dependency on the [HTTP_Request2](http://pear.php.net/package/HTTP_Request2), [Mail_mime](http://pear.php.net/package/Mail_mime)和 [Mail_mimeDecode](http://pear.php.net/package/Mail_mimeDecode) PEAR packages. The recommended way to resolve these dependencies is to install these packages using the [PEAR package manager](http://pear.php.net/manual/en/installation.php)

2. Copy the `WindowsAzure` directory of the downloaded archive to your application directory structure and reference classes from your application.

##Azure PowerShell and Azure Emulators

Azure PowerShell is a set of PowerShell cmdlets for deploying and managing Azure Services (such as Cloud Services and Virtual Machines). The Azure Emulators are emulators of cloud services and data management services that allow you to test an application locally. These components are supported Windows only.

The recommended way to install Azure PowerShell and the Azure Emulators is to use the [Microsoft Web Platform Installer][download-wpi]. Note that you can also choose to install other development components, such as PHP, SQL Server, the Microsoft Drivers for SQL Server for PHP, and WebMatrix.

For information about how to use Azure PowerShell, see [How to Use Azure PowerShell][powershell-tools].

##Azure CLI

The Azure CLI is a set of commands for deploying and managing Azure services, such as Azure Websites and Azure Virtual Machines. The following list describes how to install the Azure CLI, depending on your operating system:

* **Mac**: Download the Azure SDK Installer here: [http://go.microsoft.com/fwlink/?LinkId=252249][mac-installer]. Open the downloaded .pkg file and complete the installation steps as your are prompted.

* **Linux**: Install the latest version of [Node.js][nodejs-org] (see [Install Node.js via Package Manager][install-node-linux]), then run the following command:

		npm install azure-cli -g

	> [AZURE.NOTE]
	> You may need to run this command with elevated privileges:  `sudo npm install azure-cli -g`


For information about how to use the Azure CLI, see [How to Use the Azure Command-Line Interface for Mac, Linux, and Windows][azure cli].

[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[pear-net]: http://pear.php.net/
[http-request2-package]: http://pear.php.net/package/HTTP_Request2
[mail-mimedecode-package]: http://pear.php.net/package/Mail_mimeDecode
[mail-mime-package]: http://pear.php.net/package/Mail_mime
[install-pear]: http://pear.php.net/manual/en/installation.getting.php
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
