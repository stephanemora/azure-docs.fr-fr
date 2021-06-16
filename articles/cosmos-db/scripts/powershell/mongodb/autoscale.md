---
title: Script PowerShell pour créer une base de données et une collection d’API MongoDB avec mise à l’échelle automatique pour Azure Cosmos
description: Script Azure PowerShell - Créer une base de données et une collection d’API MongoDB avec mise à l’échelle automatique pour Azure Cosmos
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 90f9db69b8c376ada7f8c3f377cbf8f02da1b8a2
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110680106"
---
# <a name="create-a-database-and-collection-with-autoscale-for-azure-cosmos-db---mongodb-api"></a>Créer une base de données et une collection avec mise à l’échelle automatique pour Azure Cosmos DB - API MongoDB
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Cet exemple nécessite Azure PowerShell Az 5.4.0 ou ultérieur. Exécutez `Get-Module -ListAvailable Az` pour voir quelles versions sont installées.
Si vous devez l’installer, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps).

Exécutez [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) pour vous connecter à Azure.

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-autoscale.ps1 "Create a database and collection with autoscale for MongoDB API")]

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
| [New-AzCosmosDBMongoDBDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbdatabase) | Crée une base de données d’API MongoDB. |
| [New-AzCosmosDBMongoDBIndex](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbindex) | Crée un index d’API MongoDB. |
| [New-AzCosmosDBMongoDBCollection](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbcollection) | Crée une collection d’API MongoDB. |
|**Groupes de ressources Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/).
