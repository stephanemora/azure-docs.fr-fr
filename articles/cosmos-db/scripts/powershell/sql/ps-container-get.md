---
title: Azure PowerShell script - obtenir les propriétés d’un conteneur dans un compte Azure Cosmos
description: 'Script Azure PowerShell exemple : obtenir les propriétés d’un conteneur dans un compte Azure Cosmos'
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 05/086/2019
ms.author: mjbrown
ms.openlocfilehash: 09b773d1939792b97e9fd0ba21bcfdcdca4b8b2e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078204"
---
# <a name="get-the-properties-of-a-container-in-an-azure-cosmos-account-using-powershell"></a>Obtenir les propriétés d’un conteneur dans un compte Azure Cosmos à l’aide de PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-container-get.ps1 "Get the properties of a container in an Azure Cosmos account")]

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
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | Obtient une ressource. |
|**Groupes de ressources Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/).

Vous trouverez des exemples supplémentaires de scripts Azure Cosmos DB PowerShell sur la page [Scripts PowerShell Azure Cosmos DB](../../../powershell-samples.md).