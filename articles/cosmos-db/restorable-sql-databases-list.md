---
title: Répertorier les bases de données d’API SQL restaurables dans Azure Cosmos DB à l’aide de l’API REST
description: Affichez le flux d’événements de toutes les mutations effectuées sur toutes les bases de données SQL Azure Cosmos DB sous le compte pouvant être restauré. Cela vous aide à récupérer l’heure de la suppression dans le cas où la base de données a été supprimée accidentellement.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 218a39a2bf8e9269e43c4876c1654d94be886997
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539499"
---
# <a name="list-restorable-sql-api-databases-in-azure-cosmos-db-using-rest-api"></a>Répertorier les bases de données d’API SQL restaurables dans Azure Cosmos DB à l’aide de l’API REST

> [!IMPORTANT]
> La fonctionnalité de restauration à un instant dans le passé (mode de sauvegarde continue) pour Azure Cosmos DB est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Affichez le flux d’événements de toutes les mutations effectuées sur toutes les bases de données SQL Azure Cosmos DB sous le compte pouvant être restauré. Cela vous aide à récupérer l’heure de la suppression dans le cas où la base de données a été supprimée accidentellement. Cette API requiert l’autorisation `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlDatabases?api-version=2020-06-01-preview
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
| 200 OK | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult)| L’opération s’est terminée avec succès. |
| Autres codes d’état | [DefaultErrorResponse](#defaulterrorresponse)| Réponse d’erreur décrivant la raison de l’échec de l’opération. |

## <a name="examples"></a>Exemples

### <a name="cosmosdbrestorablesqldatabaselist"></a>CosmosDBRestorableSqlDatabaseList

**Exemple de demande**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases?api-version=2020-06-01-preview
```

**Exemple de réponse**

Code d’état : 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableSqlDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "operationType": "Create",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/"
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/",
            "_ts": 1599075903
          },
          "operationType": "Delete"
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "properties": {
        "resource": {
          "_rid": "aXFqUQAAAA==",
          "eventTimestamp": "2020-09-02T19:53:15Z",
          "ownerId": "Database2",
          "ownerResourceId": "0SziSg==",
          "database": {
            "id": "Database2",
            "_rid": "0SziSg==",
            "_self": "dbs/0SziSg==/",
            "_etag": "\"0000ca0a-0000-0700-0000-5f4ff82b0000\"",
            "_colls": "colls/",
            "_users": "users/"
          },
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Définitions

|Définition  | Description| | --- || --- | | [Database](#database) | Objet de ressource de base de données SQL Azure Cosmos DB. | | [DefaultErrorResponse](#defaulterrorresponse) | Réponse d’erreur du service. | | [ErrorResponse](#errorresponse) | Réponse d’erreur. | | [OperationType](#operationtype) | Enum indiquant le type d’opération de l’événement. | | [Resource](#resource) | Ressource d’un événement de base de données SQL Azure Cosmos DB. | | [RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult) | Événement de base de données SQL Azure Cosmos DB. | | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties) | Propriétés d’un événement de base de données SQL Azure Cosmos DB. | | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult) | Réponse de l’opération List, qui contient les événements de base de données SQL et leurs propriétés. |

### <a name="database"></a><a id="database"></a>Base de données

Objet de ressource de base de données SQL Azure Cosmos DB

| **Nom** | **Type** | **Description** | | --- || --- | ---| | _colls |chaîne| Propriété générée par le système qui indique le chemin d’accès adressable de la ressource de collection. | | _etag |string| Propriété générée par le système qui représente l’etag de ressource requis pour le contrôle d’accès concurrentiel optimiste. | | _rid |string| Propriété générée par le système. Un identificateur unique. | | _self |string| Propriété générée par le système qui indique le chemin d’accès adressable de la ressource de base de données. | | _ts | | Propriété générée par le système qui indique le timestamp de la dernière mise à jour de la ressource. | | _users |string| Propriété générée par le système qui indique le chemin d’accès adressable de la ressource de l’utilisateur. | | ID |string| Nom de la base de données SQL Azure Cosmos DB. |

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
| SystemOperation |string|événement de modification de la base de données déclenché par le système. Cet événement n’est pas initié par l’utilisateur|

### <a name="resource"></a><a id="resource"></a>Ressource

Ressource d’un événement de base de données SQL Azure Cosmos DB.

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| _rid |string| Propriété générée par le système. Un identificateur unique. |
| database |[Sauvegarde de la base de données](#database)| Objet de ressource de base de données SQL Azure Cosmos DB |
| eventTimestamp |string| Heure à laquelle cet événement de base de données s’est produit. |
| operationType |[OperationType](#operationtype)| Type d’opération de cet événement de base de données. |
| ownerId |string| Nom de la base de données SQL. |
| ownerResourceId |string| ID de ressource de la base de données SQL. |

### <a name="restorablesqldatabasegetresult"></a><a id="restorablesqldatabasegetresult"></a>RestorableSqlDatabaseGetResult

Événement de base de données SQL Azure Cosmos DB.

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| id |string| Identificateur de ressource unique de la ressource Azure Resource Manager. |
| name |string| Nom de la ressource Azure Resource Manager. |
| properties | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties)| Propriétés d’un événement de base de données SQL. |
| type |string| Type de ressource Azure. |

### <a name="restorablesqldatabaseproperties"></a><a id="restorablesqldatabaseproperties"></a>RestorableSqlDatabaseProperties

Propriétés d’un événement de base de données SQL Azure Cosmos DB.

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| resource |[Ressource](#resource)| Ressource d’un événement de base de données SQL Azure Cosmos DB. |

### <a name="restorablesqldatabaseslistresult"></a><a id="restorablesqldatabaseslistresult"></a>RestorableSqlDatabasesListResult

Réponse de l’opération List, qui contient les événements de base de données SQL et leurs propriétés.

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| value |[RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult)| Liste des événements de base de données SQL et leurs propriétés. |

## <a name="next-steps"></a>Étapes suivantes

* [Répertorier les conteneurs restaurables](restorable-sql-containers-list.md) dans l’API SQL Azure Cosmos DB à l’aide de l’API REST.
* [Modèle de ressource](continuous-backup-restore-resource-model.md) du mode de sauvegarde continue.