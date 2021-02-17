---
title: Répertorier les ressources d’API SQL restaurables dans Azure Cosmos DB à l’aide d’une API REST
description: Retourne la liste des combinaisons de base de données et conteneur qui existent sur le compte au timestamp et à l’emplacement donnés. Cela est utile dans des scénarios de validation des ressources qui existent à un timestamp et à un emplacement donnés.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: e31423ae5afd4955ebe7acf1d456496f15f14a6b
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537287"
---
# <a name="list-restorable-sql-api-resources-in-azure-cosmos-db-using-rest-api"></a>Répertorier les ressources d’API SQL restaurables dans Azure Cosmos DB à l’aide d’une API REST

> [!IMPORTANT]
> La fonctionnalité de restauration à un instant dans le passé (mode de sauvegarde continue) pour Azure Cosmos DB est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Retourne la liste des combinaisons de base de données et conteneur qui existent sur le compte au timestamp et à l’emplacement donnés. Cela est utile dans des scénarios de validation des ressources qui existent à un timestamp et à un emplacement donnés. Cette API requiert l’autorisation `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview
```

Avec des paramètres facultatifs :

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
```

## <a name="uri-parameters"></a>Paramètres URI

| Nom | Dans | Obligatoire | Type | Description |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |string| GUID instanceId d’un compte de base de données restaurable. |
| **location** | path | True | string| Région Azure Cosmos DB, avec des espaces entre les mots et chaque mot en majuscules. |
| **subscriptionId** | path | True | string| ID de l’abonnement cible. |
| **api-version** | query | True | string | Version de l’API à utiliser pour cette opération. |
| **restoreLocation** | query | |string| Emplacement où se trouvent les ressources restaurables. |
| **restoreTimestampInUtc** | query | |string| Timestamp du moment où les ressources restaurables existaient. |

## <a name="responses"></a>Réponses

| Nom | Type | Description |
| --- | --- | --- |
| 200 OK | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult)| L’opération s’est terminée avec succès. |
| Autres codes d’état | [DefaultErrorResponse](#defaulterrorresponse)| Réponse d’erreur décrivant la raison de l’échec de l’opération. |

## <a name="examples"></a>Exemples

### <a name="cosmosdbrestorablesqlresourcelist"></a>CosmosDBRestorableSqlResourceList

**Exemple de demande**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**Exemple de réponse**

Code d’état : 200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Container1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Container1",
        "Container2"
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

|Définition  | Description| | --- || --- | | [DatabaseRestoreResource](#databaserestoreresource) | Bases de données spécifiques à restaurer. | | [DefaultErrorResponse](#defaulterrorresponse) | Réponse d’erreur du service. | | [ErrorResponse](#errorresponse) | Réponse d’erreur. | | [RestorableMongodbResourcesListResult](#restorablesqlresourceslistresult) | Réponse de l’opération List, contenant les ressources SQL restaurables. |

### <a name="databaserestoreresource"></a><a id="databaserestoreresource"></a>DatabaseRestoreResource

Bases de données spécifiques à restaurer.

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| collectionNames |string[]| Noms des collections disponibles pour restauration. |
| databaseName |string| Nom de la base de données disponible pour restauration. |

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

### <a name="restorablesqlresourceslistresult"></a><a id="restorablesqlresourceslistresult"></a>RestorableSqlResourcesListResult

Réponse de l’opération List, contenant les ressources SQL restaurables.

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| value |[DatabaseRestoreResource](#databaserestoreresource)[]| Liste des ressources SQL restaurables, incluant les noms de base de données et de collection. |

## <a name="next-steps"></a>Étapes suivantes

* [Répertorier les bases de données restaurables](restorable-sql-databases-list.md) dans une API SQL Azure Cosmos à l’aide d’une API REST.
* [Modèle de ressource](continuous-backup-restore-resource-model.md) du mode de sauvegarde continue.