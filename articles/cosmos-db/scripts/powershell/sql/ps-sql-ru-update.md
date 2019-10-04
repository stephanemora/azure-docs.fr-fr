---
title: Script Azure PowerShell – Mise à jour du débit (RU/s) dans Azure Cosmos DB pour l’API SQL (Core)
description: Script Azure PowerShell – Mise à jour du débit (RU/s) dans Azure Cosmos DB pour l’API SQL (Core)
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/18/2019
ms.author: mjbrown
ms.openlocfilehash: 6b54b6021baf7a91c79309f8fa69b4df67d3887e
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178747"
---
# <a name="update-rus-for-a-database-or-container-for-azure-cosmos-db---sql-core-api"></a>Mise à jour du débit (RU/s) d’une base de données ou d’un conteneur pour Azure Cosmos DB – API SQL (Core)

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-update.ps1 "Update throughput on a database or container for SQL (Core) API")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
|**Ressources Azure**| |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Crée une ressource. |
|**Groupes de ressources Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/).

Vous trouverez des exemples supplémentaires de scripts Azure Cosmos DB PowerShell sur la page [Scripts PowerShell Azure Cosmos DB](../../../powershell-samples.md).