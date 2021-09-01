---
title: Modèle de ressource pour la fonctionnalité Azure Cosmos DB de restauration à un instant dans le passé
description: Cet article explique le modèle de ressource pour la fonctionnalité Azure Cosmos DB de restauration à un instant dans le passé. Il explique les paramètres qui prennent en charge la sauvegarde continue et les ressources qui peuvent être restaurées dans l’API Azure Cosmos DB pour les comptes SQL et MongoDB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: e4fffd12b72b41c45b2718e96c34a03e28eeca29
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531862"
---
# <a name="resource-model-for-the-azure-cosmos-db-point-in-time-restore-feature"></a>Modèle de ressource pour la fonctionnalité Azure Cosmos DB de restauration à un instant dans le passé
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Cet article explique le modèle de ressource pour la fonctionnalité Azure Cosmos DB de restauration à un instant dans le passé. Il explique les paramètres qui prennent en charge la sauvegarde continue et les ressources qui peuvent être restaurées dans l’API Azure Cosmos DB pour les comptes SQL et MongoDB.

## <a name="database-accounts-resource-model"></a>Modèle de ressource du compte de base de données

Le modèle de ressource du compte de base de données est mis à jour avec quelques propriétés supplémentaires pour prendre en charge les nouveaux scénarios de restauration. Ces propriétés sont **BackupPolicy, CreateMode et RestoreParameters**.

### <a name="backuppolicy"></a>BackupPolicy

Une nouvelle propriété dans la stratégie de sauvegarde au niveau du compte nommée `Type` sous le paramètre `backuppolicy` active les fonctionnalités de sauvegarde continue et de restauration à un instant dans le passé. Ce mode est appelé « **sauvegarde continue** ». Vous pouvez définir ce mode lors de la création du compte ou lors de la [migration d’un compte du mode périodique vers le mode continu](migrate-continuous-backup.md). Une fois que le mode continu est activé, les fonctionnalités de sauvegarde continue et de restauration à un instant dans le passé seront activées par défaut sur tous les conteneurs et toutes les bases de données créés dans ce compte.

> [!NOTE]
> Actuellement, la fonctionnalité de restauration à un instant dans le passé est disponible pour l’API Azure Cosmos DB pour MongoDB et les comptes SQL. Après avoir créé un compte en mode continu, vous ne pouvez pas le basculer en mode périodique.

### <a name="createmode"></a>CreateMode

Cette propriété indique la façon dont le compte a été créé. Les valeurs possibles sont *Default* et *Restore*. Pour effectuer une restauration, définissez cette valeur sur *Restore* et fournissez les valeurs appropriées dans la propriété `RestoreParameters`.

### <a name="restoreparameters"></a>RestoreParameters

La ressource `RestoreParameters` contient les détails de l’opération de restauration, notamment l’ID de compte, l’heure de restauration et les ressources qui doivent être restaurées.

