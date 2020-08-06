---
title: Script PowerShell pour obtenir des opérations de clé et de chaîne de connexion pour un compte Azure Cosmos DB
description: Exemple de script Azure PowerShell – Opérations de clé de compte et de chaîne de connexion pour un compte Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: f6dd9d1290ea9d18fc6a5f18196585926b2ab91a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505211"
---
# <a name="connection-string-and-account-key-operations-for-an-azure-cosmos-db-account-using-powershell"></a>Opérations de clé de compte et de chaîne de connexion pour un compte Azure Cosmos DB en utilisant PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exemple de script

> [!NOTE]
> Cet exemple illustre l’utilisation d’un compte d’API SQL. Pour utiliser cet exemple avec d’autres API, copiez les propriétés afférentes et appliquez-les au script propre à votre API.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-keys-connection-strings.ps1 "Connection strings and account keys for Azure Cosmos account")]

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
| [Get-AzCosmosDBAccountKey](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbaccountkey) | Obtient la chaîne de connexion ou la clé (en lecture-écriture ou en lecture seule) pour un compte Cosmos DB. |
| [New-AzCosmosDBAccountKey](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccountkey) | Regénère la clé spécifiée pour un compte Cosmos DB. |
|**Groupes de ressources Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/).

Vous trouverez des exemples supplémentaires de scripts Azure Cosmos DB PowerShell sur la page [Scripts PowerShell Azure Cosmos DB](../../../powershell-samples.md).
