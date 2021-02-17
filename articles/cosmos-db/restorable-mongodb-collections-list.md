---
title: Répertorier les collections restaurables dans l’API Azure Cosmos DB pour MongoDB – API REST
description: Affichez le flux d’événements de toutes les mutations effectuées sur toutes les collections Azure Cosmos DB MongoDB sous une base de données spécifique. Cela aide dans le cas où un conteneur a été accidentellement supprimé.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: c19de134f40c58a687dcf6bac6ac156e46cef7da
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537541"
---
# <a name="list-restorable-collections-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Répertorier les collections restaurables dans l’API Azure Cosmos DB pour MongoDB à l’aide de l’API REST

> [!IMPORTANT]
> La fonctionnalité de restauration à un instant dans le passé (mode de sauvegarde continue) pour Azure Cosmos DB est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Affichez le flux d’événements de toutes les mutations effectuées sur toutes les collections de l’API Azure Cosmos DB pour MongoDB sous une base de données spécifique. Cela aide dans le cas où un conteneur a été accidentellement supprimé. Cette API requiert l’autorisation `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview
```

Avec des paramètres facultatifs :

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid={restorableMongodbDatabaseRid}
```

## <a name="uri-parameters"></a>Paramètres URI

| Nom | Dans | Obligatoire | Type | Description |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |string| GUID instanceId d’un compte de base de données restaurable. |
| **location** | path | True | string| Région Azure Cosmos DB, avec des espaces entre les mots et chaque mot en majuscules. |
| **subscriptionId** | path | True | string| ID de l’abonnement cible. |
| **api-version** | query | True | string | Version de l’API à utiliser pour cette opération. |
| **restorableMongodbDatabaseRid** | query | |string| ID de ressource de la base de données de l’API Azure Cosmos DB pour MongoDB. |

## <a name="responses"></a>Réponses

| Nom | Type | Description |
| --- | --- | --- |
| 200 OK | [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult)| L’opération s’est terminée avec succès. |
| Autres codes d’état | [DefaultErrorResponse](#defaulterrorresponse)| Réponse d’erreur décrivant la raison de l’échec de l’opération.|

## <a name="examples"></a>Exemples

### <a name="cosmosdbrestorablemongodbcollectionlist"></a>CosmosDBRestorableMongodbCollectionList

**Exemple de requête**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid=PD5DALigDgw=
```

**Exemple de réponse**

Code d’état : 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbCollections",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Collection1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Définitions

|Définition  | Description|
| --- | --- |
| [DefaultErrorResponse](#defaulterrorresponse) | Réponse d’erreur du service. |
| [ErrorResponse](#errorresponse) | Réponse d’erreur. |
| [OperationType](#operationtype) | Enum indiquant le type d’opération de l’événement. |
| [Ressource](#resource) | Ressource d’un événement de collection MongoDB pour l’API Azure Cosmos DB |
| [RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult) | Événement de collection de l’API Azure Cosmos DB pour MongoDB |
| [RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties) | Propriétés d’un événement de collection de l’API Azure Cosmos DB pour MongoDB |
| [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult) | Réponse de l’opération List, qui contient les événements de collection et leurs propriétés. |

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

### <a name="operationtype"></a><a id="operationtype"></a>OperationType

Enum indiquant le type d’opération de l’événement.

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| Créer |string|événement de création de collection|
| DELETE |string|événement de suppression de collection|
| Replace |string|événement de modification de collection|

### <a name="resource"></a><a id="resource"></a>Ressource

Ressource d’un événement de collection Azure Cosmos DB MongoDB

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| _rid |string| Propriété générée par le système. Un identificateur unique. |
| eventTimestamp |string| Heure à laquelle cet événement de collection s’est produit. |
| operationType |[OperationType](#operationtype)|  Type d’opération de cet événement de collection. |
| ownerId |string| Nom de la collection MongoDB.|
| ownerResourceId |string| ID de ressource de la collection MongoDB. |

### <a name="restorablemongodbcollectiongetresult"></a><a id="restorablemongodbcollectiongetresult"></a>RestorableMongodbCollectionGetResult

Événement de collection Azure Cosmos DB MongoDB

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| id |string| Identificateur de ressource unique de la ressource Azure Resource Manager. |
| name |string| Nom de la ressource Resource Manager. |
| properties |[RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties)| Propriétés d’un événement de collection. |
| type |string| Type de ressource Azure. |

### <a name="restorablemongodbcollectionproperties"></a><a id="restorablemongodbcollectionproperties"></a>RestorableMongodbCollectionProperties

Propriétés d’un événement de collection Azure Cosmos DB MongoDB

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| resource | [Ressource](#resource)| Ressource d’un événement de collection MongoDB pour l’API Azure Cosmos DB |

### <a name="restorablemongodbcollectionslistresult"></a><a id="restorablemongodbcollectionslistresult"></a>RestorableMongodbCollectionsListResult

Réponse de l’opération List, qui contient les événements de collection et leurs propriétés.

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| value |[RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult)| Liste des événements de collection de l’API Azure Cosmos DB pour MongoDB et leurs propriétés. |

## <a name="next-steps"></a>Étapes suivantes

* [Répertorier les bases de données restaurables](restorable-mongodb-databases-list.md) dans l’API Azure Cosmos DB pour MongoDB à l’aide de l’API REST.
* [Modèle de ressource](continuous-backup-restore-resource-model.md) du mode de sauvegarde continue.