|Nom de la propriété |Description  |
|---------|---------|
|restoreMode  | Le mode de restauration doit être *PointInTime*. |
|restoreSource   |  L’instanceId du compte source à partir duquel la restauration sera initiée.       |
|restoreTimestampInUtc  | L’instant dans le passé en UTC auquel le compte doit être restauré. |
|databasesToRestore   | Liste d’objets `DatabaseRestoreResource` pour spécifier les bases de données et les conteneurs qui doivent être restaurés. Chaque ressource représente une seule base de données et toutes les collections sous cette base de données, voir la section [Ressources SQL restaurables](#restorable-sql-resources) pour plus de détails. Si cette valeur est vide, le compte entier est restauré.   |

### <a name="sample-resource"></a>Exemple de ressource

Le code JSON suivant est un exemple de ressource de compte de base de données où la sauvegarde continue est activée :

```json
{
  "location": "westus",
  "properties": {
    "databaseAccountOfferType": "Standard",
    "locations": [
      {
        "failoverPriority": 0,
        "locationName": "southcentralus",
        "isZoneRedundant": false
      }
    ],
    "createMode": "Restore",
    "restoreParameters": {
      "restoreMode": "PointInTime",
      "restoreSource": "/subscriptions/subid/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/1a97b4bb-f6a0-430e-ade1-638d781830cc",
      "restoreTimestampInUtc": "2020-06-11T22:05:09Z",
      "databasesToRestore": [
        {
          "databaseName": "db1",
          "collectionNames": [
            "collection1",
            "collection2"
          ]
        },
        {
          "databaseName": "db2",
          "collectionNames": [
            "collection3",
            "collection4"
          ]
        }
      ]
    },
    "backupPolicy": {
      "type": "Continuous"
    }
}
```

## <a name="restorable-resources"></a>Ressources restaurables

Un ensemble de nouvelles ressources et API est disponible pour vous aider à découvrir des informations essentielles sur les ressources qui peuvent être restaurées, les emplacements à partir desquels elles peuvent être restaurées et les timestamps lorsque des opérations clés ont été effectuées sur ces ressources.

> [!NOTE]
> Toutes les API utilisées pour énumérer ces ressources nécessitent les autorisations suivantes :
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read`

### <a name="restorable-database-account"></a>Compte de base de données restaurable

Cette ressource contient une instance de compte de base de données qui peut être restaurée. Le compte de base de données peut être soit un compte supprimé, soit un compte actif. Il contient des informations qui vous permettent de trouver le compte de base de données source que vous souhaitez restaurer.

|Nom de la propriété |Description  |
|---------|---------|
| id | Identificateur unique de la ressource. |
| accountName | Nom du compte de base de données global. |
| creationTime | Heure UTC de la création ou migration du compte.  |
| deletionTime | Heure UTC de la suppression du compte.  Cette valeur est vide si le compte est actif. |
| apiType | Type d’API du compte Azure Cosmos DB. |
| restorableLocations | Liste des emplacements où le compte existait. |
| restorableLocations: locationName | Nom de la région du compte régional. |
| restorableLocations: regionalDatabaseAccountInstanceId | GUID du compte régional. |
| restorableLocations: creationTime | Heure UTC de la création ou migration du compte régional.|
| restorableLocations: deletionTime | Heure UTC de la suppression du compte régional. Cette valeur est vide si le compte régional est actif.|

Pour obtenir la liste de tous les comptes pouvant être restaurés, consultez les articles [Comptes de base de données restaurables – Liste](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-database-accounts/list) ou [Comptes de base de données restaurables – Liste par emplacement](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-database-accounts/list-by-location).

### <a name="restorable-sql-database"></a>Base de données SQL restaurable

Chaque ressource contient des informations sur un événement de mutation, tel que la création et la suppression, qui s’est produit sur la base de données SQL. Ces informations peuvent être utiles dans les cas où la base de données a été supprimée par erreur et si vous avez besoin de savoir à quel moment cet événement s’est produit.

|Nom de la propriété |Description  |
|---------|---------|
| eventTimestamp | Heure UTC de création ou de suppression de la base de données. |
| ownerId | Nom de la base de données SQL. |
| ownerResourceId | ID de ressource de la base de données SQL.|
| operationType | Type d’opération de cet événement de base de données. Les valeurs possibles sont les suivantes :<br/><ul><li>Create : événement de création de la base de données</li><li>Delete : événement de suppression de la base de données</li><li>Replace : événement de modification de la base de données</li><li>SystemOperation : événement de modification de la base de données déclenché par le système. Cet événement n’est pas initié par l’utilisateur</li></ul> |
| database |Propriétés de la base de données SQL au moment de l’événement|

Pour obtenir la liste de toutes les mutations de base de données, consultez l’article [Bases de données SQL restaurables – Liste](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-sql-databases/list).

### <a name="restorable-sql-container"></a>Conteneur SQL restaurable

Chaque ressource contient des informations sur un événement de mutation, tel que la création et la suppression, qui s’est produit sur le conteneur SQL. Ces informations peuvent être utiles dans les cas où le conteneur a été modifié ou supprimé et si vous avez besoin de savoir à quel moment cet événement s’est produit.

|Nom de la propriété |Description  |
|---------|---------|
| eventTimestamp    | Heure UTC à laquelle cet événement de conteneur s’est produit.|
| ownerId| Nom du conteneur SQL.|
| ownerResourceId   | ID de ressource du conteneur SQL.|
| operationType | Type d’opération de cet événement de conteneur. Les valeurs possibles sont les suivantes : <br/><ul><li>Create : événement de création de conteneur</li><li>Delete : événement de suppression de conteneur</li><li>Replace : événement de modification de conteneur</li><li>SystemOperation : événement de modification du conteneur déclenché par le système Cet événement n’est pas initié par l’utilisateur</li></ul> |
| conteneur | Propriétés du conteneur SQL au moment de l’événement.|

Pour obtenir la liste de toutes les mutations de conteneur sous la même base de données, consultez l’article [Conteneurs SQL récupérables– Liste](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-sql-containers/list).

### <a name="restorable-sql-resources"></a>Ressources SQL restaurables

Chaque ressource représente une base de données unique et tous les conteneurs sous cette base de données.

|Nom de la propriété |Description  |
|---------|---------|
| databaseName  | Nom de la base de données SQL.
| collectionNames   | Liste des conteneurs SQL sous cette base de données.|

Pour obtenir la liste de toutes les combinaisons de bases de données et conteneur SQL qui existent sur le compte à l’heure et à l’emplacement donnés, consultez l’article [Ressources SQL récupérables – Liste](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-sql-resources/list).

### <a name="restorable-mongodb-database"></a>Base de données MongoDB restaurable

Chaque ressource contient des informations sur un événement de mutation, tel que la création et la suppression, qui s’est produit sur la base de données MongoDB. Ces informations peuvent être utiles dans le cas où la base de données a été supprimée par erreur et où l’utilisateur doit déterminer à quel moment cet événement s’est produit.

|Nom de la propriété |Description  |
|---------|---------|
|eventTimestamp| Heure UTC à laquelle cet événement de base de données s’est produit.|
| ownerId| Nom de la base de données MongoDB. |
| ownerResourceId   | ID de ressource de la base de données MongoDB. |
| operationType |   Type d’opération de cet événement de base de données. Les valeurs possibles sont les suivantes :<br/><ul><li> Create : événement de création de la base de données</li><li> Delete : événement de suppression de la base de données</li><li> Replace : événement de modification de la base de données</li><li> SystemOperation : événement de modification de la base de données déclenché par le système. Cet événement n’est pas initié par l’utilisateur </li></ul> |

Pour obtenir la liste de toutes les mutations de base de données, consultez l’article [Bases de données MongoDB restaurables – Liste](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-mongodb-databases/list).

### <a name="restorable-mongodb-collection"></a>Collection MongoDB restaurable

Chaque ressource contient des informations sur un événement de mutation, tel que la création et la suppression, qui s’est produit sur la collection MongoDB. Ces informations peuvent être utiles dans les cas où la collection a été modifiée ou supprimée et où l’utilisateur doit déterminer à quel moment cet événement s’est produit.

|Nom de la propriété |Description  |
|---------|---------|
| eventTimestamp |Heure UTC à laquelle cet événement de collection s’est produit. |
| ownerId| Nom de la collection MongoDB. |
| ownerResourceId   | ID de ressource de la collection MongoDB. |
| operationType |Type d’opération de cet événement de collection. Les valeurs possibles sont les suivantes :<br/><ul><li>Create : événement de création de collection</li><li>Delete : événement de suppression de collection</li><li>Replace : événement de modification de collection</li><li>SystemOperation : événement de modification de la collection déclenché par le système. Cet événement n’est pas initié par l’utilisateur</li></ul> |

Pour obtenir la liste de toutes les mutations de conteneur sous la même base de données, consultez l’article [Collections MongoDB récupérables– Liste](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-mongodb-collections/list).

### <a name="restorable-mongodb-resources"></a>Ressources MongoDB restaurables

Chaque ressource représente une base de données unique et toutes les collections sous cette base de données.

|Nom de la propriété |Description  |
|---------|---------|
| databaseName  |Nom de la base de données MongoDB. |
| collectionNames | Liste des collections MongoDB sous cette base de données. |

Pour obtenir la liste de toutes les combinaisons de bases de données et collections MongoDB qui existent sur le compte à l’heure et à l’emplacement donnés, consultez l’article [Ressources MongoDB récupérables – Liste](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-mongodb-resources/list).

## <a name="next-steps"></a>Étapes suivantes

* Approvisionnez la sauvegarde continue à l’aide du [portail Azure](provision-account-continuous-backup.md#provision-portal), de [PowerShell](provision-account-continuous-backup.md#provision-powershell), de l’interface [CLI](provision-account-continuous-backup.md#provision-cli) ou d’[Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template).
* Restaurez un compte à l’aide du [portail Azure](restore-account-continuous-backup.md#restore-account-portal), de [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), de l’interface [CLI](restore-account-continuous-backup.md#restore-account-cli) ou d’[Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template).
* [Migrer vers un compte à partir d’une sauvegarde périodique vers une sauvegarde continue](migrate-continuous-backup.md).
* [Gérez les autorisations](continuous-backup-restore-permissions.md) requises pour restaurer les données en mode de sauvegarde continue.
