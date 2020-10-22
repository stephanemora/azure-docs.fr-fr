---
title: Script PowerShell destiné à créer un verrou de ressource pour une base de données et un graphe d’API Gremlin Azure Cosmos
description: Créer un verrou de ressource pour une base de données et un graphe d’API Gremlin Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: 6e119fe2cc54ce38dcb8ac2d0c711056dc7d77ce
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92280645"
---
# <a name="create-a-resource-lock-for-azure-cosmos-gremlin-api-database-and-graph-using-azure-powershell"></a>Créer un verrou de ressource pour une base de données et un graphe d’API Gremlin Azure Cosmos à l’aide d’Azure PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

> [!IMPORTANT]
> Les verrous de ressources ne fonctionnent pas pour les modifications apportées par les utilisateurs qui se connectent à l’aide d’un SDK Gremlin ou du portail Azure, sauf si le compte Cosmos DB est d’abord verrouillé avec la propriété `disableKeyBasedMetadataWriteAccess` activée. Pour en savoir plus sur l’activation de cette propriété, consultez [Prévention des modifications à partir des SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-lock.ps1 "Create, list, and remove resource locks")]

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
