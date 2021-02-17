---
title: Répertorier les comptes de base de données pouvant être restaurés par emplacement à l’aide de l’API REST Azure Cosmos DB
description: Répertorie tous les comptes de base de données Azure Cosmos DB restaurables et disponibles sous l’abonnement et dans une région.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 26c07ddaf4db71961d4aeff50e482740d969f0ef
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537676"
---
# <a name="list-restorable-database-accounts-by-location-using-azure-cosmos-db-rest-api"></a>Répertorier les comptes de base de données pouvant être restaurés par emplacement à l’aide de l’API REST Azure Cosmos DB

> [!IMPORTANT]
> La fonctionnalité de restauration à un instant dans le passé (mode de sauvegarde continue) pour Azure Cosmos DB est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Répertorie tous les comptes de base de données Azure Cosmos DB restaurables et disponibles sous l’abonnement et dans une région. Cet appel nécessite l’autorisation `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read`.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts?api-version=2020-06-01-preview

```

## <a name="uri-parameters"></a>Paramètres URI

| Nom | Dans | Obligatoire | Type | Description |
| --- | --- | --- | --- | --- |
| **location** | path | True | string| Région Azure Cosmos DB, avec des espaces entre les mots et chaque mot en majuscules. |
| **subscriptionId** | path | True | string| ID de l’abonnement cible. |
| **api-version** | query | True | string | Version de l’API à utiliser pour cette opération. |

## <a name="responses"></a>Réponses

| Nom | Type | Description |
| --- | --- | --- |
| 200 OK | [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult)| L’opération s’est terminée avec succès. |
| Autres codes d’état | [DefaultErrorResponse](#defaulterrorresponse)| Réponse d’erreur décrivant la raison de l’échec de l’opération. |

## <a name="examples"></a>Exemples

### <a name="cosmosdbdatabaseaccountlist"></a>CosmosDBDatabaseAccountList

**Exemple de requête**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

**Exemple de réponse**

Code d’état : 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "name": "d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "location": "West US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb1",
        "creationTime": "2020-04-11T21:56:15Z",
        "deletionTime": "2020-06-12T22:05:09Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "name": "4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "location": "West US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb2",
        "creationTime": "2020-05-01T08:05:18Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    }
  ]
}
```

## <a name="definitions"></a>Définitions

|Définition  | Description|
| --- | --- |
| [ApiType](#apitype) | Enum indiquant le type d’API du compte de base de données pouvant être restauré. |
| [DefaultErrorResponse](#defaulterrorresponse) | Réponse d’erreur du service. |
| [ErrorResponse](#errorresponse) | Réponse d’erreur. |
| [RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult) | Compte de base de données Azure Cosmos DB restaurable. |
| [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult) | Réponse de l’opération List, qui contient les comptes de base de données pouvant être restaurés et leurs propriétés. |
| [RestorableLocationResource](#restorablelocationresource) | Propriétés du compte régional restaurable. |

### <a name="apitype"></a><a id="apitype"></a>ApiType

Enum indiquant le type d’API du compte de base de données pouvant être restauré.

| **Nom** | **Type** |
| --- | --- |
| Cassandra |string|
| Gremlin |string|
| GremlinV2 |string|
| MongoDB |string|
| SQL |string|
| Table de charge de travail |string|

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

### <a name="restorabledatabaseaccountgetresult"></a><a id="restorabledatabaseaccountgetresult"></a>RestorableDatabaseAccountGetResult

Compte de base de données Azure Cosmos DB restaurable.

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| id |string| Identificateur de ressource unique de la ressource Azure Resource Manager. |
| location |string| Emplacement du groupe de ressources auquel appartient la ressource. |
| name |string| Nom de la ressource Resource Manager. |
| properties.accountName |string| Nom du compte de base de données global. |
| properties.apiType |[ApiType](#apitype)| Type d’API du compte de base de données pouvant être restauré. |
| properties.creationTime |string| Heure de création du compte de base de données pouvant être restauré (format ISO-8601). |
| properties.deletionTime |string| Heure à laquelle le compte de base de données pouvant être restauré a été supprimé (format ISO-8601). |
| properties.restorableLocations |[RestorableLocationResource](#restorablelocationresource)| Liste des régions à partir desquelles le compte de base de données peut être restauré. |
| type |string| Type de ressource Azure. |

### <a name="restorabledatabaseaccountslistresult"></a><a id="restorabledatabaseaccountslistresult"></a>RestorableDatabaseAccountsListResult

Réponse de l’opération List, qui contient les comptes de base de données pouvant être restaurés et leurs propriétés.

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| value |[RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult)| Liste des comptes de base de données pouvant être restaurés et de leurs propriétés. |

### <a name="restorablelocationresource"></a><a id="restorablelocationresource"></a>RestorableLocationResource

Propriétés du compte régional restaurable.

| **Nom** | **Type** | **Description** |
| --- | --- | --- |
| creationTime |string| Heure de création du compte de base de données régional pouvant être restauré (format ISO-8601). |
| deletionTime |string| Heure à laquelle le compte de base de données régional pouvant être restauré a été supprimé (format ISO-8601). |
| locationName |string| Emplacement du compte régional pouvant être restauré. |
| regionalDatabaseAccountInstanceId |string| ID d’instance du compte régional restaurable. |

## <a name="next-steps"></a>Étapes suivantes

* [Répertorier les collections restaurables](restorable-database-accounts-list-by-location.md) dans l’API Azure Cosmos DB pour MongoDB à l’aide de l’API REST.
* [Modèle de ressource](continuous-backup-restore-resource-model.md) du mode de sauvegarde continue.