---
title: Inventaire des objets blob du Stockage Azure
description: L’inventaire du Stockage Azure est un outil qui permet d’obtenir une vue d’ensemble de toutes les données blob d’un compte de stockage.
services: storage
author: normesta
ms.service: storage
ms.date: 08/16/2021
ms.topic: conceptual
ms.author: normesta
ms.reviewer: klaasl
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 6962688a574d7f7c11f8cbfc71ccdb29ac3b6445
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619385"
---
# <a name="azure-storage-blob-inventory"></a>Inventaire des objets blob du Stockage Azure

La fonctionnalité d’inventaire des blobs de Stockage Azure offre une vue d’ensemble de vos conteneurs, blobs, instantanés et versions de blobs au sein d’un compte de stockage. Utilisez le rapport d’inventaire pour comprendre divers attributs des blobs et des conteneurs, tels que la taille totale de vos données, leur ancienneté, l’état du chiffrement, la stratégie d’immuabilité et la conservation légale, etc. Il vous fournit une synthèse de vos données pour les besoins de l’entreprise et ses exigences de conformité.

## <a name="inventory-features"></a>Fonctionnalités de l’inventaire

La liste suivante décrit les fonctionnalités et capacités disponibles dans la version actuelle de l’inventaire de blobs de Stockage Azure.

- **Rapports d’inventaire pour les blobs et les conteneurs**

  Vous pouvez générer des rapports d’inventaire pour les blobs et les conteneurs. Un rapport sur les blobs peut contenir les blobs de base, les instantanés, les versions de blobs et leurs propriétés associées telles que l’heure de création ou l’heure de dernière modification. Un rapport sur les conteneurs décrit les conteneurs et leurs propriétés associées telles que l’état de la stratégie d’immuabilité ou l’état de conservation légale.

- **Schéma personnalisé**

  Vous pouvez choisir les champs à afficher dans les rapports. Faites votre choix parmi une liste de champs pris en charge. Cette liste apparaît plus loin dans cet article.

- **Format de sortie CSV et Apache Parquet**

  Vous pouvez générer un rapport d’inventaire au format de sortie CSV ou Apache Parquet.

- **Fichier manifeste et événement Azure Event Grid par rapport d’inventaire**

  Un fichier manifeste et un événement Azure Event Grid sont générés par rapport d’inventaire. Ils sont décrits plus loin dans cet article.

## <a name="enabling-inventory-reports"></a>Activation des rapports d’inventaire

Activez les rapports d’inventaire des blobs en ajoutant une stratégie comprenant une ou plusieurs règles à votre compte de stockage. Pour obtenir une aide, consultez [Activer les rapports d’inventaire des blobs de Stockage Azure](blob-inventory-how-to.md).

## <a name="upgrading-an-inventory-policy"></a>Mise à niveau d’une stratégie d’inventaire

Si vous êtes un utilisateur actuel de l’inventaire de blobs Stockage Azure qui a configuré son inventaire avant juin 2021, vous pouvez commencer à utiliser les nouvelles fonctionnalités en chargeant la stratégie, puis en réenregistrant la stratégie après avoir apporté des modifications. Lorsque vous rechargez la stratégie, les nouveaux champs de la stratégie sont renseignés avec les valeurs par défaut. Vous pouvez modifier ces valeurs si vous le souhaitez. En outre, les deux fonctionnalités suivantes seront disponibles.

- Un conteneur de destination est désormais pris en charge pour chaque règle au lieu d’être simplement pris en charge pour la stratégie.

- Un fichier manifeste et un événement Azure Event Grid sont désormais générés par règle et non plus par stratégie.

## <a name="inventory-policy"></a>Stratégie d’inventaire

Une stratégie d’inventaire est un ensemble de règles dans un document JSON.

```json
{
  "enabled": true,
  "rules": [
  {
    "enabled": true,
    "name": "inventoryrule1",
    "destination": "inventory-destination-container",
    "definition": {. . .}
  },
  {
    "enabled": true,
    "name": "inventoryrule2",
    "destination": "inventory-destination-container",
    "definition": {. . .}
  }]
}
```

Pour afficher le code JSON d’une stratégie d’inventaire, sélectionnez l’onglet **Mode Code** dans la section **Inventaire des objets blob** du Portail Azure.

