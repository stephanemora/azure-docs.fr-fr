---
title: Script PowerShell destiné à créer un verrou de ressource pour un espace de clés et une table d’API Cassandra Azure Cosmos
description: Créer un verrou de ressource pour un espace de clés et une table d’API Cassandra Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: be0ba84b323f235d15761ed5bf85a380f48276a2
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505431"
---
# <a name="create-a-resource-lock-for-azure-cosmos-cassandra-api-keyspace-and-table-using-azure-powershell"></a>Créer un verrou de ressource pour un espace de clés et une table d’API Cassandra Azure Cosmos avec Azure PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

> [!IMPORTANT]
> Les verrous de ressources ne fonctionnent pas pour les modifications apportées par les utilisateurs qui se connectent à l’aide d’un SDK Cassandra, de l’interpréteur de commandes CQL ou du portail Azure, sauf si le compte Cosmos DB est d’abord verrouillé avec la propriété `disableKeyBasedMetadataWriteAccess` activée. Pour en savoir plus sur l’activation de cette propriété, consultez [Prévention des modifications à partir des SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
|**Ressource Azure**| |
| [New-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock) | Crée un verrou de ressource. |
| [Get-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcelock) | Obtient un verrou de ressource ou liste les verrous de ressources. |
| [Remove-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcelock) | Supprime un verrou de ressource. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/).

Vous trouverez des exemples supplémentaires de scripts Azure Cosmos DB PowerShell sur la page [Scripts PowerShell Azure Cosmos DB](../../../powershell-samples.md).