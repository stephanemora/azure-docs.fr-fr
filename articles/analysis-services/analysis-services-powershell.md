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
ms.openlocfilehash: 177d74a54e4ab4de698cbb63091656cc8b584e2b
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010682"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Gérer Azure Analysis Services avec PowerShell

Cet article décrit les applets de commande PowerShell permettant d’effectuer les tâches de gestion de base de données et de serveur Azure Analysis Services. 

Les tâches de gestion de serveur telles que la création ou la suppression d’un serveur, l’interruption ou la reprise des opérations du serveur ou la modification du niveau de service utilisent les applets de commande Azure Resource Manager (ressource) et les applets de commande Analysis Services (serveur). D’autres tâches de gestion des bases de données comme l’ajout ou la suppression de membres de rôle, le traitement ou le partitionnement utilisent les applets de commande incluses dans le même module SqlServer que SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Autorisations

La plupart des tâches PowerShell nécessitent que vous disposiez de privilèges d’administrateur sur le serveur Analysis Services que vous gérez. Les tâches PowerShell planifiées s’exécutent sans assistance. Le compte ou le principal de service exécutant le planificateur doit disposer de privilèges d’administrateur sur le serveur Analysis Services. 

Pour les opérations de serveur à l’aide des applets de commande Azure PowerShell, votre compte ou le compte exécutant scheduler doit également appartenir au rôle de propriétaire de la ressource dans [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-management-operations"></a>Opérations de gestion des ressources 

Module - [Az.AnalysisServices](/powershell/module/az.analysisservices)

|Applet de commande|Description| 
|------------|-----------------| 
|[Get-AzAnalysisServicesServer](/powershell/module/az.analysisservices/get-azanalysisservicesserver)|Obtient les détails d’une instance de serveur.|  
|[New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver)|Crée une instance de serveur.|   
|[New-AzAnalysisServicesFirewallConfig](/powershell/module/az.analysisservices/new-azanalysisservicesfirewallconfig)|Crée une nouvelle configuration de pare-feu Analysis Services.|   
|[New-AzAnalysisServicesFirewallRule](/powershell/module/az.analysisservices/new-azanalysisservicesfirewallrule)|Crée une nouvelle règle de pare-feu Analysis Services.|   
|[Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/remove-azanalysisservicesserver)|Supprime une instance de serveur.|  
|[Resume-AzAnalysisServicesServer](/powershell/module/az.analysisservices/resume-azanalysisservicesserver)|Reprend une instance de serveur.|  
|[Suspend-AzAnalysisServicesServer](/powershell/module/az.analysisservices/suspend-azanalysisservicesserver)|Interrompt une instance de serveur.| 
|[Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver)|Modifie une instance de serveur.|   
|[Test-AzAnalysisServicesServer](/powershell/module/az.analysisservices/test-azanalysisservicesserver)|Teste l’existence d’une instance de serveur.| 

## <a name="server-management-operations"></a>Opérations de gestion des serveurs

Module - [Azure.AnalysisServices](https://www.powershellgallery.com/packages/Azure.AnalysisServices)

|Applet de commande|Description| 
|------------|-----------------| 
|[Add-AzAnalysisServicesAccount](/powershell/module/az.analysisservices/add-AzAnalysisServicesaccount)|Ajoute un compte authentifié à utiliser pour les demandes d’applet de commande du serveur Azure Analysis Services.| 
|[Export-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/export-AzAnalysisServicesinstancelog)|Exporte un journal à partir d’une instance de serveur Analysis Services dans actuellement connecté dans un environnement tel que spécifié dans la commande de Add-AzAnalysisServicesAccount|  
|[Restart-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/restart-AzAnalysisServicesinstance)|Redémarre une instance de serveur Analysis Services dans l’environnement actuellement connecté ; spécifié dans la commande de Add-AzAnalysisServicesAccount.|  
|[Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/restart-AzAnalysisServicesinstance)|Synchronise une base de données spécifiée sur l’instance spécifiée de serveur Analysis Services pour toutes les instances de montée en puissance de requête dans actuellement connecté dans un environnement tel que spécifié dans la commande de Add-AzAnalysisServicesAccount|  

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
