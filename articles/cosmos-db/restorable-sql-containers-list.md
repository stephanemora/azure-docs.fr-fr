---
title: Répertorier les conteneurs d’API SQL restaurables dans Azure Cosmos DB à l’aide de l’API REST
description: Affichez le flux d’événements de toutes les mutations effectuées sur tous les conteneurs SQL Azure Cosmos DB sous une base de données spécifique. Cela aide dans le cas où un conteneur a été accidentellement supprimé.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: bba00b10d1a24dc29c1e986de1f0144c00350f5d
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537422"
---
# <a name="list-restorable-sql-api-containers-in-azure-cosmos-db-using-rest-api"></a>Répertorier les conteneurs d’API SQL restaurables dans Azure Cosmos DB à l’aide de l’API REST

> [!IMPORTANT]
> La fonctionnalité de restauration à un instant dans le passé (mode de sauvegarde continue) pour Azure Cosmos DB est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Affichez le flux d’événements de toutes les mutations effectuées sur tous les conteneurs SQL Azure Cosmos DB sous une base de données spécifique. Cela aide dans le cas où un conteneur a été accidentellement supprimé. Cette API requiert l’autorisation `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview
```

Avec des paramètres facultatifs :

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid={restorableSqlDatabaseRid}
```

## <a name="uri-parameters"></a>Paramètres URI

| Nom | Dans | Obligatoire | Type | Description |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |string| GUID instanceId d’un compte de base de données restaurable. |
| **location** | path | True | string| Région Azure Cosmos DB, avec des espaces entre les mots et chaque mot en majuscules. |
| **subscriptionId** | path | True | string| ID de l’abonnement cible. |
| **api-version** | query | True | string | Version de l’API à utiliser pour cette opération. |
| **restorableSqlDatabaseRid** | query | |string| ID de ressource de la base de données SQL. |

## <a name="responses"></a>Réponses

| Nom | Type | Description |
| --- | --- | --- |
| 200 OK | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult)| L’opération s’est terminée avec succès. |
| Autres codes d’état | [DefaultErrorResponse](#defaulterrorresponse)| Réponse d’erreur décrivant la raison de l’échec de l’opération. |

## <a name="examples"></a>Exemples

### <a name="cosmosdbrestorablesqlcontainerlist"></a>CosmosDBRestorableSqlContainerList

**Exemple de requête**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid=3fu-hg==
```

**Exemple de réponse**

Code d’état : 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00003e00-0000-0700-0000-5f85338a0000\""
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/e85298a1-c631-4726-825e-a7ca092e9098",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "e85298a1-c631-4726-825e-a7ca092e9098",
      "properties": {
        "resource": {
          "_rid": "PrArcgAAAA==",
          "eventTimestamp": "2020-10-13T05:03:27Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Replace",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "defaultTtl": 12345,
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00004400-0000-0700-0000-5f85351f0000\""
          }
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Définitions

|Définition  | Description| | --- || --- | | [Container](#container) | Objet de ressource de conteneur SQL Azure Cosmos DB. | | [DefaultErrorResponse](#defaulterrorresponse) | Réponse d’erreur du service. | | [ErrorResponse](#errorresponse) | Réponse d’erreur. | | [OperationType](#operationtype) | Enum indiquant le type d’opération de l’événement. | | [Resource](#resource) | Ressource d’un événement de conteneur SQL Azure Cosmos DB. | | [RestorableSqlContainerGetResult](#restorablesqlcontainergetresult) | Événement de conteneur SQL Azure Cosmos DB. | | [RestorableSqlContainerProperties](#restorablesqlcontainerproperties) | Propriétés d’un événement de conteneur SQL Azure Cosmos DB. | | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult) | Réponse de l’opération List, qui contient les événements de conteneur SQL et leurs propriétés. |

### <a name="container"></a><a id="container"></a>Container

Objet de ressource de conteneur SQL Azure Cosmos DB

| **Nom** | **Type** | **Description** | | --- || --- | ---| | _etag |string| Propriété générée par le système qui représente l’etag de ressource requis pour le contrôle d’accès concurrentiel optimiste. | | _rid |string| Propriété générée par le système. Un identificateur unique. | | _self |string| Propriété générée par le système qui indique le chemin d’accès adressable de la ressource de conteneur. | | _ts | | Propriété générée par le système qui indique le timestamp de la dernière mise à jour de la ressource. | | ID |string| Nom du conteneur SQL Azure Cosmos DB. |

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
| Créer |string|événement de création de conteneur|
| DELETE |string|événement de suppression de conteneur|
| Replace |string|événement de modification de conteneur|
| SystemOperation |string|événement de modification du conteneur déclenché par le système. Cet événement n’est pas initié par l’utilisateur|

### <a name="resource"></a><a id="resource"></a>Ressource

Ressource d’un événement de conteneur SQL Azure Cosmos DB

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| _rid |string| Propriété générée par le système. Un identificateur unique. |
| conteneur |[Conteneur](#container)| Objet de ressource de conteneur SQL Azure Cosmos DB |
| eventTimestamp |string| Heure à laquelle cet événement de conteneur s’est produit. |
| operationType |[OperationType](#operationtype)| Type d’opération de cet événement de conteneur. |
| ownerId |string| Nom du conteneur SQL. |
| ownerResourceId |string| ID de ressource du conteneur SQL. |

### <a name="restorablesqlcontainergetresult"></a><a id="restorablesqlcontainergetresult"></a>RestorableSqlContainerGetResult

Événement de conteneur SQL Azure Cosmos DB

| **Nom** | **Type** | **Description** |
| --- | --- | ---
| id |string| Identificateur de ressource unique de la ressource Azure Resource Manager. |
| name |string| Nom de la ressource Azure Resource Manager. |
| properties |[RestorableSqlContainerProperties](#restorablesqlcontainerproperties)| Propriétés d’un événement de conteneur SQL. |
| type |string| Type de ressource Azure. |

### <a name="restorablesqlcontainerproperties"></a><a id="restorablesqlcontainerproperties"></a>RestorableSqlContainerProperties

Propriétés d’un événement de conteneur SQL Azure Cosmos DB.

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| resource |[Ressource](#resource)| Ressource d’un événement de conteneur SQL Azure Cosmos DB |

### <a name="restorablesqlcontainerslistresult"></a><a id="restorablesqlcontainerslistresult"></a>RestorableSqlContainersListResult

Réponse de l’opération List, qui contient les événements de conteneur SQL et leurs propriétés.

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| value |[RestorableSqlContainerGetResult](#restorablesqlcontainergetresult)| Liste des événements de conteneur SQL et leurs propriétés. |

## <a name="next-steps"></a>Étapes suivantes

* [Répertorier les bases de données restaurables](restorable-mongodb-databases-list.md) dans l’API Azure Cosmos DB pour MongoDB à l’aide de l’API REST.
* [Modèle de ressource](continuous-backup-restore-resource-model.md) du mode de sauvegarde continue.