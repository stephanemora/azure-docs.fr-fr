---
title: Utiliser Azure CLI pour configurer la sauvegarde continue et la restauration à un instant dans le passé dans Azure Cosmos DB
description: Découvrez comment approvisionner un compte avec sauvegarde continue et restauration des données à l’aide d’Azure CLI.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9ea71dae746ac423e7b17b6235b4d5cd3e143cd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377327"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-cli"></a>Configurer et gérer la sauvegarde en continu et la restauration à un instant dans le passé (préversion) – Azure CLI
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> La fonctionnalité de restauration à un instant dans le passé (mode de sauvegarde continue) pour Azure Cosmos DB est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La fonctionnalité de restauration à un instant dans le passé d’Azure Cosmos DB (préversion) vous permet de récupérer vos données après une modification accidentelle au sein d’un conteneur, de restaurer un compte, une base de données ou un conteneur supprimé, ou de restaurer dans n’importe quelle région (où des sauvegardes existent). Le mode de sauvegarde continue vous permet d’effectuer une restauration à n’importe quel instant dans le passé au cours des 30 derniers jours.

Cet article explique comment approvisionner un compte avec sauvegarde continue et restauration des données à l’aide d’Azure CLI.

## <a name="install-azure-cli"></a><a id="install"></a>Installer Azure CLI

1. Installez la dernière version d’Azure CLI.

   * Installez la dernière version d’[Azure CLI](/cli/azure/install-azure-cli) ou une version supérieure à 2.17.1.
   * Si vous avez déjà installé l’interface CLI, exécutez la commande `az upgrade` pour effectuer la mise à jour vers la dernière version. Cette commande ne fonctionnera qu’avec une version de CLI supérieure à la version 2.11. Si vous disposez d’une version antérieure, utilisez le lien ci-dessus pour installer la dernière version.

1. Installez l’extension CLI `cosmosdb-preview`.

   * Les commandes de restauration à un instant dans le passé sont disponibles sous l’extension `cosmosdb-preview`.
   * Vous pouvez installer cette extension en exécutant la commande suivante : `az extension add --name cosmosdb-preview`.
   * Vous pouvez désinstaller cette extension en exécutant la commande suivante : `az extension remove --name cosmosdb-preview`.

1. Se connecter et sélectionner votre abonnement

   * Connectez-vous à votre compte Azure à l’aide de la commande `az login`.
   * Sélectionnez l’abonnement requis à l’aide de la commande `az account set -s <subscriptionguid>`.

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>Approvisionner un compte d’API SQL avec sauvegarde continue

Pour approvisionner un compte d’API SQL avec sauvegarde continue, un argument supplémentaire `--backup-policy-type Continuous` doit être transmis avec la commande d’approvisionnement standard. La commande suivante est un exemple de compte d’écriture à région unique nommé `pitracct2` avec une stratégie de sauvegarde continue créée dans la région *USA Ouest* sous le groupe de ressources *myrg* :

```azurecli-interactive

az cosmosdb create \
  --name pitracct2 \
  --resource-group myrg \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-an-azure-cosmos-db-api-for-mongodb-account-with-continuous-backup"></a><a id="provision-mongo-api"></a>Approvisionner un compte d’API Azure Cosmos DB pour MongoDB avec sauvegarde continue

La commande suivante montre un exemple de compte d’écriture à région unique nommé `pitracct3` avec une stratégie de sauvegarde continue créée dans la région *USA Ouest* sous le groupe de ressources *myrg* :

```azurecli-interactive

az cosmosdb create \
  --name pitracct3 \
  --kind MongoDB \
  --resource-group myrg \
  --server-version "3.6" \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore"></a>Déclencher une opération de restauration avec l’interface CLI

La façon la plus simple de déclencher une restauration consiste à lancer la commande de restauration avec le nom du compte cible, le compte source, l’emplacement, le groupe de ressources, le timestamp (UTC) et éventuellement les noms de la base de données et du conteneur. Voici quelques exemples pour déclencher l’opération de restauration :

1. Créez un compte Azure Cosmos DB en le restaurant à partir d’un compte existant.

   ```azurecli-interactive

   az cosmosdb restore \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --resource-group MyResourceGroup \
    --location "West US"

   ```

2. Créez un compte Azure Cosmos DB en restaurant uniquement les bases de données et les conteneurs sélectionnés à partir d’un compte de base de données existant.

   ```azurecli-interactive

   az cosmosdb restore \
    --resource-group MyResourceGroup \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --location "West US" \
    --databases-to-restore name=MyDB1 collections=collection1 collection2 \
    --databases-to-restore name=MyDB2 collections=collection3 collection4

   ```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Énumérer les ressources restaurables pour l’API SQL

Les commandes d’énumération décrites ci-dessous vous aident à découvrir les ressources disponibles pour la restauration à différents moments. En outre, elles fournissent également un flux d’événements clés sur les ressources du compte, des bases de données et des conteneurs restaurables.

**Répertorier tous les comptes qui peuvent être restaurés dans l’abonnement actuel**

