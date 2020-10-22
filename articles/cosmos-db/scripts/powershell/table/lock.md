---
title: Script PowerShell destiné à créer un verrou de ressource pour une table d’API Table Azure Cosmos
description: Créer un verrou de ressource pour une table d’API Table Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: 0d2d7b0e8d4a67b2ff67a780a71ea46ec54df1de
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92280009"
---
# <a name="create-a-resource-lock-for-azure-cosmos-table-api-table-using-azure-powershell"></a>Créer un verrou de ressource pour une table d’API Table Azure Cosmos à l’aide d’Azure PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-PowerShell-install](../../../../../includes/sample-PowerShell-install-no-ssh.md)]

> [!IMPORTANT]
> Les verrous de ressources ne fonctionnent pas pour les modifications apportées par les utilisateurs qui se connectent via un SDK Cosmos DB, les outils qui se connectent via des clés de compte ou le portail Azure, à moins que le compte Cosmos DB soit d’abord verrouillé avec la propriété `disableKeyBasedMetadataWriteAccess` activée. Pour en savoir plus sur l’activation de cette propriété, consultez [Prévention des modifications à partir des SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Exemple de script

[!code-PowerShell[main](../../../../../PowerShell_scripts/cosmosdb/table/ps-table-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.

```PowerShell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
|**Ressource Azure**| |
| [New-AzResourceLock](https://docs.microsoft.com/PowerShell/module/az.resources/new-azresourcelock) | Crée un verrou de ressource. |
| [Get-AzResourceLock](https://docs.microsoft.com/PowerShell/module/az.resources/get-azresourcelock) | Obtient un verrou de ressource ou liste les verrous de ressources. |
| [Remove-AzResourceLock](https://docs.microsoft.com/PowerShell/module/az.resources/remove-azresourcelock) | Supprime un verrou de ressource. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/PowerShell/).
