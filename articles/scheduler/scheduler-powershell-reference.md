---
title: Informations de référence sur les applets de commande PowerShell - Azure Scheluler
description: En savoir plus sur les applets de commande PowerShell pour Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: daf960bec0fac5f0c96749c219304bc77a4ba905
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358554"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Informations de référence sur les applets de commande PowerShell pour Azure Scheluler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) remplace Azure Scheduler, qui est en cours de retrait. Pour planifier des travaux, [essayez à la place Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vous pouvez utiliser les applets de commande PowerShell pour créer des scripts afin de créer et de gérer des travaux et des collections de travaux Scheduler. Cet article répertorie les principales [applets de commande PowerShell pour Azure Scheduler](/powershell/module/azurerm.scheduler) avec des liens vers leurs articles de référence. Pour installer Azure PowerShell pour votre abonnement Azure, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/overview). Pour plus d’informations sur les [applets de commande Azure Resource Manager](/powershell/azure/overview), consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

| Applet de commande | Description |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azschedulerjobcollection) |Désactive une collection de travaux. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azschedulerjobcollection) |Active une collection de travaux. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azschedulerjob) |Récupère les travaux du Planificateur. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azschedulerjobcollection) |Récupère les collections de travaux. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azschedulerjobhistory) |Affiche l’historique des travaux. |
| [New-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azschedulerhttpjob) |Crée un travail HTTP. |
| [New-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azschedulerjobcollection) |Crée une collection de travaux. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azschedulerservicebusqueuejob) | Crée un travail de file d’attente Service Bus. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azschedulerservicebustopicjob) |Crée un travail de rubrique Service Bus. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azschedulerstoragequeuejob) |Crée un travail de file d’attente de stockage. |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azschedulerjob) |Supprime un travail du Planificateur. |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azschedulerjobcollection) |Supprime une collection de travaux. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azschedulerhttpjob) |Modifie un travail HTTP du Planificateur. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azschedulerjobcollection) |Modifie une collection de travaux. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azschedulerservicebusqueuejob) |Modifie un travail de file d’attente Service Bus. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azschedulerservicebustopicjob) |Modifie un travail de rubrique Service Bus. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azschedulerstoragequeuejob) |Modifie un travail de file d’attente de stockage. |
||| 

Pour plus d’informations, vous pouvez exécuter les applets de commande suivantes : 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Voir aussi

* [Présentation d’Azure Scheduler](scheduler-intro.md)
* [Concepts, terminologie et hiérarchie d’entité](scheduler-concepts-terms.md)
* [Créer et planifier votre premier travail - portail Azure](scheduler-get-started-portal.md)
* [Référence API REST de planificateur Azure](https://msdn.microsoft.com/library/mt629143)
