---
title: 安装弹性数据库作业 | Microsoft 文档
description: 演练如何安装弹性作业功能。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: cc322f44760ddf0a7cd28751c895a7c4938dbbc0
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52867233"
---
# <a name="installing-elastic-database-jobs-overview"></a>安装弹性数据库作业概述

[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]



可以通过 PowerShell 或 Azure 门户安装[**弹性数据库作业**](sql-database-elastic-jobs-overview.md)。只有安装了 PowerShell 程序包，才能获取使用 PowerShell API 创建和管理作业的权限。 此外，PowerShell API 目前提供的功能明显多于门户。

如果从现有的“弹性池”通过门户安装了“弹性数据库作业”，最新的 Powershell 预览包含用于升级现有安装的脚本。 强烈建议将安装升级到最新的**弹性数据库作业**组件，以便利用通过 PowerShell API 公开的新功能。

## <a name="prerequisites"></a>先决条件
* Azure 订阅。 如需免费试用，请参阅[免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* Azure PowerShell。 使用 [Web 平台安装程序](https://go.microsoft.com/fwlink/p/?linkid=320376)安装最新版本。 有关详细信息，请参阅 [如何安装和配置 Azure PowerShell](/powershell/azure/overview)。
* [NuGet 命令行实用程序](https://nuget.org/nuget.exe)用于安装弹性数据库作业包。 有关详细信息，请参阅 http://docs.nuget.org/docs/start-here/installing-nuget。

## <a name="download-and-import-the-elastic-database-jobs-powershell-package"></a>下载并导入弹性数据库作业 PowerShell 程序包
1. 启动 Microsoft Azure PowerShell 命令窗口，并导航到 NuGet 命令行实用程序 (nuget.exe) 所下载到的目录。
2. 使用以下命令，将**弹性数据库作业**包下载并导入到当前目录：
   
        PS C:\>.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease
   
    **弹性数据库作业**文件放在本地目录中名为 **Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x**（其中的 *x.x.xxxx.x* 表示版本号）的文件夹内。 PowerShell cmdlet（包括所需的客户端 .dll）位于 **tools\ElasticDatabaseJobs** 子目录中，用于安装、升级和卸载的 PowerShell 脚本也位于 **tools** 子目录中。
3. 键入 cd tools，导航到 Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x 文件夹下的 tools 子目录，例如：
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

4. 执行 .\InstallElasticDatabaseJobsCmdlets.ps1 脚本，将 ElasticDatabaseJobs 目录复制到 $home\Documents\WindowsPowerShell\Modules 中。 这也会自动导入要使用的模块，例如：
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobsCmdlets.ps1

## <a name="install-the-elastic-database-jobs-components-using-powershell"></a>使用 PowerShell 安装弹性数据库作业组件
1. 启动 Microsoft Azure PowerShell 命令窗口，并导航到 Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x 文件夹下的 \tools 子目录：键入 cd \tools
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2. 执行 .\InstallElasticDatabaseJobs.ps1 PowerShell 脚本，并提供其所请求变量的值。 此脚本会根据[弹性数据库作业组件和定价](sql-database-elastic-jobs-overview.md#components-and-pricing)中所述创建组件，并将 Azure 云服务配置为适当使用依赖组件。

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobs.ps1

运行此命令时，会打开一个窗口，要求提供“**用户名**”和“**密码**”。 这不是 Azure 凭据，请输入用户名和密码，将其作为要为新服务器创建的管理员凭据。

可以根据所需的设置，修改此示例调用中提供的参数。 下面提供了有关每个参数行为的详细信息：

<table style="width:100%">
  <tr>
    <th>参数</th>
    <th>说明</th>
  </tr>

<tr>
    <td>ResourceGroupName</td>
    <td>提供为了包含新建的 Azure 组件而创建的 Azure 资源组名称。 此参数默认为“__ElasticDatabaseJob”。 不建议更改此值。</td>
    </tr>

</tr>

    <tr>
    <td>ResourceGroupLocation</td>
    <td>提供用于保存新建的 Azure 组件的 Azure 位置。 此参数默认为“美国中部”位置。</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>提供要安装的服务辅助角色数目。 此参数默认为 1。 可以使用更多的辅助角色来扩大服务，并提供高可用性。 建议针对需要服务高可用性的部署使用“2”。</td>
    </tr>

</tr>
    <tr>
    <td>ServiceVmSize</td>
    <td>提供在云服务中使用的 VM 大小。 此参数默认为 A0。 接受参数值 A0/A1/A2/A3，这会导致辅助角色分别使用 ExtraSmall/Small/Medium/Large 大小。 有关辅助角色大小的详细信息，请参阅[弹性数据库作业组件和定价](sql-database-elastic-jobs-overview.md#components-and-pricing)。</td>
</tr>

</tr>
    <tr>
    <td>SqlServerDatabaseSlo</td>
    <td>为标准版提供计算大小。 此参数默认为 S0。 接受参数值 S0/S1/S2/S3/S4/S6/S9/S12，这会导致 Azure SQL 数据库使用各自的计算大小。 有关 SQL 数据库计算大小的详细信息，请参阅[弹性数据库作业组件和定价](sql-database-elastic-jobs-overview.md#components-and-pricing)。</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorUserName</td>
    <td>提供新建的 Azure SQL 数据库服务器的管理员用户名。 如果未指定，系统将打开 PowerShell 凭据窗口提示输入凭据。</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorPassword</td>
    <td>提供新建的 Azure SQL 数据库服务器的管理员密码。 如果未提供，系统将打开 PowerShell 凭据窗口提示输入凭据。</td>
</tr>
</table>

对于要针对大量数据库同时运行大量作业的系统，建议指定如下参数：-ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2。

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\InstallElasticDatabaseJobs.ps1 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2

## <a name="update-an-existing-elastic-database-jobs-components-installation-using-powershell"></a>使用 PowerShell 更新现有的弹性数据库作业组件安装
可以在现有的安装中更新**弹性数据库作业**，以实现缩放和高可用性。 此程序允许升级将来的服务代码，而无需删除并重新创建控制数据库。 也可以在同一版本中使用此过程来修改服务 VM 的大小或服务器的辅助角色计数。

要更新安装的 VM 大小，请运行以下脚本，并将参数更新选择的值。

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\UpdateElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\UpdateElasticDatabaseJobs.ps1 -ServiceVmSize A1 -ServiceWorkerCount 2

<table style="width:100%">
  <tr>
  <th>参数</th>
  <th>说明</th>
</tr>

  <tr>
    <td>ResourceGroupName</td>
    <td>标识最初安装弹性数据库作业组件时使用的 Azure 资源组名称。 此参数默认为“__ElasticDatabaseJob”。 因为不建议更改此值，因此，应该不必指定此参数。</td>
    </tr>
</tr>

</tr>

  <tr>
    <td>ServiceWorkerCount</td>
    <td>提供要安装的服务辅助角色数目。  此参数默认为 1。  可以使用更多的辅助角色来扩大服务，并提供高可用性。  建议针对需要服务高可用性的部署使用“2”。</td>
</tr>

</tr>

    <tr>
    <td>ServiceVmSize</td>
    <td>提供在云服务中使用的 VM 大小。 此参数默认为 A0。 接受参数值 A0/A1/A2/A3，这会导致辅助角色分别使用 ExtraSmall/Small/Medium/Large 大小。 有关辅助角色大小的详细信息，请参阅[弹性数据库作业组件和定价](sql-database-elastic-jobs-overview.md#components-and-pricing)。</td>
</tr>

</table>

## <a name="install-the-elastic-database-jobs-components-using-the-portal"></a>使用门户安装弹性数据库作业组件
[创建弹性池](sql-database-elastic-pool-manage-portal.md)后，可以安装**弹性数据库作业**组件，以便对弹性池中的每个数据库执行管理任务。 与使用**弹性数据库作业** PowerShell API 不同，门户界面目前限制为只能针对现有的池执行。

**估计完成时间：** 10 分钟。

1. 在 [Azure 门户](https://portal.azure.com/#)上的弹性池的仪表板视图中，单击“创建作业”。
2. 如果是首次创建作业，必须通过单击“**预览版条款**”安装“**弹性数据库作业**”。
3. 单击相应的复选框接受条款。
4. 在“安装服务”视图中，单击“**作业凭据**”。
   
    ![安装服务][1]
5. 键入数据库管理员的用户名和密码。在安装过程中，将新建 Azure SQL 数据库服务器。 在此新服务器中，创建了一个称为控制数据库的新数据库，用于包含弹性数据库作业的元数据。 此处创建的用户名和密码用于登录控制数据库。 单独的凭据用于对池中的数据库执行脚本。
   
    ![创建用户名和密码][2]
6. 单击“确定”按钮。 几分钟后，会在新的[资源组](../azure-resource-manager/resource-group-overview.md)中创建组件。 新资源组已固定到开始面板，如下所示。 创建后，弹性数据库作业（云服务、SQL 数据库、服务总线和存储空间）都在该组中创建。
   
    ![开始面板中的资源组][3]
7. 如果在安装弹性数据库作业时尝试创建或管理某个作业，则在提供“**凭据**”时，将看到以下消息。
   
    ![部署仍在进行][4]

如果需要卸载，请删除资源组。 请参阅[如何卸载弹性数据库作业组件](sql-database-elastic-jobs-uninstall.md)。

## <a name="next-steps"></a>后续步骤
确保已在组中的每个数据库上创建对脚本执行具有适当权限的凭据。有关详细信息，请参阅[保护 SQL 数据库](sql-database-manage-logins.md)。
请参阅[创建和管理弹性数据库作业](sql-database-elastic-jobs-create-and-manage.md)以开始操作。

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/not-done.png
