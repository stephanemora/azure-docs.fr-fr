---
title: FAQ sur Azure Automation | Microsoft Docs
description: Réponses aux questions fréquemment posées sur Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921756"
---
# <a name="azure-automation-frequently-asked-questions"></a>Questions fréquemment posées sur Azure Automation

Ce FAQ Microsoft est une liste de questions fréquemment posées concernant Azure Automation. Si vous avez d’autres questions sur ses capacités, rendez-vous sur le forum de discussion et publiez vos questions. Lorsqu’une question est fréquemment posée, nous l’ajoutons à cet article pour qu’elle soit facile et rapide à trouver.

## <a name="update-management-solution"></a>Solution Update Management

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Puis-je empêcher les mises à niveau inattendues au niveau du système d’exploitation ?

Sur certaines variantes Linux, comme Red Hat Enterprise Linux, les mises à niveau du système d’exploitation peuvent se produire par le biais de packages. Celles-ci peuvent alors entraîner des exécutions Update Management au cours desquelles le numéro de version du système d’exploitation change. Étant donné que la solution Update Management utilise les mêmes méthodes pour mettre à jour des packages qu’un administrateur sur l’ordinateur Linux local, ce comportement est volontaire.

Pour éviter de mettre à jour la version du système d’exploitation par le biais de déploiements Update Management, utilisez la fonctionnalité **Exclusion**.

Dans Red Hat Enterprise Linux, le nom du package à exclure est : redhat-release-server.x86_64.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Pourquoi les mises à jour critiques et de sécurité ne sont-elles pas appliquées ?

Lorsque vous déployez des mises à jour sur un ordinateur Linux, vous pouvez sélectionner des classifications. Cette option filtre les mises à jour afin d’appliquer à la machine uniquement celles qui remplissent les critères de classification spécifiés. Ce filtre est appliqué localement sur l’ordinateur lorsque la mise à jour est déployée.

Comme Update Management enrichit les mises à jour dans le cloud, certaines mises à jour peuvent être signalées dans Update Management comme ayant un impact sur la sécurité quand bien même l’ordinateur local n’a pas ces informations. Ainsi, si vous appliquez des mises à jour critiques à un ordinateur Linux, certaines mises à jour, non signalées comme ayant un impact sur la sécurité pour cet ordinateur, peuvent ne pas être appliquées. Toutefois, Update Management peut quand même signaler cet ordinateur comme étant non conforme, car il contient des informations supplémentaires sur la mise à jour concernée.

Le déploiement de mises à jour par classification ne fonctionne pas sur les versions RTM de CentOS. Pour déployer correctement les mises à jour pour CentOS, sélectionnez toutes les classifications pour garantir que les mises à jour sont appliquées. Pour SUSE, le fait de sélectionner *uniquement* **Autres mises à jour** en tant que classification peut également entraîner l’installation de certaines mises à jour de sécurité si les mises à jour de sécurité associées à zypper (gestionnaire de package) ou à ses dépendances doivent être d’abord installées. Il s’agit d’une limitation de zypper. Dans certains cas, vous devrez peut-être réexécuter le déploiement des mises à jour. Pour savoir si cela est nécessaire, consultez le journal des mises à jour.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Puis-je déployer des mises à jour dans tous les locataires Azure ?

Si des machines se trouvent dans un autre rapport de tenant Azure pour Update Management que vous devez corriger, vous devez utiliser la solution de contournement suivante pour planifier l’opération. Vous pouvez utiliser la cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) avec le commutateur `-ForUpdate` pour créer une planification, et utiliser la cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) et faire passer les machines de l’autre tenant au paramètre `-NonAzureComputer`. L’exemple suivant vous montre comment procéder :

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Étapes suivantes

Si vous ne trouvez pas de réponse à votre question ici, vous pouvez consulter le forum Internet suivant pour plus de questions et réponses.

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

Pour obtenir des commentaires d’ordre général concernant la solution Update Management, consultez le [forum de commentaires](https://feedback.azure.com/forums/905242-update-management).