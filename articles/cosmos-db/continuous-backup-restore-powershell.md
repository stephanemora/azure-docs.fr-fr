---
title: Utilisez Azure PowerShell pour configurer la sauvegarde continue et la restauration à un instant dans le passé dans Azure Cosmos DB.
description: Découvrez comment approvisionner un compte avec sauvegarde continue et restauration des données à l’aide d’Azure PowerShell.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 5261075a82eaefd91cbedd2dd2fe08cb1e0a20b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381832"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-powershell"></a>Configurer et gérer la sauvegarde continue et la restauration à un instant dans le passé (préversion) à l’aide d’Azure PowerShell
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> La fonctionnalité de restauration à un instant dans le passé (mode de sauvegarde continue) pour Azure Cosmos DB est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La fonctionnalité de restauration à un instant dans le passé d’Azure Cosmos DB (préversion) vous permet de récupérer vos données après une modification accidentelle au sein d’un conteneur, de restaurer un compte, une base de données ou un conteneur supprimé, ou de restaurer dans n’importe quelle région (où des sauvegardes existent). Le mode de sauvegarde continue vous permet d’effectuer une restauration à n’importe quel instant dans le passé au cours des 30 derniers jours.

Cet article explique comment approvisionner un compte avec sauvegarde continue et restauration des données à l’aide d’Azure PowerShell.

## <a name="install-azure-powershell"></a><a id="install-powershell"></a>Installation d’Azure PowerShell

1. Exécutez la commande suivante à partir d’Azure PowerShell pour installer le module `Az.CosmosDB` en préversion, qui contient les commandes relatives à la restauration à un instant dans le passé :

   ```azurepowershell
   Install-Module -Name Az.CosmosDB -AllowPrerelease
   ```

1. Après avoir installé les modules, connectez-vous à Azure à l’aide de

   ```azurepowershell
   Connect-AzAccount
   ```

1. Sélectionnez un abonnement spécifique avec la commande suivante :

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>Approvisionner un compte d’API SQL avec sauvegarde continue

Pour approvisionner un compte avec sauvegarde continue, ajoutez un argument `-BackupPolicyType Continuous` à la commande d’approvisionnement standard.

L’applet de commande suivante est un exemple de compte d’écriture monorégion nommé `pitracct2` avec une stratégie de sauvegarde continue créée dans la région *USA Ouest* sous le groupe de ressources *myrg* :

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct2" `
  -ApiKind "Sql"
      
```

## <a name="provision-a-mongodb-api-account-with-continuous-backup"></a><a id="provision-mongodb-api"></a>Approvisionner un compte d’API MongoDB avec sauvegarde continue

L’applet de commande suivante est un exemple de compte de sauvegarde continue *pitracct2* créé dans la région *USA Ouest* sous le groupe de ressources *myrg* :

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct3" `
  -ApiKind "MongoDB" `
  -ServerVersion "3.6"

```

## <a name="trigger-a-restore-operation"></a><a id="trigger-restore"></a>Déclencher une opération de restauration

L’applet de commande suivante est un exemple qui permet de déclencher une opération de restauration avec la commande Restore à l’aide du compte cible, du compte source, de l’emplacement, du groupe de ressources et du timestamp :

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName <resourceGroupName> `
  -TargetDatabaseAccountName <restored-account-name> `
  -SourceDatabaseAccountName <sourceDatabaseAccountName> `
  -RestoreTimestampInUtc <UTC time> `
  -Location <Azure Region Name>

```

**Exemple 1 :** Restauration de l'intégralité du compte :

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**Exemple 2 :** Restauration de collections et de bases de données spécifiques. Cet exemple restaure les collections myCol1, myCol2 à partir de myDB1 et la totalité de la base de données myDB2, qui comprend tous les conteneurs sous celui-ci.

```azurepowershell
$datatabaseToRestore1 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB1" -CollectionName "myCol1", "myCol2"
$datatabaseToRestore2 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB2"

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -DatabasesToRestore $datatabaseToRestore1, $datatabaseToRestore2 `
  -Location "West US"

```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Énumérer les ressources restaurables pour l’API SQL

Les cmdlets d’énumération vous aident à découvrir les ressources disponibles pour la restauration à différents moments. En outre, elles fournissent également un flux d’événements clés sur les ressources du compte, des bases de données et des conteneurs restaurables.

**Répertorier tous les comptes qui peuvent être restaurés dans l’abonnement actuel**

