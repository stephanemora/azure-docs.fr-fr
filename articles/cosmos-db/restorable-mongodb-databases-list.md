---
title: Répertorier les bases de données restaurables dans l’API Azure Cosmos DB pour MongoDB à l’aide de l’API REST
description: Affichez le flux d’événements de toutes les mutations effectuées sur toutes les bases de données MongoDB Azure Cosmos DB sous le compte pouvant être restauré. Cela vous aide à récupérer l’heure de la suppression dans le cas où la base de données a été supprimée accidentellement.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 5ebb0a23822074f61a16bf1d7652dba589399542
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537508"
---
# <a name="list-restorable-databases-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Répertorier les bases de données restaurables dans l’API Azure Cosmos DB pour MongoDB à l’aide de l’API REST

> [!IMPORTANT]
> La fonctionnalité de restauration à un instant dans le passé (mode de sauvegarde continue) pour Azure Cosmos DB est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Affichez le flux d’événements de toutes les mutations effectuées sur toutes les bases de données MongoDB Azure Cosmos DB sous le compte pouvant être restauré. Cela vous aide à récupérer l’heure de la suppression dans le cas où la base de données a été supprimée accidentellement. Cette API requiert l’autorisation `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbDatabases?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>Paramètres URI

| Nom | Dans | Obligatoire | Type | Description |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |string| GUID instanceId d’un compte de base de données restaurable. |
| **location** | path | True | string| Région Azure Cosmos DB, avec des espaces entre les mots et chaque mot en majuscules. |
| **subscriptionId** | path | True | string| ID de l’abonnement cible. |
| **api-version** | query | True | string | Version de l’API à utiliser pour cette opération. |

## <a name="responses"></a>Réponses

| Nom | Type | Description |
| --- | --- | --- |
| 200 OK | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult)| L’opération s’est terminée avec succès. |
| Autres codes d’état | [DefaultErrorResponse](#defaulterrorresponse)| Réponse d’erreur décrivant la raison de l’échec de l’opération.|

## <a name="examples"></a>Exemples

### <a name="cosmosdbrestorablemongodbdatabaselist"></a>CosmosDBRestorableMongodbDatabaseList

**Exemple de requête**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases?api-version=2020-06-01-preview
```

**Exemple de réponse**

Code d’état : 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableMongodbDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Create"
        }
      }
    },
    {
      "id": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Delete"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Définitions

|Définition  | Description| | --- || --- | | [DefaultErrorResponse](#defaulterrorresponse) | Réponse d’erreur du service. | | [ErrorResponse](#errorresponse) | Réponse d’erreur. | | [OperationType](#operationtype) | Enum indiquant le type d’opération de l’événement. | | [Resource](#resource) | Ressource d’un événement de base de données de l’API Azure Cosmos DB pour MongoDB. | | [RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult) | Événement de base de données de l’API Azure Cosmos DB pour MongoDB. | | [RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties) | Propriétés d’un événement de base de données de l’API Azure Cosmos DB pour MongoDB. | | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult) | Réponse de l’opération List, qui contient les événements de base de données de l’API Azure Cosmos DB pour MongoDB et leurs propriétés. |

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
| Créer |string|événement de création de base de données|
| DELETE |string|événement de suppression de base de données|
| Replace |string|événement de modification de base de données|

### <a name="resource"></a><a id="resource"></a>Ressource

Ressource d’un événement de base de données MongoDB pour l’API Azure Cosmos DB

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| _rid |string| Propriété générée par le système. Un identificateur unique. |
| eventTimestamp |string| Heure à laquelle cet événement de base de données s’est produit. |
| operationType |[OperationType](#operationtype)| Type d’opération de cet événement de base de données.  |
| ownerId |string| Nom de la base de données de l’API Azure Cosmos DB pour MongoDB.|
| ownerResourceId |string| ID de ressource de la base de données de l’API Azure Cosmos DB pour MongoDB. |

### <a name="restorablemongodbdatabasegetresult"></a><a id="restorablemongodbdatabasegetresult"></a>RestorableMongodbDatabaseGetResult

Événement de base de données de l’API Azure Cosmos DB pour MongoDB

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| id |string| Identificateur de ressource unique de la ressource Azure Resource Manager. |
| name |string| Nom de la ressource Resource Manager. |
| properties |[RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties)| Propriétés d’un événement de base de données de l’API Azure Cosmos DB pour MongoDB. |
| type |string| Type de ressource Azure. |

### <a name="restorablemongodbdatabaseproperties"></a><a id="restorablemongodbdatabaseproperties"></a>RestorableMongodbDatabaseProperties

Propriétés d’un événement de base de données de l’API Azure Cosmos DB pour MongoDB

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| resource |[Ressource](#resource)| Ressource d’un événement de base de données MongoDB pour l’API Azure Cosmos DB |

### <a name="restorablemongodbdatabaseslistresult"></a><a id="restorablemongodbdatabaseslistresult"></a>RestorableMongodbDatabasesListResult

Réponse de l’opération List, qui contient les événements de base de données et leurs propriétés.

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| value |[RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult)| Liste des événements de base de données et leurs propriétés. |

## <a name="next-steps"></a>Étapes suivantes

* [Répertorier les ressources restaurables](restorable-mongodb-resources-list.md) dans l’API Azure Cosmos DB pour MongoDB à l’aide de l’API REST.
* [Répertorier les collections restaurables](restorable-mongodb-collections-list.md) dans l’API Azure Cosmos DB pour MongoDB à l’aide de l’API REST.
* [Modèle de ressource](continuous-backup-restore-resource-model.md) du mode de sauvegarde continue.