| Nom du paramètre | Type de paramètre | Notes | Requis ? |
|--|--|--|--|
| enabled | boolean | Paramètre permettant de désactiver l’intégralité de la stratégie. Lorsqu’il a la valeur **true**, le champ de niveau règle activé remplace ce paramètre. Quand il est désactivé, l’inventaire est désactivé pour toutes les règles. | Yes |
| rules | Tableau d’objets de règle | Une stratégie requiert au moins une règle. Jusqu’à 100 règles sont prises en charge par stratégie. | Yes |

## <a name="inventory-rules"></a>Règles d’inventaire

Une règle capture les conditions de filtrage et les paramètres de sortie associés à la génération d’un rapport d’inventaire. Chaque règle crée un rapport d’inventaire. Les règles peuvent comporter des préfixes qui se chevauchent. Il peut arriver qu’un objet blob apparaisse dans plusieurs inventaires en fonction des définitions de règles.

Chaque règle au sein de la stratégie a plusieurs paramètres :

| Nom du paramètre | Type de paramètre | Notes | Requis ? |
|--|--|--|--|
| name | string | Un nom de règle peut comporter jusqu’à 256 caractères alphanumériques sensibles à la casse. Il doit être unique dans une stratégie. | Yes |
| enabled | boolean | Indicateur permettant l’activation et la désactivation d’une règle. La valeur par défaut est **true**. | Yes |
| Définition | Définition de règle d’inventaire JSON | Chaque définition se compose d’un ensemble de filtres de règle. | Yes |
| destination | string | Conteneur de destination dans lequel tous les fichiers d’inventaire seront générés. Il doit déjà exister. |

L’indicateur global **Inventaire des objets blob activé** est prioritaire sur le paramètre *activé* dans une règle.

### <a name="rule-definition"></a>Définition de la règle

| Nom du paramètre | Type de paramètre | Notes | Obligatoire |
|--|--|--|--|
| filtres | json | Les filtres déterminent si un blob ou un conteneur fait partie de l’inventaire ou non. | Yes |
| format | string | Détermine la sortie du fichier d’inventaire. Les valeurs valides sont `csv` (pour le format CSV) et `parquet` (pour le format Apache Parquet).| Yes |
| objectType | string | Indique s’il s’agit d’une règle d’inventaire pour les blobs ou les conteneurs. Les valeurs valides sont `blob` et `container`. |Yes |
| schedule | string | Calendrier d’exécution de cette règle. Les valeurs valides sont `daily` et `weekly`. | Yes |
| schemaFields | Tableau JSON | Liste des champs de schéma devant faire partie de l’inventaire. | Yes |

### <a name="rule-filters"></a>Filtres de règle

Plusieurs filtres sont disponibles pour personnaliser un rapport d’inventaire d’objets blob :

| Nom du filtre | Type de filtre | Notes | Requis ? |
|--|--|--|--|
| blobTypes | Tableau de valeurs enum prédéfinies | Les valeurs valides sont `blockBlob` et `appendBlob` pour les comptes avec espace de noms hiérarchique, et `blockBlob`, `appendBlob` et `pageBlob` pour les autres. Ce champ n’est pas applicable à l’inventaire d’un conteneur (objectType : `container`). | Oui |
| prefixMatch | Tableau pouvant comporter jusqu’à 10 chaînes pour les préfixes à mettre en correspondance | Si vous ne définissez pas *prefixMatch* ou que vous indiquez un préfixe vide, la règle s’applique à tous les objets blob du compte de stockage. Un préfixe doit être un préfixe de nom de conteneur ou un nom de conteneur. Par exemple, `container`, `container1/foo`. | No |
| includeSnapshots | boolean | Spécifie si l’inventaire doit inclure les instantanés. La valeur par défaut est `false`. Ce champ n’est pas applicable à l’inventaire d’un conteneur (objectType : `container`). | No |
| includeBlobVersions | boolean | Spécifie si l’inventaire doit inclure les versions d’objets blob. La valeur par défaut est `false`. Ce champ n’est pas applicable à l’inventaire d’un conteneur (objectType : `container`). | No |

Pour afficher le code JSON de règles d’inventaire, sélectionnez l’onglet **Mode Code** dans la section **Inventaire des objets blob** du Portail Azure. Les filtres sont spécifiés au sein d’une définition de règle.