Exécutez la commande PowerShell `Get-AzCosmosDBRestorableDatabaseAccount` pour répertorier tous les comptes qui peuvent être restaurés dans l’abonnement actuel.

La réponse comprend tous les comptes de base de données (à la fois actifs et supprimés) qui peuvent être restaurés et les régions à partir desquelles ils peuvent être restaurés.

```console
{
    "accountName": "sampleaccount",
    "apiType": "Sql",
    "creationTime": "2020-08-08T01:04:52.070190+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995",
    "identity": null,
    "location": "West US",
    "name": "23e99a35-cd36-4df4-9614-f767a03b9995",
    "restorableLocations": [
      {
        "creationTime": "2020-08-08T01:04:52.945185+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "30701557-ecf8-43ce-8810-2c8be01dccf9"
      },
      {
        "creationTime": "2020-08-08T01:15:43.507795+00:00",
        "deletionTime": null,
        "locationName": "East US",
        "regionalDatabaseAccountInstanceId": "8283b088-b67d-4975-bfbe-0705e3e7a599"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  },
```

À l’instar de l’heure `CreationTime` ou `DeletionTime` pour le compte, il y a également une heure `CreationTime` ou `DeletionTime` pour la région. Ces heures vous permettent de choisir la bonne région et une plage horaire valide pour restaurer dans cette région.

**Répertorier toutes les versions des bases de données SQL dans un compte de base de données actif**

La liste de toutes les versions de bases de données vous permet de choisir la base de données appropriée dans un scénario où le moment réel de l'existence de la base de données est inconnu.

Exécutez la commande PowerShell suivante pour répertorier toutes les versions des bases de données. Cette commande fonctionne uniquement avec des comptes actifs. Les paramètres `DatabaseAccountInstanceId` et `LocationName` sont obtenus à partir des propriétés `name` et `location` dans la réponse de l’applet de commande `Get-AzCosmosDBRestorableDatabaseAccount`. L’attribut `DatabaseAccountInstanceId` fait référence à la propriété `instanceId` du compte de base de données source en cours de restauration :


```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -LocationName "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

**Répertorier toutes les versions des conteneurs SQL d’une base de données dans un compte de base de données actif.**

Utilisez la commande suivante pour répertorier toutes les versions des conteneurs SQL. Cette commande fonctionne uniquement avec des comptes actifs. Le paramètre `DatabaseRid` est le `ResourceId` de la base de données que vous souhaitez restaurer. Il s’agit de la valeur de l’attribut `ownerResourceid` trouvé dans la réponse de l’applet de commande `Get-AzCosmosdbSqlRestorableDatabase`. La réponse inclut également la liste des opérations effectuées sur tous les conteneurs de cette base de données.

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"

```

**Rechercher des bases de données ou des conteneurs qui peuvent être restaurés à une heure donnée**

Utilisez la commande suivante pour récupérer la liste des bases de données ou des conteneurs qui peuvent être restaurés à une heure donnée. Cette commande fonctionne uniquement avec des comptes actifs.

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

## <a name="enumerate-restorable-resources-for-mongodb"></a><a id="enumerate-mongodb-api"></a>Énumérer les ressources restaurables pour MongoDB

Les commandes d’énumération décrites ci-dessous vous aident à découvrir les ressources disponibles pour la restauration à différents moments. En outre, elles fournissent également un flux d’événements clés sur les ressources du compte, des bases de données et des conteneurs restaurables. Ces commandes ne fonctionnent que pour les comptes actifs et sont similaires aux commandes de l’API SQL, mais avec `MongoDB` dans le nom de la commande au lieu de `sql`.

**Répertorier toutes les versions des bases de données MongoDB dans un compte de base de données actif**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableDatabase `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US"

```

**Répertorier toutes les versions des collections mongodb d’une base de données dans un compte de base de données actif**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableCollection `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"
```

**Répertorier toutes les ressources d’un compte de base de données mongodb qui peuvent être restaurées à une heure et dans une région données**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Étapes suivantes

* Configurez et gérez la sauvegarde continue en utilisant [Azure CLI](continuous-backup-restore-command-line.md), [Resource Manager](continuous-backup-restore-template.md) ou le [portail Azure](continuous-backup-restore-portal.md).
* [Modèle de ressource de mode de sauvegarde continue](continuous-backup-restore-resource-model.md)
* [Gérez les autorisations](continuous-backup-restore-permissions.md) requises pour restaurer les données en mode de sauvegarde continue.
