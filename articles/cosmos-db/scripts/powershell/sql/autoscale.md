---
title: Script PowerShell pour créer une base de données et un conteneur d’API SQL avec mise à l’échelle automatique pour Azure Cosmos DB
description: Script Azure PowerShell - Créer une base de données et un conteneur d’API SQL avec mise à l’échelle automatique pour Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 42407f3ebb0b7f3a726c0031a0af5effc338c880
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677985"
---
# <a name="create-a-database-and-container-with-autoscale-for-azure-cosmos-db---sql-api"></a>Créer une base de données et un conteneur avec mise à l’échelle automatique pour Azure Cosmos DB - API SQL
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Cet exemple nécessite Azure PowerShell Az 5.4.0 ou ultérieur. Exécutez `Get-Module -ListAvailable Az` pour voir quelles versions sont installées.
Si vous devez l’installer, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps).

Exécutez [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) pour vous connecter à Azure.

## <a name="sample-script"></a>Exemple de script

Ce script crée un compte Cosmos pour l’API Core (SQL) dans deux régions avec une cohérence de niveau session, une base de données et un conteneur avec débit avec mise à l’échelle automatique et une stratégie d’index par défaut.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-autoscale.ps1 "Create an account, database, and container with autoscale for Core (SQL) API")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
|**Azure Cosmos DB**| |
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Crée un compte Cosmos DB. |
| [New-AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbsqldatabase) | Crée une base de données SQL Cosmos DB. |
| [New-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer) | Crée un conteneur SQL Cosmos DB. |
|**Groupes de ressources Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/).