```json
{
  "destination": "inventory-destination-container",
  "enabled": true,
  "rules": [
  {
    "definition": {
      "filters": {
        "blobTypes": ["blockBlob", "appendBlob", "pageBlob"],
        "prefixMatch": ["inventorytestcontainer1", "inventorytestcontainer2/abcd", "etc"],
        "includeSnapshots": false,
        "includeBlobVersions": true,
      },
      "format": "csv",
      "objectType": "blob",
      "schedule": "daily",
      "schemaFields": ["Name", "Creation-Time"]
    },
    "enabled": true,
    "name": "blobinventorytest",
    "destination": "inventorydestinationContainer"
  },
  {
    "definition": {
      "filters": {
        "prefixMatch": ["inventorytestcontainer1", "inventorytestcontainer2/abcd", "etc"]
      },
      "format": "csv",
      "objectType": "container",
      "schedule": "weekly",
      "schemaFields": ["Name", "HasImmutabilityPolicy", "HasLegalHold"]
    },
    "enabled": true,
    "name": "containerinventorytest",
    "destination": "inventorydestinationContainer"
    }
  ]
}
```

### <a name="custom-schema-fields-supported-for-blob-inventory"></a>Champs de schéma personnalisés pris en charge pour l’inventaire des blobs

- Name (obligatoire)
- Creation-Time
- Last-Modified
- Content-Length
- Content-MD5
- BlobType
- AccessTier
- AccessTierChangeTime
- Expiry-Time
- hdi_isfolder
- Propriétaire
- Groupe
- Autorisations
- Acl
- Snapshot (disponible et obligatoire lorsque vous choisissez d’inclure des instantanés dans votre rapport)
- VersionId (disponible et obligatoire lorsque vous choisissez d’inclure des versions de blobs dans votre rapport)
- IsCurrentVersion (disponible et obligatoire lorsque vous choisissez d’inclure des versions de blobs dans votre rapport)
- Métadonnées
- LastAccessTime

### <a name="custom-schema-fields-supported-for-container-inventory"></a>Champs de schéma personnalisés pris en charge pour l’inventaire des conteneurs

- Name (obligatoire)
- Last-Modified
- LeaseStatus
- LeaseState
- LeaseDuration
- PublicAccess
- HasImmutabilityPolicy
- HasLegalHold
- Métadonnées

## <a name="inventory-run"></a>Exécution d’un inventaire

L’exécution d’un inventaire des objets blob est planifiée automatiquement tous les jours. Elle peut prendre jusqu’à 24 heures. La configuration d’un rapport d’inventaire s’effectue en ajoutant une stratégie d’inventaire avec une ou plusieurs règles.

Les stratégies d’inventaire sont lues ou écrites en entier. Les mises à jour partielles ne sont pas prises en charge.

