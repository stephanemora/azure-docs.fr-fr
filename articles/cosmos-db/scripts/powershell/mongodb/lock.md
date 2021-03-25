---
title: Script PowerShell destiné à créer un verrou de ressource pour une base de données et une collection d’API MongoDB Azure Cosmos
description: Créer un verrou de ressource pour une base de données et une collection d’API MongoDB Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: 0d7b5037e3adb4a8710d117b98e93536e8a278df
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98677929"
---
# <a name="create-a-resource-lock-for-azure-cosmos-mongodb-api-database-and-collection-using-azure-powershell"></a>Créer un verrou de ressource pour une base de données et une collection d’API MongoDB Azure Cosmos à l’aide d’Azure PowerShell
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Cet exemple nécessite Azure PowerShell Az 5.4.0 ou ultérieur. Exécutez `Get-Module -ListAvailable Az` pour voir quelles versions sont installées.
Si vous devez l’installer, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps).

Exécutez [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) pour vous connecter à Azure.

> [!IMPORTANT]
> Les verrous de ressources ne fonctionnent pas pour les modifications apportées par les utilisateurs qui se connectent à l’aide d’un SDK MongoDB, de Mongoshell, d’outils ou du portail Azure, sauf si le compte Cosmos DB est d’abord verrouillé avec la propriété `disableKeyBasedMetadataWriteAccess` activée. Pour en savoir plus sur l’activation de cette propriété, consultez [Prévention des modifications à partir des SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-lock.ps1 "Create, list, and remove resource locks")]

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
| [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) | Crée un verrou de ressource. |
| [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock) | Obtient un verrou de ressource ou liste les verrous de ressources. |
| [Remove-AzResourceLock](/powershell/module/az.resources/remove-azresourcelock) | Supprime un verrou de ressource. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/).