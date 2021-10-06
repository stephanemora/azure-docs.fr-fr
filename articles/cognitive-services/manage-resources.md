---
title: Récupérer une ressource Cognitive Services supprimée
titleSuffix: Azure Cognitive Services
description: Cet article donne des instructions sur la récupération d’une ressource de Cognitive Services qui a déjà été supprimée.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: nitinme
ms.openlocfilehash: 4dce35c1713a4dcb4880080d1f28ed124a2209be
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546074"
---
# <a name="recover-deleted-cognitive-services-resources"></a>Récupérer des ressources Cognitive Services supprimées

Cet article donne des instructions sur la récupération d’une ressource de Cognitive Services qui est supprimée. L’article explique également comment vider une ressource supprimée.

> [!NOTE]
> Les instructions de cet article sont applicables aussi bien à une ressource multiservice et qu’à une ressource monoservice. La ressource multiservice permet d’accéder à plusieurs instances de Cognitive Services à l’aide d’une seule clé et d’un seul point de terminaison. La ressource monoservice ne permet, quant à elle, d’accéder qu’au Cognitive Service spécifique pour lequel elle a été créée.

## <a name="prerequisites"></a>Configuration requise

* La ressource à récupérer doit avoir été supprimée au cours des dernières 48 heures.
* La ressource à récupérer ne doit pas déjà avoir été vidée. Une ressource vidée ne peut pas être récupérée.
* Avant de tenter de récupérer une ressource supprimée, assurez-vous que le groupe de ressources pour ce compte existe. Si le groupe de ressources a été supprimé, vous devez le recréer. Il est impossible de récupérer un groupe de ressources. Pour plus d’informations, consultez  [Gérer des groupes de ressources](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Si la ressource supprimée a utilisé des clés gérées par le client avec Azure Key Vault et que le coffre de clés a également été supprimé, vous devez restaurer le coffre de clés avant de restaurer la ressource Cognitive Services. Pour plus d’informations, consultez [Gestion de la récupération d’Azure Key Vault](../key-vault/general/key-vault-recovery.md).
* Si la ressource supprimée utilisait un stockage géré par le client et que le compte de stockage a également été supprimé, vous devez restaurer le compte de stockage avant de restaurer la ressource Cognitive Services. Pour savoir comment procéder, consultez [Récupérer un compte de stockage supprimé](../storage/common/storage-account-recover.md).

Votre abonnement doit inclure les autorisations `Microsoft.CognitiveServices/locations/resourceGroups/deletedAccounts/delete` pour pouvoir vider les ressources, comme [Contributeur Cognitive Services](../role-based-access-control/built-in-roles.md#cognitive-services-contributor) ou [Contributeur](../role-based-access-control/built-in-roles.md#contributor). 

## <a name="recover-a-deleted-resource"></a>Récupérer une ressource supprimée 

Pour récupérer une ressource Cognitive Services supprimée, utilisez les commandes suivantes. Le cas échéant, remplacez :

* `{subscriptionID}` par votre ID d’abonnement Azure
* `{resourceGroup}` par votre groupe de ressources
* `{resourceName}` par le nom de votre ressource
* `{location}` par l’emplacement de votre ressource

### <a name="using-the-rest-api"></a>En utilisant l’API REST

Utilisez la commande `PUT` suivante :

```rest-api
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroup}/providers/Microsoft.CognitiveServices/accounts/{resourceName}?Api-Version=2021-04-30
```

Dans le corps de la requête, utilisez le format JSON suivant :

```json
{ 
  "location": "{location}", 
   "properties": { 
        "restore": true 
    } 
} 
```

### <a name="using-powershell"></a>Utilisation de PowerShell

Utilisez la commande suivante pour restaurer la ressource : 

```powershell
New-AzResource -Location {location} -Properties @{restore=$true} -ResourceId /subscriptions/{subscriptionID}/resourceGroups/{resourceGroup}/providers/Microsoft.CognitiveServices/accounts/{resourceName}   -ApiVersion 2021-04-30 
```

Si vous avez besoin de trouver le nom de vos ressources supprimées, vous pouvez obtenir la liste de noms des ressources supprimées à l’aide de la commande suivante : 

```powershell
Get-AzResource -ResourceId /subscriptions/{subscriptionId}/providers/Microsoft.CognitiveServices/deletedAccounts -ApiVersion 2021-04-30 
```

### <a name="using-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure (CLI)

```azurecli-interactive
az resource create --subscription {subscriptionID} -g {resourceGroup} -n {resourceName} --location {location} --namespace Microsoft.CognitiveServices --resource-type accounts --properties "{\"restore\": true}"
```

## <a name="purge-a-deleted-resource"></a>Vider une ressource supprimée 

Une fois que vous avez supprimé une ressource, vous ne pouvez pas en créer une autre portant le même nom pendant 48 heures. Pour créer une ressource portant le même nom, vous devez vider la ressource supprimée.

Pour vider une ressource Cognitive Services supprimée, utilisez les commandes suivantes. Le cas échéant, remplacez :

* `{subscriptionID}` par votre ID d’abonnement Azure
* `{resourceGroup}` par votre groupe de ressources
* `{resourceName}` par le nom de votre ressource
* `{location}` par l’emplacement de votre ressource

> [!NOTE]
> Une fois qu’une ressource est vidée, elle est définitivement supprimée et ne peut pas être restaurée. Vous perdrez toutes les données et clés associées à la ressource.

### <a name="using-the-rest-api"></a>En utilisant l’API REST

Utilisez la commande `DELETE` suivante :

```rest-api
https://management.azure.com/subscriptions/{subscriptionID}/providers/Microsoft.CognitiveServices/locations/{location}/resourceGroups/{resourceGroup}/deletedAccounts/{resourceName}?Api-Version=2021-04-30`
```

### <a name="using-powershell"></a>Utilisation de PowerShell

```powershell
Remove-AzResource -ResourceId /subscriptions/{subscriptionID}/providers/Microsoft.CognitiveServices/locations/{location}/resourceGroups/{resourceGroup}/deletedAccounts/{resourceName}  -ApiVersion 2021-04-30`
```

### <a name="using-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure (CLI)

```azurecli-interactive
az resource delete --ids /subscriptions/{subscriptionId}/providers/Microsoft.CognitiveServices/locations/{location}/resourceGroups/{resourceGroup}/deletedAccounts/{resourceName}
```

## <a name="see-also"></a>Voir aussi
* [Créer une ressource en utilisant le portail Azure](cognitive-services-apis-create-account.md)
* [Créer une ressource en utilisant Azure CLI](cognitive-services-apis-create-account-cli.md)
* [Créer une ressource en utilisant la bibliothèque de client](cognitive-services-apis-create-account-client-library.md)
* [Créer une ressource à l’aide d’un modèle ARM](create-account-resource-manager-template.md)
