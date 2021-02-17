---
title: Répertorier les ressources restaurables dans l’API Azure Cosmos DB pour MongoDB à l’aide de l’API REST
description: Retourne la liste de toutes les combinaisons de bases de données et collections qui existent sur le compte à l’heure et à l’emplacement donnés. Cela permet, dans des scénarios, de valider les ressources qui existent à une heure et à un emplacement donnés.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: b5800c2e9c1f3b36a3ac9afe6cd68f706505fbe0
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537439"
---
# <a name="list-restorable-resources-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Répertorier les ressources restaurables dans l’API Azure Cosmos DB pour MongoDB à l’aide de l’API REST

> [!IMPORTANT]
> La fonctionnalité de restauration à un instant dans le passé (mode de sauvegarde continue) pour Azure Cosmos DB est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Retourne la liste de toutes les combinaisons de bases de données et collections qui existent sur le compte à l’heure et à l’emplacement donnés. Cela permet, dans des scénarios, de valider les ressources qui existent à une heure et à un emplacement donnés. Cette API requiert l’autorisation `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview
```

Avec des paramètres facultatifs :

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
```

## <a name="uri-parameters"></a>Paramètres URI

| Nom | Dans | Obligatoire | Type | Description |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |string| GUID instanceId d’un compte de base de données restaurable. |
| **location** | path | True | string| Région Azure Cosmos DB, avec des espaces entre les mots et chaque mot en majuscules. |
| **subscriptionId** | path | True | string| ID de l’abonnement cible. |
| **api-version** | query | True | string | Version de l’API à utiliser pour cette opération. |
| **restoreLocation** | query | |string| Emplacement où se trouvent les ressources restaurables. |
| **restoreTimestampInUtc** | query | |string| Timestamp où les ressources restaurables existaient. |

## <a name="responses"></a>Réponses

| Nom | Type | Description |
| --- | --- | --- |
| 200 OK | [RestorableMongodbResourcesListResult](#restorablemongodbresourceslistresult)| L’opération s’est terminée avec succès. |
| Autres codes d’état | [DefaultErrorResponse](#defaulterrorresponse)| Réponse d’erreur décrivant la raison de l’échec de l’opération. |


## <a name="examples"></a>Exemples

### <a name="cosmosdbrestorablemongodbresourcelist"></a>CosmosDBRestorableMongodbResourceList

**Exemple de requête**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**Exemple de réponse**

Code d’état : 200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Collection1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Collection1",
        "Collection2"
      ]
    },
    {
      "databaseName": "Database3",
      "collectionNames": []
    }
  ]
}
```

## <a name="definitions"></a>Définitions

|Définition  | Description| | --- || --- | | [DatabaseRestoreResource](#databaserestoreresource) | Bases de données spécifiques à restaurer. | | [DefaultErrorResponse](#defaulterrorresponse) | Réponse d’erreur du service. | | [ErrorResponse](#errorresponse) | Réponse d’erreur. | | [RestorableMongodbResourcesListResult](#restorablemongodbresourceslistresult) | Réponse de l’opération List, qui contient les ressources SQL restaurables. |

### <a name="databaserestoreresource"></a><a id="databaserestoreresource"></a>DatabaseRestoreResource

Bases de données spécifiques à restaurer.

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| collectionNames |string[]| Noms des collections disponibles pour la restauration. |
| databaseName |string| Nom de la base de données disponible pour la restauration. |

### <a name="defaulterrorresponse"></a><a id="defaulterrorresponse"></a>DefaultErrorResponse

Réponse d’erreur du service.

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| erreur | [ErrorResponse](#errorresponse)| Réponse d’erreur. |

### <a name="errorresponse"></a><a id="errorresponse"></a>ErrorResponse

Réponse d’erreur.

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| code |string| Code d’erreur. |
| message |string| Message d’erreur indiquant la raison de l’échec de l’opération. |

### <a name="restorablemongodbresourceslistresult"></a><a id="restorablemongodbresourceslistresult"></a>RestorableMongodbResourcesListResult

Réponse de l’opération List, qui contient les ressources restaurables de l’API Azure Cosmos DB pour MongoDB.

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| value |[DatabaseRestoreResource](#databaserestoreresource)| Liste des ressources restaurables de l’API Azure Cosmos DB pour MongoDB, y compris les noms de la base de données et des collections. |

## <a name="next-steps"></a>Étapes suivantes

* [Répertorier les bases de données restaurables](restorable-mongodb-databases-list.md) dans l’API Azure Cosmos DB pour MongoDB à l’aide de l’API REST.
* [Modèle de ressource](continuous-backup-restore-resource-model.md) du mode de sauvegarde continue.