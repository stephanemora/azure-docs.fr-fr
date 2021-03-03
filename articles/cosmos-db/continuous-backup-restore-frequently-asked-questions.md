---
title: Foire aux questions relative à la fonctionnalité Azure Cosmos DB de restauration à un instant dans le passé
description: Cet article répertorie les questions fréquentes sur la fonctionnalité Azure Cosmos DB de restauration à un instant dans le passé, disponible dans le mode de sauvegarde continue.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1cf94964f420f7a7d4fc0f6ba0b77813b3e75787
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393222"
---
# <a name="frequently-asked-questions-on-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Foire aux questions relative à la fonctionnalité Azure Cosmos DB de restauration à un instant dans le passé (préversion)
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> La fonctionnalité de restauration à un instant dans le passé (mode de sauvegarde continue) pour Azure Cosmos DB est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article répertorie les questions fréquentes sur la fonctionnalité Azure Cosmos DB de restauration à un instant dans le passé (préversion), disponible dans le mode de sauvegarde continue.

## <a name="how-much-time-does-it-takes-to-restore"></a>Combien de temps prend la restauration ?
La durée de la restauration dépend de la taille de vos données.

### <a name="can-i-submit-the-restore-time-in-local-time"></a>Puis-je envoyer l’heure de restauration en heure locale ?
La restauration peut ne pas se produire selon que les ressources clés comme les bases de données ou les conteneurs existaient à ce moment-là. Vous pouvez le vérifier en entrant l’heure et en examinant la base de données ou le conteneur sélectionné pour une période donnée. Si vous ne voyez aucune ressource à restaurer, le processus de restauration ne fonctionne pas.

### <a name="how-can-i-track-if-an-account-is-being-restored"></a>Comment puis-je savoir si un compte est en cours de restauration ?
Après que vous avez soumis la commande de restauration et attendu sur la même page, une fois l’opération terminée, la barre d’état affiche le message de compte restauré avec succès. Vous pouvez également rechercher le compte restauré et [suivre l’état du compte en cours de restauration](continuous-backup-restore-portal.md#track-restore-status). Pendant que la restauration est en cours, l’état du compte est *En cours de création* ; une fois l’opération de restauration terminée, l’état du compte devient *En ligne*.

De même, pour PowerShell et CLI, vous pouvez suivre la progression de l’opération de restauration en exécutant la commande `az cosmosdb show` comme suit :

```azurecli-interactive
az cosmosdb show --name "accountName" --resource-group "resourceGroup"
```

La propriété provisioningState affiche *Succeeded* quand le compte est en ligne.

```json
{
"virtualNetworkRules": [],
"writeLocations" : [
{
    "documentEndpoint": "https://<accountname>.documents.azure.com:443/", 
    "failoverpriority": 0,
    "id": "accountName" ,
    "isZoneRedundant" : false, 
    "locationName": "West US 2", 
    "provisioningState": "Succeeded"
}
]
}
```

### <a name="how-can-i-find-out-whether-an-account-was-restored-from-another-account"></a>Comment puis-je savoir si un compte a été restauré à partir d’un autre compte ?
Exécutez la commande `az cosmosdb show`. Dans la sortie, vous pouvez voir la valeur de la propriété `createMode`. Si la valeur est définie sur **Restore**, cela indique que le compte a été restauré à partir d’un autre compte. La propriété `restoreParameters` contient des détails supplémentaires, tels que `restoreSource`, qui a l’ID de compte source. Le dernier GUID dans le paramètre `restoreSource` est l’instanceId du compte source.

Par exemple, dans la sortie suivante, l’ID d’instance du compte source est *7b4bb-f6a0-430e-ade1-638d781830cc*.

```json
"restoreParameters": {
   "databasesToRestore" : [],
   "restoreMode": "PointInTime",
   "restoreSource": "/subscriptions/2a5b-f6a0-430e-ade1-638d781830dd/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/7b4bb-f6a0-430e-ade1-638d781830cc",
   "restoreTimestampInUtc": "2020-06-11T22:05:09Z"
}
```

### <a name="what-happens-when-i-restore-a-shared-throughput-database-or-a-container-within-a-shared-throughput-database"></a>Que se passe-t-il lorsque je restaure une base de données à débit partagé ou un conteneur dans une base de données à débit partagé ?
L’intégralité de la base de données à débit partagé est restaurée. Vous ne pouvez pas choisir un sous-ensemble de conteneurs dans une base de données à débit partagé pour les restaurer.

### <a name="what-is-the-use-of-instanceid-in-the-account-definition"></a>Quelle est l’utilisation d’InstanceID dans la définition de compte ?
À tout moment, la propriété `accountName` du compte Azure Cosmos DB est unique au monde tant qu’il est actif. Toutefois, une fois le compte supprimé, il est possible de créer un autre compte portant le même nom. Par conséquent, la propriété « accountName » n’est plus suffisante pour identifier l’instance d’un compte. 

ID ou `instanceId` est une propriété d’une instance de compte qui est utilisée pour lever l’ambiguïté entre plusieurs comptes (actifs et supprimés) s’ils portent le même nom pour la restauration. Vous pouvez récupérer l’ID d’instance en exécutant les commandes `Get-AzCosmosDBRestorableDatabaseAccount` ou `az cosmosdb restorable-database-account`. La valeur de l’attribut de nom désigne « InstanceID ».

## <a name="next-steps"></a>Étapes suivantes

* Qu’est-ce que le mode de [sauvegarde continue](continuous-backup-restore-introduction.md) ?
* Configurez et gérez la sauvegarde continue en utilisant [Portail Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md) ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Gérez les autorisations](continuous-backup-restore-permissions.md) requises pour restaurer les données en mode de sauvegarde continue.
* [Modèle de ressource du mode de sauvegarde continue](continuous-backup-restore-resource-model.md).