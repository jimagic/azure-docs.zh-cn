---
title: 排查 Azure 虚拟机备份错误
description: Azure 虚拟机备份和还原疑难解答
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 8/7/2018
ms.author: trinadhk
ms.openlocfilehash: 9bbaf23999c04eba5157ebe7dff73ed47418c99a
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2018
ms.locfileid: "53634178"
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Azure 虚拟机备份疑难解答
可使用下表中所列出的信息排查使用 Azure 备份时遇到的错误：

| 错误详细信息 | 解决方法 |
| ------ | --- |
| 由于虚拟机 (VM) 不再存在，备份无法执行操作： <br>停止保护虚拟机，无需删除备份数据。 有关更多信息，请参阅[停止保护虚拟机](http://go.microsoft.com/fwlink/?LinkId=808124)。 |删除主 VM 时会发生此错误，但备份策略仍会查找要备份的 VM。 要修复此错误，请执行以下步骤： <ol><li> 重新创建具有相同名称和相同资源组名称的虚拟机，“云服务名称”<br>**or**</li><li> 通过删除或不删除备份数据来停止保护虚拟机。 有关更多信息，请参阅[停止保护虚拟机](https://go.microsoft.com/fwlink/?LinkId=808124)。</li></ol> |
| 由于虚拟机未建立网络连接，快照操作失败： <br>请确保 VM 具有网络访问权限。 要成功进行快照操作，请将 Azure 数据中心 IP 范围列入白名单，或设置代理服务器以进行网络访问。 有关详细信息，请参阅[对 Azure 备份失败进行故障排除：代理或扩展的问题](http://go.microsoft.com/fwlink/?LinkId=800034)。 <br><br>如果已经在使用代理服务器，请确保正确配置代理服务器设置。 | 拒绝虚拟机上的出站 Internet 连接时，会发生此错误。 VM 快照扩展需要 Internet 连接才可拍摄基础磁盘的快照。 请参阅 [ExtensionSnapshotFailedNoNetwork](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine)。 |
| Azure 虚拟机代理（VM 代理）无法与 Azure 备份服务通信： <br>请确保 VM 具有网络连接，并且 VM 代理为最新版且正常运行。 有关详细信息，请参阅[对 Azure 备份失败进行故障排除：代理或扩展的问题](http://go.microsoft.com/fwlink/?LinkId=800034)。 |如果 VM 代理出现问题，或以某种方式阻止了对 Azure 基础结构的网络访问，则会发生此错误。 了解有关[调试 VM 快照问题](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup)的详细信息。 <br><br>如果 VM 代理未导致任何问题，请重启 VM。 VM 状态错误可能导致出现问题，重启 VM 会重置状态。 |
| VM 处于失败的预配状态： <br>请重启 VM，并确保 VM 正在运行或已关闭。 | 当其中某个扩展失败将 VM 状态置于失败的预配状态时，会发生此错误。 请转到扩展列表，查看是否有失败的扩展，将其删除并尝试重启虚拟机。 如果所有扩展都处于运行状态，请检查 VM 代理服务是否正在运行。 如果未运行，请重启 VM 代理服务。 |
| 托管磁盘的“VMSnapshot”扩展操作失败： <br>请重试备份操作。 如果问题仍然存在，请按照[对 Azure 备份失败进行故障排除](http://go.microsoft.com/fwlink/?LinkId=800034)中的说明进行操作。 如果问题持续出现，请联系 Microsoft 支持。 | 备份服务未能触发快照时，会发生此错误。 了解有关[调试 VM 快照问题](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#ExtentionOperationFailed-vmsnapshot-extension-operation-failed)的详细信息。 |
| 由于存储帐户中的可用空间不足，备份无法复制虚拟机的快照： <br>请确保存储帐户的可用空间等于连接到虚拟机的高级存储磁盘上的数据。 | 对于 VM 备份堆栈 V1 上的高级 VM，我们将快照复制到存储帐户。 此步骤可确保在快照上运行的备份管理流量不会限制使用高级磁盘的应用程序的可用 IOPS 数。 <br><br>我们建议只分配总存储帐户空间的 50%（即 17.5 TB）。 这样，Azure 备份服务可以将快照复制到存储帐户，并将数据从存储帐户中的复制位置传输到保管库。 |
| 由于 VM 代理没有响应，因此备份无法执行操作。 |如果 VM 代理出现问题，或以某种方式阻止了对 Azure 基础结构的网络访问，则会发生此错误。 对于 Windows VM，请检查服务中的 VM 代理服务状态，以及代理是否显示在控制面板的程序中。 <br><br>请尝试从控制面板中删除程序，然后按照 [VM 代理](#vm-agent)中的说明重新安装代理。 重新安装代理后，将触发临时备份以进行验证。 |
| 恢复服务扩展操作失败： <br>请确保虚拟机上有最新的 VM 代理，并且 VM 代理服务正在运行。 请重试备份操作。 如果备份操作失败，请联系 Microsoft 支持部门。 |VM 代理过期时会发生此错误。 请参阅[对 Azure 虚拟机备份进行故障排除](#updating-the-VM-agent)以更新 VM 代理。 |
| 虚拟机不存在： <br>请确保该虚拟机存在，或选择其他虚拟机。 |删除主 VM 时会发生此错误，但备份策略仍会查找要备份的 VM。 要修复此错误，请执行以下步骤： <ol><li> 重新创建具有相同名称和相同资源组名称的虚拟机，“云服务名称”<br>**or**<br></li><li>停止保护虚拟机，无需删除备份数据。 有关更多信息，请参阅[停止保护虚拟机](https://go.microsoft.com/fwlink/?LinkId=808124)。</li></ol> |
| 命令运行失败： <br>此项上当前正在进行另一项操作。 等待前一项操作完成。 然后重试该操作。 |现有备份作业正在运行，当前作业结束前无法开始新的作业。 |
| 从恢复服务保管库复制 VHD 时超时： <br>请在几分钟后重试该操作。 如果问题持续出现，请联系 Microsoft 支持。 | 如果存储端存在暂时性错误，或者备份服务未在超时期限内接收足够的存储帐户 IOPS 以将数据传输到保管库，则会发生此错误。 请确保按照 [VM 配置最佳做法](backup-azure-vms-introduction.md#best-practices)进行操作。 将 VM 移到未加载的其他存储帐户，然后重试备份作业。|
| 备份失败并出现内部错误： <br>请在几分钟后重试该操作。 如果问题持续出现，请联系 Microsoft 支持。 |出现此错误的原因有两个： <ul><li> 在访问 VM 存储时存在暂时性问题。 请查看 [Azure 状态网站](https://azure.microsoft.com/status/)，检查区域中是否存在计算、存储或网路问题。 问题解决后，请重试备份作业。 <li> 已删除原始 VM，恢复点无法采用。 若要保留已删除 VM 的备份数据，但要删除备份错误，请取消保护 VM 并选择保留数据选项。 此操作可停止计划备份作业和阻止反复出现的错误消息。 |
| 备份无法在所选项上安装 Azure 恢复服务扩展： <br>VM 代理是 Azure 恢复服务扩展的先决条件。 安装 Azure 虚拟机代理并重启注册操作。 |<ol> <li>检查 VM 代理是否安装正确。 <li>确保已正确设置 VM 配置中的标志。</ol> 阅读有关[安装 VM 代理](#validating-vm-agent-installation)以及如何验证 VM 代理安装的详细信息。 |
| 扩展安装失败，出现错误“COM+ 无法与 Microsoft 分布式事务处理协调器通信”。 |此错误通常表示 COM+ 服务未运行。 请与 Microsoft 支持部门联系，以获取解决此问题所需的帮助。 |
| 快照操作失败，出现卷影复制服务 (VSS) 操作错误“此驱动器已通过 BitLocker 驱动器加密锁定。必须通过控制面板解锁此驱动器”。 |关闭 VM 上的所有驱动器的 BitLocker，并检查 VSS 问题是否得到解决。 |
| VM 未处于允许备份的状态。 |<ul><li>如果 VM 处于“运行”和“关闭”之间的瞬时状态，请等待状态更改。 然后触发备份作业。 <li> 如果 VM 是 Linux VM 并使用安全性增强的 Linux 内核模块，则需要从安全策略排除 Azure Linux 代理路径 (/var/lib/waagent)，确保已安装备份扩展。  |
| 找不到 Azure 虚拟机。 |删除主 VM 时会发生此错误，但备份策略仍会查找已删除的 VM。 请修复此错误，如下所示： <ol><li>重新创建具有相同名称和相同资源组名称的虚拟机，“云服务名称” <br>**or** <li> 禁用对此 VM 的保护，从而不创建备份作业。 </ol> |
| 虚拟机上不存在 VM 代理： <br>安装任何必备组件和 VM 代理。 然后，重启该操作。 |阅读有关 [VM 代理安装以及如何验证 VM 代理安装](#vm-agent)的详细信息。 |
| 快照操作失败，因为 VSS 编写器处于错误状态。 |请重启处于错误状态的 VSS 编写器。 在提升的命令提示符处，运行 ```vssadmin list writers```。 输出包含所有 VSS 编写器及其状态。 对于每个状态不为“[1] 稳定”的 VSS 编写器，要重启 VSS 编写器，请在提升权限的命令提示符处运行以下命令： <ol><li>```net stop serviceName``` <li> ```net start serviceName```</ol>|
| 由于配置分析失败，因此快照操作失败。 |发生此错误的原因是 MachineKeys 目录 %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys 上的权限已更改。 <br> 请运行以下命令，并验证“MachineKeys”目录的权限是否为默认值：<br>icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys。 <br><br>默认权限如下： <ul><li>Everyone:(R,W) <li>BUILTIN\Administrators：(F)</ul> 如果在“MachineKeys”目录中看到的权限与默认值不同，请执行以下步骤以更正权限、删除证书以及触发备份： <ol><li>修复“MachineKeys”目录上的权限。 通过在目录中使用 Explorer 安全属性和高级安全设置，将权限重新设为默认值。 从目录中删除所有用户对象（默认值除外），确保 Everyone 权限具有以下特殊访问权限： <ul><li>列出文件夹/读取数据 <li>读取属性 <li>读取扩展的属性 <li>创建文件/写入数据 <li>创建文件夹/附加数据<li>写入属性<li>写入扩展的属性<li>读取权限 </ul><li>删除其中发布对象为经典部署模式或“Windows Azure CRP 证书生成器”的所有证书：<ol><li>[在本地计算机控制台上打开证书](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx)。<li>在“个人” > “证书”下，删除其中发布对象为经典部署模式或 Windows Azure CRP 证书生成器的所有证书。</ol> <li>触发 VM 备份作业。 </ol>|
| Azure 备份服务对 Azure Key Vault 没有足够的权限来备份加密的虚拟机。 |通过使用[从还原的磁盘创建 VM](backup-azure-vms-automation.md) 中的步骤，在 PowerShell 中为备份服务提供这些权限。 |
|快照扩展安装失败，出现错误“COM+ 无法与 Microsoft 分布式事务处理协调器通信”。 | 通过提升的命令提示符，启动 Windows 服务“COM+ 系统应用程序”。 例如，net start COMSysApp。 如果该服务无法启动，请执行以下步骤：<ol><li> 确保服务“分布式事务处理协调器”的登录帐户为“网络服务”。 如果不是，请将登录帐户更改为“网络服务”并重启该服务。 然后尝试启动“COM+ 系统应用程序”。<li>如果“COM+ 系统应用程序”无法启动，请按照以下步骤卸载/安装服务“分布式事务处理协调器”： <ol><li>停止 MSDTC 服务。 <li>打开命令提示符“(cmd)”。 <li>运行命令 ```msdtc -uninstall```。 <li>运行命令 ```msdtc -install```。 <li>启动 MSDTC 服务。 </ol> <li>启动 Windows 服务“COM+ 系统应用程序”。 “COM+ 系统应用程序”启动后，从 Azure 门户触发备份作业。</ol> |
|  由于 COM+ 错误，快照操作失败。 | 我们建议通过提升的命令提示符“net start COMSysApp”重启 Windows 服务“COM+ 系统应用程序”。 如果此问题一再出现，请重启 VM。 如果重启 VM 不起作用，请尝试[删除 VMSnapshot 扩展](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#cause-3-the-backup-extension-fails-to-update-or-load)并手动触发备份。 |
| 备份无法冻结一个或多个 VM 装入点以获取文件系统一致性快照。 | 执行以下步骤： <ul><li>通过使用“tune2fs”命令来检查所有装入设备的文件系统状态。 例如，*tune2fs -l /dev/sdb1\*。| grep 文件系统状态。 <li>使用“umount”命令卸载未清除文件系统状态的设备。 <li> 使用“fsck”命令在这些设备上运行文件系统一致性检查。 <li> 再次装入设备，并尝试备份。</ol> |
| 由于无法创建安全的网络通信通道，因此快照操作失败。 | <ol><li> 通过在权限提升模式下运行“regedit.exe”来打开注册表编辑器。 <li> 标识系统中存在的所有 .NET Framework 版本。 它们位于注册表项“HKEY_LOCAL_MACHINE \ SOFTWARE \ Microsoft”的层次结构下。 <li> 请为注册表项中存在的每个 .Net Framework 添加以下键： <br> “SchUseStrongCrypto"=dword:00000001”。 </ol>|
| 由于 Visual C++ Redistributable for Visual Studio 2012 安装失败，因此快照操作失败。 | 导航到 C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion and install vcredist2012_x64。 请确保允许此服务安装的注册表项值设置为正确的值。 也就是说，注册表项“HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver”的值设置为“3”，而不是“4”。 <br><br>如果仍然遇到安装问题，请通过权限提升的命令提示符运行“MSIEXEC /UNREGISTER”，接着运行“MSIEXEC /REGISTER”来重启安装服务。  |


## <a name="jobs"></a>作业
| 错误详细信息 | 解决方法 |
| --- | --- |
| 此作业类型不支持取消： <br>请等待作业完成。 |无 |
| 该作业未处于可取消状态： <br>请等待作业完成。 <br>**or**<br> 所选作业未处于可取消状态： <br>请等待作业完成。 |这项作业很可能快完成了。 等待作业完成。|
| 备份不能取消该作业，因为它没有正在进行： <br>仅支持取消正在进行的作业。 尝试取消正在进行的作业。 |由于临时状态而发生此错误。 请稍等片刻，并重试取消操作。 |
| 备份未能取消作业： <br>请等待作业完成。 |无 |

## <a name="restore"></a>还原
| 错误详细信息 | 解决方法 |
| --- | --- |
| 还原失败，发生云内部错误。 |<ol><li>尝试还原的云服务使用 DNS 设置进行配置。 可以检查： <br>“$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings”。<br>如果配置了“地址”，则配置了 DNS 设置。<br> <li>尝试还原的云服务配置了“ReservedIP”，且云服务中的现有 VM 处于停止状态。 可以使用以下 PowerShell cmdlet 检查云服务是否已保留 IP：$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName。 <br><li>正在尝试将具有以下特殊网络配置的虚拟机还原到同一个云服务中： <ul><li>采用负载均衡器配置的虚拟机（内部和外部）。<li>具有多个保留 IP 的虚拟机。 <li>具有多个 NIC 的虚拟机。 </ul><li>请在 UI 中选择新的云服务，或参阅[还原注意事项](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)，了解具有特殊网络配置的 VM。</ol> |
| 已存在所选的 DNS 名称： <br>请指定其他 DNS 名称，然后重试。 |此 DNS 名称是指云服务名称，通常以 “cloudapp.net”结尾。 此名称必须是唯一名称。 如果出现此错误，则需在还原期间选择其他 VM 名称。 <br><br> 此错误仅向 Azure 门户用户显示。 通过 PowerShell 进行的还原操作成功，因为它仅还原磁盘而不创建 VM。 如果在磁盘还原操作之后显式创建 VM，则会遇到该错误。 |
| 指定的虚拟网络配置不正确： <br>指定其他虚拟网络配置，然后重试。 |无 |
| 指定的云服务使用与要还原的虚拟机的配置不匹配的保留 IP： <br>指定不使用保留的 IP 的其他云服务。 或者选择要还原的其他恢复点。 |无 |
| 云服务已达到其输入终结点数量的限制： <br>通过指定其他云服务或使用现有终结点重试该操作。 |无 |
| 恢复服务保管库和目标存储帐户位于两个不同的区域： <br>确保还原操作中指定的存储帐户与恢复服务保管库位于同一 Azure 区域中。 |无 |
| 为还原操作指定的存储帐户不受支持： <br>仅支持具有本地冗余或异地冗余复制设置的基本或标准存储帐户。 选择受支持的存储帐户。 |无 |
| 为还原操作指定的存储帐户的类型不是联机状态： <br>确保还原操作中指定的存储帐户处于联机状态。 |如果 Azure 存储中出现暂时性错误或中断，可能会发生此错误。 请选择另一个存储帐户。 |
| 资源组配额已达限制： <br>请从 Azure 门户中删除某些资源组，或者与 Azure 支持部门联系，以提高限额。 |无 |
| 所选子网不存在： <br>选择存在的子网。 |无 |
| 备份服务无权访问订阅中的资源。 |要修复此错误，请首先使用[还原备份磁盘](backup-azure-arm-restore-vms.md#create-new-restore-disks)中的步骤来还原磁盘。 然后使用[从已还原的磁盘创建 VM](backup-azure-vms-automation.md#restore-an-azure-vm) 中的 PowerShell 步骤。 |

## <a name="backup-or-restore-takes-time"></a>备份或还原需要一定时间
如果备份时间超过 12 小时，或还原时间超过 6 小时：
* 请了解[影响备份时间的因素](backup-azure-vms-introduction.md#time-considerations)和[影响还原时间的因素](backup-azure-vms-introduction.md#restore-considerations)。
* 请务必遵循[备份最佳做法](backup-azure-vms-introduction.md#best-practices)。

## <a name="vm-agent"></a>VM 代理
### <a name="set-up-the-vm-agent"></a>设置 VM 代理
通常，VM 代理已存在于从 Azure 库创建的 VM 中。 但是，从本地数据中心迁移的虚拟机上将不会安装 VM 代理。 对于这些 VM，必须显式安装 VM 代理。

#### <a name="windows-vms"></a>Windows VM

* 下载并安装 [代理 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 需要有管理员权限才能完成安装。
* 对于使用经典部署模型创建的虚拟机，请[更新 VM 属性](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)以指示已安装代理。 Azure 资源管理器虚拟机不需要此步骤。

#### <a name="linux-vms"></a>Linux VM

* 从分发存储库安装最新版本的代理。 有关包名称的详细信息，请参阅 [Linux 代理存储库](https://github.com/Azure/WALinuxAgent)。
* 对于使用经典部署模型创建的 VM，请[使用此博客](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)更新 VM 属性并验证是否已安装代理。 资源管理器虚拟机无需执行此步骤。

### <a name="update-the-vm-agent"></a>更新 VM 代理
#### <a name="windows-vms"></a>Windows VM

* 要更新 VM 代理，请重新安装 [VM 代理二进制文件](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 更新此代理之前，请确保 VM 代理更新期间不存在任何备份操作。

#### <a name="linux-vms"></a>Linux VM

* 要更新 Linux VM 代理，请按照[更新 Linux VM 代理](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)一文中的说明进行操作。

    > [!NOTE]
    > 始终使用分发存储库来更新代理。 

    请勿从 GitHub 下载代理代码。 如果最新代理不适用于发行版，请与分发支持部门联系，获取有关获取最新代理的说明。 还可以在 GitHub 存储库中查看最新的 [Windows Azure Linux 代理](https://github.com/Azure/WALinuxAgent/releases)信息。

### <a name="validate-vm-agent-installation"></a>验证 VM 代理安装

验证 Windows VM 上的 VM 代理版本：

1. 登录 Azure 虚拟机并导航到 C:\WindowsAzure\Packages 文件夹。 应会发现“WaAppAgent.exe”文件。
2. 右键单击该文件并转到“属性”。 然后选择“详细信息”选项卡。“产品版本”字段应为 2.6.1198.718 或更高版本。

## <a name="troubleshoot-vm-snapshot-issues"></a>排查 VM 快照问题
VM 备份依赖于向底层存储发出快照命令。 如果无法访问存储或者快照任务运行延迟，则备份作业可能会失败。 以下状态可能会导致快照任务失败：

- 使用 NSG 阻止对存储进行网络访问。 了解有关如何使用 IP 白名单或通过代理服务器[建立网络访问](backup-azure-arm-vms-prepare.md#establish-network-connectivity)的详细信息。
- 配置了 SQL Server 备份的 VM 可能会导致快照任务延迟。 默认情况下，VM 备份在 Windows VM 上创建 VSS 完整备份。 运行 SQL Server 且配置有 SQL Server 备份的 VM 可能会遇到快照延迟。 如果快照延迟导致备份失败，请设置以下注册表项：

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

- 由于在 RDP 中关闭了 VM，VM 状态报告不正确。 如果使用远程桌面关闭虚拟机，请验证门户中的 VM 状态是否正确。 如果状态不正确，请使用门户 VM 仪表板中的“关闭”选项关闭 VM。
- 如果四个以上的 VM 共享同一云服务，请为 VM 选择多个不同的备份策略。 错开备份时间，使同时开始的 VM 备份不超过四个。 尝试将策略中的开始时间至少隔开一小时。
- VM 在高 CPU 或内存情况下运行。 如果虚拟机在高内存或 CPU 使用率（超过 90%）情况下运行，则快照任务将排队并延迟。 最终会超时。如果发生此问题，请尝试按需备份。

## <a name="networking"></a>网络
与所有扩展一样，备份扩展也需要访问公共 Internet 才能工作。 无法访问公共 Internet 可能会出现以下各种情况：

* 扩展安装可能会失败。
* 磁盘快照等备份操作可能失败。
* 显示备份操作状态可能失败。

[此 Azure 支持博客](https://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx)中讨论了解析公共 Internet 地址的必要性。 检查 VNET 的 DNS 配置，并确保可以解析 Azure URI。

正确完成名称解析后，还需要提供对 Azure IP 的访问权限。 若要取消阻止对 Azure 基础结构的访问，请执行以下步骤之一：

- 将 Azure 数据中心 IP 范围加入白名单：
   1. 获取要列入允许列表的 [Azure 数据中心 IP](https://www.microsoft.com/download/details.aspx?id=41653) 列表。
   1. 使用 [New-NetRoute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute) cmdlet 取消阻止 IP。 在 Azure VM 上提升权限的 PowerShell 窗口中运行此 cmdlet。 以管理员身份运行。
   1. 如果已创建规则，则向 NSG 添加规则，以允许访问这些 IP。
- 为 HTTP 流量创建路径：
   1. 如果指定了某种网络限制，请部署 HTTP 代理服务器来路由流量。 例如，网络安全组。 请参阅[建立网络连接](backup-azure-arm-vms-prepare.md#establish-network-connectivity)中的部署 HTTP 代理服务器的步骤。
   1. 如果已创建规则，则向 NSG 添加规则，以允许从 HTTP 代理访问 Internet。

> [!NOTE]
> 必须在来宾内启用 DHCP，才能正常进行 IaaS VM 备份。 如果需要静态专用 IP，请通过 Azure 门户或 PowerShell 配置该 IP。 请确保已启用 VM 内的 DHCP 选项。
> 获取有关如何通过 PowerShell 设置静态 IP 的详细信息：
> - [如何向现有 VM 添加静态内部 IP](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
> - [更改分配给网络接口的专用 IP 地址的分配方法](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
>
>
