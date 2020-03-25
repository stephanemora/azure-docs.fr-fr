---
title: Exemple PowerShell – Groupe de basculement – Instance gérée d’Azure SQL Database
description: Exemple de script Azure PowerShell pour créer une instance gérée d’Azure SQL Database, l’ajouter à un groupe de basculement et tester le basculement.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: e50877f6f3194885b139683fe865144384716b48
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691766"
---
# <a name="use-powershell-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>Utiliser PowerShell pour ajouter une instance gérée d’Azure SQL Database à un groupe de basculement 

Cet exemple de script PowerShell crée deux instances gérées, les ajoute à un groupe de basculement, puis teste le basculement de l’instance gérée principale vers l’instance gérée secondaire. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous avez besoin de la version 1.4.0 d’AZ PowerShell ou d’une version ultérieure pour suivre ce tutoriel. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="sample-scripts"></a>Exemples de scripts

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées. Vous devez supprimer le groupe de ressources à deux reprises. La première suppression du groupe de ressources entraîne la suppression de l’instance gérée et des clusters virtuels, mais échouera avec le message d’erreur `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`. Exécutez la commande Remove-AzResourceGroup une deuxième fois pour supprimer toutes les ressources résiduelles, ainsi que le groupe de ressources.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crée un groupe de ressources Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Créer un réseau virtuel.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Ajoute une configuration de sous-réseau à un réseau virtuel. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtient un réseau virtuel dans un groupe de ressources. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Obtient un sous-réseau dans un réseau virtuel. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Crée un groupe de sécurité réseau. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Crée une table de routage. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Met à jour une configuration de sous-réseau pour un réseau virtuel.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Met à jour un réseau virtuel.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Obtient un groupe de sécurité réseau. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Ajoute une configuration de règle de sécurité réseau à un groupe de sécurité réseau. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Met à jour un groupe de sécurité réseau.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Ajoute un itinéraire à une table de routage. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Met à jour une table de routage.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Crée une instance managée Azure SQL Database.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Retourne des informations sur la base de données Azure SQL Managed Database Instance. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Crée une adresse IP publique.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Crée une configuration IP pour une passerelle de réseau virtuel |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Crée une passerelle de réseau virtuel |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Crée une connexion entre les deux passerelles de réseau virtuel.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Crée un groupe de basculement d’une instance managée Azure SQL Database.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Obtient ou répertorie des groupes de basculement d’instance managée.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Exécute un basculement d’un groupe de basculement d’une instance managée. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources. | 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts SQL Database PowerShell dans [Scripts PowerShell Azure SQL Database](../sql-database-powershell-samples.md).
