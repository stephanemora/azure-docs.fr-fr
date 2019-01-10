---
title: Gérer Azure Analysis Services avec PowerShell | Microsoft Docs
description: Gestion d’Azure Analysis Services avec PowerShell.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 12/19/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b4aec7ff560b0695175a18c9c7e429a8ab733345
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633706"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Gérer Azure Analysis Services avec PowerShell

Cet article décrit les applets de commande PowerShell permettant d’effectuer les tâches de gestion de base de données et de serveur Azure Analysis Services. 

Les tâches de gestion de serveur telles que la création ou la suppression d’un serveur, l’interruption ou la reprise des opérations du serveur ou la modification du niveau de service utilisent les applets de commande Azure Resource Manager (ressource) et les applets de commande Analysis Services (serveur). D’autres tâches de gestion des bases de données comme l’ajout ou la suppression de membres de rôle, le traitement ou le partitionnement utilisent les applets de commande incluses dans le même module SqlServer que SQL Server Analysis Services.

## <a name="permissions"></a>Autorisations
La plupart des tâches PowerShell nécessitent que vous disposiez de privilèges d’administrateur sur le serveur Analysis Services que vous gérez. Les tâches PowerShell planifiées s’exécutent sans assistance. Le compte ou le principal de service exécutant le planificateur doit disposer de privilèges d’administrateur sur le serveur Analysis Services. 

Pour les opérations de serveur utilisant des applets de commande AzureRm, votre compte ou le compte exécutant Scheduler doivent également appartenir au rôle Propriétaire associé à la ressource dans le [contrôle d’accès en fonction du rôle (RBAC) d’Azure](../role-based-access-control/overview.md). 

## <a name="resource-management-operations"></a>Opérations de gestion des ressources 
Module - [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices)

|Applet de commande|Description| 
|------------|-----------------| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|Obtient les détails d’une instance de serveur.|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|Crée une instance de serveur.|   
|[New-AzureRmAnalysisServicesFirewallConfig](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesfirewallconfig)|Crée une nouvelle configuration de pare-feu Analysis Services.|   
|[New-AzureRmAnalysisServicesFirewallRule](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesfirewallrule)|Crée une nouvelle règle de pare-feu Analysis Services.|   
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|Supprime une instance de serveur.|  
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|Reprend une instance de serveur.|  
|[Suspend-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|Interrompt une instance de serveur.| 
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|Modifie une instance de serveur.|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|Teste l’existence d’une instance de serveur.| 

## <a name="server-management-operations"></a>Opérations de gestion des serveurs

Module - [Azure.AnalysisServices](https://www.powershellgallery.com/packages/Azure.AnalysisServices)

|Applet de commande|Description| 
|------------|-----------------| 
|[Add-AzureAnalysisServicesAccount](/powershell/module/azure.analysisservices/add-azureanalysisservicesaccount)|Ajoute un compte authentifié à utiliser pour les demandes d’applet de commande du serveur Azure Analysis Services.| 
|[Export-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/export-azureanalysisservicesinstancelog)|Exporte un journal à partir d’une instance du serveur Analysis Services dans l’environnement actuellement connecté, spécifié dans la commande Add-AzureAnalysisServicesAccount.|  
|[Restart-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Redémarre une instance du serveur Analysis Services dans l’environnement actuellement connecté ; spécifié dans la commande Add-AzureAnalysisServicesAccount.|  
|[Sync-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Synchronise une base de données spécifiée sur l’instance donnée du serveur Analysis Services avec toutes les instances de scale-out de requête au sein de l’environnement actuellement connecté comme spécifié dans la commande Add-AzureAnalysisServicesAccount|  

## <a name="database-operations"></a>Opérations de base de données

Les opérations de bases de données Azure Analysis Services utilisent le même [module SqlServer](https://www.powershellgallery.com/packages/SqlServer) que SQL Server Analysis Services. Toutefois, certaines applets de commande ne sont pas prises en charge par Azure Analysis Services. Pour en savoir plus, consultez [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell).

Le module SqlServer fournit des applets de commande de gestion de base de données spécifiques à chaque tâche, ainsi que l’applet de commande Invoke-ASCmd à usage général, qui accepte un script ou une requête utilisant le langage de script de modèle tabulaire (TMSL). Les applets de commande suivantes du module SqlServer sont prises en charge par Azure Analysis Services.

  
|Applet de commande|Description|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Ajoute un membre à un rôle de base de données.| 
|[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Sauvegarde une base de données Analysis Services.|  
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Supprime un membre d’un rôle de base de données.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Exécute un script TMSL.|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Traite une base de données.|  
|[Invoke-ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Traite une partition.| 
|[Invoke-ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Traiter une table.|  
|[Merge-Partition](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Fusionne une partition.|  
|[Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Restaurer une base de données Analysis Services.| 
  

## <a name="related-information"></a>Informations connexes

* [Télécharger le module SQL Server PowerShell](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Télécharger SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Module SqlServer dans PowerShell Gallery](https://www.powershellgallery.com/packages/SqlServer)    
* [Programmation de modèle tabulaire pour le niveau de compatibilité 1200 et ultérieur](https://msdn.microsoft.com/library/mt712541.aspx)