> [!IMPORTANT]
> Si vous activez des règles de pare-feu sur votre compte de stockage, les demandes d’inventaire risquent d’être bloquées. Vous pouvez débloquer ces requêtes en fournissant des exceptions pour les services Microsoft approuvés. Pour plus d’informations, consultez la section Exceptions dans [Configurer des pare-feu et des réseaux virtuels](../common/storage-network-security.md#exceptions).

## <a name="inventory-completed-event"></a>Événement d’inventaire terminé

L’événement `BlobInventoryPolicyCompleted` est généré lorsque l’exécution de l’inventaire est terminée pour une règle. Cet événement se produit également si l’exécution de l’inventaire rencontre une erreur utilisateur avant de commencer. Par exemple, une stratégie non valide ou une erreur qui se produit lorsqu’un conteneur de destination n’est pas présent déclenchera l’événement. Le json suivant montre un exemple d’événement `BlobInventoryPolicyCompleted`.

```json
{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/BlobInventory/providers/Microsoft.EventGrid/topics/BlobInventoryTopic",
  "subject": "BlobDataManagement/BlobInventory",
  "eventType": "Microsoft.Storage.BlobInventoryPolicyCompleted",
  "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "data": {
    "scheduleDateTime": "2021-05-28T03:50:27Z",
    "accountName": "testaccount",
    "ruleName": "Rule_1",
    "policyRunStatus": "Succeeded",
    "policyRunStatusMessage": "Inventory run succeeded, refer manifest file for inventory details.",
    "policyRunId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "manifestBlobUrl": "https://testaccount.blob.core.windows.net/inventory-destination-container/2021/05/26/13-25-36/Rule_1/Rule_1.csv"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-05-28T15:03:18Z"
}
```

Le tableau suivant décrit le schéma de l’événement `BlobInventoryPolicyCompleted`.

|Champ|Type|Description|
|---|---|
|scheduleDateTime|string|Heure à laquelle la stratégie d’inventaire a été planifiée.|
|accountName|string|nom du compte de stockage.|
|ruleName|string|Nom de la règle.|
|policyRunStatus|string|État de l’exécution de l’inventaire. Les valeurs possibles sont `Succeeded`, `PartiallySucceeded` et `Failed`.|
|policyRunStatusMessage|string|Message d’état pour l’exécution de l’inventaire.|
|policyRunId|string|ID d’exécution de la stratégie pour l’exécution de l’inventaire.|
|manifestBlobUrl|string|URL du blob pour le fichier manifeste pour l’exécution de l’inventaire.|

## <a name="inventory-output"></a>Sortie de l’inventaire

Chaque règle d’inventaire génère un ensemble de fichiers dans le conteneur de destination des inventaires spécifié pour cette règle. La sortie d’inventaire est générée dans le chemin `https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/<ruleName`, où :

- *accountName* est le nom du compte Stockage Blob Azure ;
- *inventory-destination-container* est le conteneur de destination que vous avez spécifié dans la règle d’inventaire ;
- *YYYY/MM/DD/HH-MM-SS* est l’heure à laquelle l’inventaire a commencé à s’exécuter ;
- *ruleName* est le nom de la règle d’inventaire.

### <a name="inventory-files"></a>Fichiers d’inventaire

Chaque exécution d’inventaire pour une règle génère les fichiers suivants :

- **Fichier d’inventaire :** L’exécution d’un inventaire pour une règle génère un ou plusieurs fichiers au format CSV ou Apache Parquet. Si le nombre d’objets mis en correspondance est important, plusieurs fichiers sont générés au lieu d’un seul. Chacun de ces fichiers contient les objets mis en correspondance et leurs métadonnées. Pour un fichier au format CSV, la première ligne est toujours celle du schéma. Voici un fichier CSV d’inventaire ouvert dans Microsoft Excel.

  :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="Capture d’écran d’un fichier CSV d’inventaire ouvert dans Microsoft Excel":::

  > [!NOTE]
  > Les rapports au format Apache Parquet présentent les dates au format suivant : `timestamp_millis [number of milliseconds since 1970-01-01 00:00:00 UTC`.

- **Fichier de somme de contrôle :** Un fichier de somme de contrôle contient la somme de contrôle MD5 du contenu du fichier manifest.json. Le nom du fichier de somme de contrôle est `<ruleName>-manifest.checksum`. La génération du fichier de somme de contrôle marque la fin de l’exécution d’une règle d’inventaire.

- **Fichier manifeste :** Un fichier manifest.json contient les détails du ou des fichiers d’inventaire générés pour cette règle. Le nom du fichier est `<ruleName>-manifest.json`. Ce fichier capture également la définition de règle fournie par l’utilisateur et le chemin vers l’inventaire pour cette règle. Le code JSON suivant montre le contenu d’un exemple de fichier manifest.json.

  ```json
  {
  "destinationContainer" : "inventory-destination-container",
  "endpoint" : "https://testaccount.blob.core.windows.net",
  "files" : [
    {
      "blob" : "2021/05/26/13-25-36/Rule_1/Rule_1.csv",
      "size" : 12710092
    }
  ],
  "inventoryCompletionTime" : "2021-05-26T13:35:56Z",
  "inventoryStartTime" : "2021-05-26T13:25:36Z",
  "ruleDefinition" : {
    "filters" : {
      "blobTypes" : [ "blockBlob" ],
      "includeBlobVersions" : false,
      "includeSnapshots" : false,
      "prefixMatch" : [ "penner-test-container-100003" ]
    },
    "format" : "csv",
    "objectType" : "blob",
    "schedule" : "daily",
    "schemaFields" : [
      "Name",
      "Creation-Time",
      "BlobType",
      "Content-Length",
      "LastAccessTime",
      "Last-Modified",
      "Metadata",
      "AccessTier"
    ]
  },
  "ruleName" : "Rule_1",
  "status" : "Succeeded",
  "summary" : {
    "objectCount" : 110000,
    "totalObjectSize" : 23789775
  },
  "version" : "1.0"
  }
  ```

## <a name="pricing-and-billing"></a>Tarification et facturation

La tarification de l’inventaire est basée sur le nombre de blobs et de conteneurs analysés au cours de la période de facturation. À titre d’exemple, supposons qu’un compte contienne un million de blobs et que l’inventaire des blobs soit configuré pour s’exécuter une fois par semaine. Après quatre semaines, quatre millions d’entrées de blobs auront été analysées.

La facturation de l’inventaire des blobs commence le 1er octobre 2021. La tarification régionale sera publiée à ce moment-là. Le prix de base sans ajustement régional est d’environ 0,0025 USD par million d’entrées analysées pour le stockage Blob et de 0,0035 USD si Data Lake Storage Gen2 est activé. Après la création des fichiers d’inventaire, des frais standard supplémentaires de stockage de données et d’exploitation seront encourus pour le stockage, la lecture et l’écriture des fichiers générés par l’inventaire dans le compte.

Une fois qu’un rapport d’inventaire est terminé, des frais standard supplémentaires de stockage de données et d’exploitation seront encourus pour le stockage, la lecture et l’écriture du rapport d’inventaire dans le compte de stockage.

Si une règle contient un préfixe qui chevauche le préfixe d’une autre règle, le même blob peut apparaître dans plusieurs rapports d’inventaire. Dans ce cas, les deux instances vous sont facturées. Par exemple, supposons que l’élément `prefixMatch` d’une règle est défini sur `["inventory-blob-1", "inventory-blob-2"]` et que l’élément `prefixMatch` d’une autre règle est défini sur `["inventory-blob-10", "inventory-blob-20"]`. Un objet nommé `inventory-blob-200` apparaît dans les deux rapports d’inventaire.

Les instantanés et les versions d’un blob sont également pris en compte dans la facturation, même si vous avez défini les filtres `includeSnapshots` et `includeVersions` sur `false`. Ces valeurs de filtre sont sans effet sur la facturation. Vous pouvez les utiliser uniquement pour filtrer ce qui apparaît dans le rapport.

Pour plus d’informations sur la tarification de l’inventaire de blobs Stockage Azure, consultez [Tarification Stockage Blob Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="feature-support"></a>Prise en charge des fonctionnalités

Ce tableau montre comment cette fonctionnalité est prise en charge dans votre compte ainsi que l’impact sur la prise en charge lorsque vous activez certaines fonctionnalités.

| Type de compte de stockage | Stockage Blob (prise en charge par défaut) | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| Usage général v2 Standard | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png)<sup>2</sup>              | ![Oui](../media/icons/yes-icon.png)<sup>2</sup> |
| Objets blob de blocs Premium | ![Oui](../media/icons/yes-icon.png)| ![Oui](../media/icons/yes-icon.png)<sup>2</sup> | ![Oui](../media/icons/yes-icon.png)<sup>2</sup> |

<sup>1</sup> Data Lake Storage Gen2 et le protocole NFS (Network File System) 3.0 requièrent tous deux un compte de stockage avec un espace de noms hiérarchique activé.

<sup>2</sup> La fonctionnalité est prise en charge dans la préversion.

## <a name="known-issues"></a>Problèmes connus

Cette section décrit les limitations et les problèmes connus de la fonctionnalité d’inventaire des objets blob du service Stockage Azure.

### <a name="inventory-job-fails-to-complete-for-hierarchical-namespace-enabled-accounts"></a>Impossible de terminer le travail d’inventaire pour les comptes dont l’espace de noms hiérarchique est activé

Le travail d’inventaire risque de ne pas se terminer dans les 24 heures pour un compte contenant des centaines de millions de blobs et dont l’espace de noms hiérarchique est activé. Dans ce cas, aucun fichier d’inventaire n’est créé.

### <a name="inventory-job-cannot-write-inventory-reports"></a>Le travail d’inventaire ne peut pas écrire de rapports d’inventaire

Une stratégie de réplication d’objet peut empêcher un travail d’inventaire d’écrire des rapports d’inventaire dans le conteneur de destination. D’autres scénarios peuvent archiver les rapports ou les rendre immuables lorsqu’ils sont partiellement terminés. Cela peut entraîner l’échec du travail d’inventaire.

## <a name="next-steps"></a>Étapes suivantes

- [Activer les rapports d’inventaire d’objets blob de stockage Azure](blob-inventory-how-to.md)
- [Calculer le nombre et la taille totale des objets blob par conteneur](calculate-blob-count-size.md)
- [Gérer le cycle de vie de Stockage Blob Azure](./lifecycle-management-overview.md)