Exécutez la commande CLI suivante pour répertorier tous les comptes qui peuvent être restaurés dans l’abonnement actuel.

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "pitrbb"
```

La réponse comprend tous les comptes de base de données (à la fois actifs et supprimés) qui peuvent être restaurés et les régions à partir desquelles ils peuvent être restaurés :

```json
{
    "accountName": "pitrbb",
    "apiType": "Sql",
    "creationTime": "2021-01-08T23:34:11.095870+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865",
    "identity": null,
    "location": "West US",
    "name": "7133a59a-d1c0-4645-a699-6e296d6ac865",
    "restorableLocations": [
      {
        "creationTime": "2021-01-08T23:34:11.095870+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "f02df26b-c0ec-4829-8bef-3482d36e6230"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

À l’instar de l’heure `CreationTime` ou `DeletionTime` pour le compte, il y a également une heure `CreationTime` ou `DeletionTime` pour la région. Ces heures vous permettent de choisir la bonne région et une plage horaire valide pour restaurer dans cette région.

**Répertorier toutes les versions des bases de données dans un compte de base de données actif**

L’énumération de toutes les versions de bases de données vous permet de choisir la base de données appropriée dans un scénario où le moment réel de l’existence de la base de données est inconnu.

Exécutez la commande CLI suivante pour répertorier toutes les versions des bases de données. Cette commande fonctionne uniquement avec des comptes actifs. Les paramètres `instanceId` et `location` sont obtenus à partir des propriétés `name` et `location` dans la réponse de la commande `az cosmosdb restorable-database-account list`. L’attribut instanceId est également une propriété du compte de base de données source en cours de restauration :

```azurecli-interactive
az cosmosdb sql restorable-database list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US"
```

Cette sortie de commande indique désormais quand une base de données a été créée et supprimée.

```json
[
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/40e93dbd-2abe-4356-a31a-35567b777220",
    ..
    "name": "40e93dbd-2abe-4356-a31a-35567b777220",
    "resource": {
      "database": {
        "id": "db1"
      },
      "eventTimestamp": "2021-01-08T23:27:25Z",
      "operationType": "Create",
      "ownerId": "db1",
      "ownerResourceId": "YuZAAA=="
    },
    ..
  },
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/243c38cb-5c41-4931-8cfb-5948881a40ea",
    ..
    "name": "243c38cb-5c41-4931-8cfb-5948881a40ea",
    "resource": {
      "database": {
        "id": "spdb1"
      },
      "eventTimestamp": "2021-01-08T23:25:25Z",
      "operationType": "Create",
      "ownerId": "spdb1",
      "ownerResourceId": "OIQ1AA=="
    },
   ..
  }
]
```

**Répertorier toutes les versions des conteneurs SQL d’une base de données dans un compte de base de données actif**

Utilisez la commande suivante pour répertorier toutes les versions des conteneurs SQL. Cette commande fonctionne uniquement avec des comptes actifs. Le paramètre `databaseRid` est le `ResourceId` de la base de données que vous souhaitez restaurer. Il s’agit de la valeur de l’attribut `ownerResourceid` trouvé dans la réponse de la commande `az cosmosdb sql restorable-database list`.

```azurecli-interactive
az cosmosdb sql restorable-container list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "OIQ1AA==" \
    --location "West US"
```

Cette sortie de commande affiche la liste des opérations effectuées sur tous les conteneurs de cette base de données :

```json
[
  {
    ...
    
      "eventTimestamp": "2021-01-08T23:25:29Z",
      "operationType": "Replace",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
...
  },
  {
    ...
      "eventTimestamp": "2021-01-08T23:25:26Z",
      "operationType": "Create",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
  },
]
```

**Rechercher des bases de données ou des conteneurs qui peuvent être restaurés à une heure donnée**

Utilisez la commande suivante pour récupérer la liste des bases de données ou des conteneurs qui peuvent être restaurés à une heure donnée. Cette commande fonctionne uniquement avec des comptes actifs.

```azurecli-interactive

az cosmosdb sql restorable-resource list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US" \
  --restore-location "West US" \  
  --restore-timestamp "2021-01-10 T01:00:00+0000"

```

```json
[
  {
    "collectionNames": [
      "procol1",
      "procol2"
    ],
    "databaseName": "db1"
  },
  {
    "collectionNames": [
      "procol3",
       "spcol1"
    ],
    "databaseName": "spdb1"
  }
]
```

## <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>Énumérer les ressources restaurables pour le compte d’API MongoDB

Les commandes d’énumération décrites ci-dessous vous aident à découvrir les ressources disponibles pour la restauration à différents moments. En outre, elles fournissent également un flux d’événements clés sur les ressources du compte, des bases de données et des conteneurs restaurables. Comme avec l’API SQL, vous pouvez utiliser la commande `az cosmosdb`, mais avec `mongodb` comme paramètre au lieu de `sql`. Ces commandes fonctionnent uniquement pour les comptes actifs.

**Répertorier toutes les versions des bases de données mongodb dans un compte de base de données actif**

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

**Répertorier toutes les versions des collections mongodb d’une base de données dans un compte de base de données actif**

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "<InstanceID>" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

**Répertorier toutes les ressources d’un compte de base de données mongodb qui peuvent être restaurées à une heure et dans une région données**

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Étapes suivantes

* Configurez et gérez la sauvegarde continue en utilisant [Portail Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md) ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Modèle de ressource du mode de sauvegarde continue](continuous-backup-restore-resource-model.md).
* [Gérez les autorisations](continuous-backup-restore-permissions.md) requises pour restaurer les données en mode de sauvegarde continue.
