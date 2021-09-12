---
title: Restaurer des blobs dans un compte de stockage à l’aide de l’API REST Protection des données Azure
description: Cet article explique comment restaurer des blobs d’un compte de stockage à l’aide d’une API REST.
ms.topic: conceptual
ms.date: 07/09/2021
ms.assetid: 9b8d21e6-3e23-4345-bb2b-e21040996afd
ms.openlocfilehash: 0ba444126026dc77d6e9b963edb6bed3d108c97b
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598633"
---
# <a name="restore-azure-blobs-to-point-in-time-using-azure-data-protection-rest-api"></a>Restaurer des blobs Azure à un instant dans le passé à l’aide de l’API REST Protection des données Azure

Cet article explique comment restaurer des [objets blob](blob-backup-overview.md) à n’importe quel point dans le temps à l’aide de la sauvegarde Azure.

> [!IMPORTANT]
> Avant de procéder à la restauration des objets blob Azure à l’aide de Sauvegarde Azure, consultez les [points importants](blob-restore.md#before-you-start).

Dans cet article, vous allez apprendre à :

- Restaurer des objets blob Azure à un point dans le temps

- suivre l’état de l’opération de restauration.

## <a name="prerequisites"></a>Configuration requise

- [Créer un coffre de sauvegarde](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [Créer une stratégie de sauvegarde de blob](backup-azure-dataprotection-use-rest-api-create-update-blob-policy.md)

- [Configurer une sauvegarde de blob](backup-azure-dataprotection-use-rest-api-backup-blobs.md)

Les exemples font référence à un coffre de sauvegarde existant, _TestBkpVault_, sous le groupe de ressources _testBkpVaultRG_, où les blobs se trouvent dans un compte de stockage nommé « msblobbackup-f2df34eb-5628-4570-87B2-0331d797c67d ».

## <a name="restoring-azure-blobs-within-a-storage-account"></a>Restauration d’objets blob Azure dans un compte de stockage

### <a name="fetching-the-valid-time-range-for-restore"></a>Récupération de l’intervalle de temps valide pour la restauration

Étant donné que la sauvegarde opérationnelle pour les objets blob est continue, il n’existe aucun point distinct à partir duquel effectuer la restauration. Au lieu de cela, nous devons extraire la plage horaire valide dans laquelle les objets blob peuvent être restaurés à n’importe quel point dans le temps. Dans cet exemple, nous allons vérifier les plages horaires valides pour la restauration au cours des 30 derniers jours.

Vous pouvez afficher la liste des intervalles de temps pouvant être restaurés en utilisant l’API de [recherche d’intervalle de temps pouvant être restauré](/rest/api/dataprotection/restorable-time-ranges/find). Il s’agit d’une API *POST* qui déclenche une opération pour calculer la plage de sauvegardes continues des blobs dans le compte de stockage.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/findRestorableTimeRanges?api-version=2021-01-01
```

Dans notre exemple, cela se traduit par

```http
POST https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d/findRestorableTimeRanges?api-version=2021-01-01
```

#### <a name="create-the-request-body-to-fetch-valid-time-ranges-for-restore"></a>Créer le corps de la demande pour extraire les intervalles de temps valides pour extraction

Pour déclencher une opération de calcul des intervalles de temps valides, les composants d’un corps de demande sont les suivants

|**Nom**  |**Type**  |**Description**  |
|---------|---------|---------|
|sourceDatastoreType     |   [RestoreSourceDataStoreType](/rest/api/dataprotection/restorable-time-ranges/find#restoresourcedatastoretype)      |    Magasin de données contenant les données à restaurer     |
|startTime     |    String     |  Heure de début de la demande de liste des intervalles de temps pouvant être restaurés. Format ISO 8601.       |
|endTime     |    Chaîne     |    Heure de fin de la demande de liste des intervalles de temps pouvant être restaurés. Format ISO 8601.     |

##### <a name="example-request-body-to-fetch-valid-time-range"></a>Exemple de corps de demande pour extraire un intervalle de temps valide

Le corps de demande suivant définit les propriétés requises pour extraire les intervalles de temps des données continues pouvant être restaurées. Étant donné que les sauvegardes de blobs résident dans le compte de stockage, le magasin de données est « opérationnel ». Vous pouvez spécifier des heures de début et de fin qui permettent d’affiner le processus de recherche et de renvoyer l’intervalle de temps disponible.

```json
{
  "sourceDataStoreType": "OperationalStore",
  "startTime": "",
  "endTime": ""
}
```

#### <a name="responses-for-fetch-valid-time-ranges"></a>Réponses pour les intervalles de temps valides pour extraction

Une fois que vous avez envoyé la demande *POST*, la réponse 200 (OK) indique les heures de début et de fin de l’intervalle de temps disponible pour restauration s’inscrivant dans les heures de début et de fin spécifiées dans la demande.

|**Nom**  |**Type**  |**Description**  |
|---------|---------|---------|
|200 (OK)     |   [AzureBackupFindRestorableTimeRangesResponseResource](/rest/api/dataprotection/restorable-time-ranges/find#azurebackupfindrestorabletimerangesresponseresource)      |    Ok     |
|Autres codes d’état     |    [CloudError](/rest/api/dataprotection/restorable-time-ranges/find#clouderror)     |  Réponse d’erreur décrivant le motif de l’échec de l’opération.       |

##### <a name="example-response-for-fetch-valid-time-ranges"></a>Exemple de réponse pour les intervalles de temps valides pour extraction

```http
HTTP/1.1 200 OK
Content-Length: 379
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: a2b7c2d9-01f5-499a-b521-55da4862c79a
x-ms-routing-request-id: CENTRALUSEUAP:20210708T184646Z:4996a2bf-2df8-48a7-9b53-a552466a27f7
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 18:46:45 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "id": "msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d",
  "type": "Microsoft.DataProtection/backupVaults/backupInstances/findRestorableTimeRanges",
  "properties": {
    "restorableTimeRanges": [
      {
        "startTime": "2021-07-06T18:46:45.947728Z",
        "endTime": "2021-07-08T18:46:45.9932408Z",
        "objectType": "RestorableTimeRange"
      }
    ],
    "objectType": "AzureBackupFindRestorableTimeRangesResponse"
  }
}
```

### <a name="preparing-the-restore-request"></a>Préparation de la demande de restauration

Une fois l’instant dans le passé pour la restauration dans le même compte de stockage fixé, il existe plusieurs options de restauration.

#### <a name="restoring-all-the-blobs-to-a-point-in-time"></a>Restauration de tous les objets blob à un point dans le temps

L’utilisation de cette option restaure tous les objets blob de blocs dans le compte de stockage en les restaurant à l’instant dans le passé qui a été sélectionné. La restauration des comptes de stockage contenant de grandes quantités de données ou témoin d’une attrition élevée peut prendre plus de temps.

##### <a name="constructing-the-request-body-for-point-in-time-restore-of-all-blobs"></a>Construction du corps de la demande pour la restauration à un instant dans le passé de tous les blobs

Les points clés à retenir dans ce scénario sont les suivants :

- La restauration étant effectuée dans le même compte de stockage, l’objet cible pour la restauration est le même que la source de données. Cela se reflète dans la section des informations sur la cible de restauration ci-dessous.
- Les sauvegardes étant continues, l’heure de restauration est un instant dans le passé, non un point de récupération distinct.
- Tous les blobs sont restaurés
- Le magasin de données source, c’est-à-dire l’emplacement où résident les sauvegardes, est le même compte de stockage. Par conséquent, le magasin de données source est un magasin « opérationnel ».

```json
{
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryTimeBasedRestoreRequest",
    "restoreTargetInfo": {
      "objectType": "RestoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "restoreLocation": "westus",
      "datasourceInfo": {
        "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
        "resourceLocation": "westus",
        "resourceName": "msblobbackup",
        "resourceType": "Microsoft.Storage/storageAccounts",
        "resourceUri": ""
      }
    },
    "sourceDataStoreType": "OperationalStore",
    "recoveryPointTime": "2021-07-08T00:00:00.0000000Z"
  }
}
```

#### <a name="restoring-few-containers-to-a-point-in-time"></a>Restauration de quelques conteneurs à un instant dans le passé

L’utilisation de cette option vous permet de sélectionner jusqu’à 10 conteneurs à restaurer, ou de restaurer un sous-ensemble de blobs à l’aide d’une correspondance de préfixe. Vous pouvez spécifier jusqu’à 10 plages lexicographiques d’objets blob dans un seul conteneur ou dans plusieurs conteneurs pour ramener ces objets blob à l’état qu’ils avaient à un instant dans le passé donné. En cas d’utilisation de préfixes, voici quelques considérations à garder à l’esprit :

- Vous pouvez utiliser une barre oblique (/) pour délimiter le nom du conteneur du préfixe de l’objet blob
- Le début de la plage spécifiée est inclusif mais la plage spécifiée est exclusive.

[En savoir plus](blob-restore.md#use-prefix-match-for-restoring-blobs) sur l’utilisation de préfixes pour restaurer des plages d’objets blob.

##### <a name="constructing-the-request-body-for-point-in-time-restore-of-selected-containers-or-few-blobs"></a>Construction du corps de la demande pour la restauration à un instant dans le passé de conteneurs sélectionnés ou de quelques blobs

Les points clés à retenir dans ce scénario sont les suivants :

- La restauration étant effectuée dans le même compte de stockage, l’objet cible pour la restauration est le même que la source de données. Cela se reflète dans la section des informations sur la cible de restauration ci-dessous.
- Les sauvegardes étant continues, l’heure de restauration est un instant dans le passé, non un point de récupération distinct.
- Peu d’éléments dans le compte de stockage sont restaurés. Il peut s’agir de conteneurs ou de blobs correspondant à un modèle de préfixe.
- Le magasin de données source, c’est-à-dire l’emplacement où résident les sauvegardes, est le même compte de stockage. Par conséquent, le magasin de données source est un magasin « opérationnel ».

```json
{
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryTimeBasedRestoreRequest",
    "restoreTargetInfo": {
      "objectType": "ItemLevelRestoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "restoreLocation": "westus",
      "restoreCriteria": [
        {
          "objectType": "RangeBasedItemLevelRestoreCriteria",
          "minMatchingValue": "Container1",
          "maxMatchingValue": "Container10-0"
        }
      ],
      "datasourceInfo": {
        "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
        "resourceLocation": "westus",
        "resourceName": "msblobbackup",
        "resourceType": "Microsoft.Storage/storageAccounts",
        "resourceUri": ""
      }
    },
    "sourceDataStoreType": "OperationalStore",
    "recoveryPointTime": "2021-07-08T00:00:00.0000000Z"
  }
}
```

#### <a name="validating-restore-requests"></a>Validation des demandes de restauration

Une fois le corps de la demande préparé, il peut être validé à l’aide de l’[API Valider pour la restauration](/rest/api/dataprotection/backup-instances/validate-for-restore). Comme l’API Valider pour la sauvegarde, il s’agit d’une opération *POST*.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/validateRestore?api-version=2021-01-01
```

Dans notre exemple, cela se traduit par :

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d/validateRestore?api-version=2021-01-01"
```

Le corps de la demande pour cette API POST est détaillé [ici](/rest/api/dataprotection/backup-instances/validate-for-restore#request-body). Nous avons construit le même corps de demande dans la section ci-dessus pour les scénarios de [restauration de tous les blobs](#constructing-the-request-body-for-point-in-time-restore-of-all-blobs) et de [restauration de quelques éléments](#constructing-the-request-body-for-point-in-time-restore-of-selected-containers-or-few-blobs). Nous allons l’utiliser pour déclencher une opération de validation.

##### <a name="response-to-validate-restore-requests"></a>Réponse aux demandes de validation de restauration

La demande de validation de restauration est une [opération asynchrone](../azure-resource-manager/management/async-operations.md). ce qui signifie qu’elle crée une autre opération qui doit faire l’objet d’un suivi distinct.

Elle retourne deux réponses : 202 (Accepté) lors de la création d’une autre opération, puis 200 (OK) quand cette opération est terminée.

|Nom  |Type  |Description  |
|---------|---------|---------|
|200 OK     |         |  État de la demande de validation       |
|202 Accepté     |         |     Acceptée    |

###### <a name="example-response-to-restore-validate-request"></a>Exemple de réponse à une demande de validation de restauration

Une fois l’opération *POST* envoyée, la réponse initiale est 202 (Acceptée) avec un en-tête Azure-asyncOperation.

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 10
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: bae60c92-669d-45a4-aed9-8392cca7cc8d
x-ms-routing-request-id: CENTRALUSEUAP:20210708T205935Z:f51db7a4-9826-4084-aa3b-ae640dc78af6
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 20:59:35 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

Suivez l’en-tête Azure-AsyncOperation avec une simple demande *GET*. Lorsque la demande aboutit, elle retourne 200 (OK) avec une réponse d’état de réussite.

```http
 GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==",
  "status": "Succeeded",
  "startTime": "2021-07-08T20:59:35.0060264Z",
  "endTime": "2021-07-08T20:59:57Z"
}
```

#### <a name="triggering-restore-requests"></a>Demandes de déclenchement de restauration

L’opération de déclenchement de restauration est une API ***POST***. Tous les détails sur l’opération de déclenchement de restauration figurent [ici](/rest/api/dataprotection/backup-instances/trigger-restore).

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/restore?api-version=2021-01-01
```

Dans notre exemple, cela se traduit par :

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d/restore?api-version=2021-01-01"
```

##### <a name="creating-a-request-body-for-restore-operations"></a>Création d’un corps de demande pour les opérations de restauration

Une fois les demandes validées, le même corps de demande peut être utilisé pour déclencher la demande de restauration avec des modifications mineures.

###### <a name="example-request-body-for-all-blobs-restore"></a>Exemple de corps de demande de restauration de tous les blobs

La seule modification du corps de la demande de validation de restauration consiste à supprimer l’objet « restoreRequest » au début.

```json
{
  "objectType": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "restoreTargetInfo": {
    "objectType": "RestoreTargetInfo",
    "recoveryOption": "FailIfExists",
    "restoreLocation": "westus",
    "datasourceInfo": {
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceLocation": "westus",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceUri": ""
    }
  },
  "sourceDataStoreType": "OperationalStore",
  "recoveryPointTime": "2021-07-08T00:00:00Z"
}
```

###### <a name="example-request-body-for-items-or-few-blobs-restore"></a>Exemple de corps de demande pour la restauration d’éléments ou de quelques blobs

La seule modification du corps de la demande de validation de restauration consiste à supprimer l’objet « restoreRequest » au début.

```json
{
  "objectType": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "restoreTargetInfo": {
    "objectType": "ItemLevelRestoreTargetInfo",
    "recoveryOption": "FailIfExists",
    "restoreLocation": "westus",
    "restoreCriteria": [
      {
        "objectType": "RangeBasedItemLevelRestoreCriteria",
        "minMatchingValue": "Container1",
        "maxMatchingValue": "Container2"
      }
    ],
    "datasourceInfo": {
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceLocation": "westus",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceUri": ""
    }
  },
  "sourceDataStoreType": "OperationalStore",
  "recoveryPointTime": "2021-07-08T00:00:00.0000000Z"
}
```

#### <a name="response-to-trigger-restore-requests"></a>Réponse aux demandes de déclenchement de restauration

La demande de déclenchement de restauration est une [opération asynchrone](../azure-resource-manager/management/async-operations.md). ce qui signifie qu’elle crée une autre opération qui doit faire l’objet d’un suivi distinct.

Elle retourne deux réponses : 202 (Accepté) lors de la création d’une autre opération, puis 200 (OK) quand cette opération est terminée.

|Nom  |Type  |Description  |
|---------|---------|---------|
|200 OK     |         |  État de la demande de restauration       |
|202 Accepté     |         |     Acceptée    |

##### <a name="example-response-to-trigger-restore-request"></a>Exemple de réponse à une demande de déclenchement de restauration

Une fois l’opération *POST* envoyée, la réponse initiale est 202 (Acceptée) avec un en-tête Azure-asyncOperation.

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 30
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1197
x-ms-correlation-request-id: 8661209c-5b6a-44fe-b676-4e2b9c296593
x-ms-routing-request-id: CENTRALUSEUAP:20210708T204652Z:69e3fa4b-c5d9-4601-9410-598006ada187
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 20:46:52 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

Suivez l’en-tête Azure-AsyncOperation avec une simple demande *GET*. Lorsque la demande aboutit, elle renvoie 200 (OK) avec un ID de travail qui doit être suivi pour l’accomplissement de la demande de restauration.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==",
  "status": "Succeeded",
  "startTime": "2021-07-08T20:46:52.4110868Z",
  "endTime": "2021-07-08T20:46:56Z",
  "properties": {
    "jobId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852",
    "objectType": "OperationJobExtendedInfo"
  }
}
```

#### <a name="tracking-jobs"></a>Suivi des travaux

Les demandes de déclenchement de restauration qui ont déclenché le travail de restauration et l’ID de travail qui en résulte devraient être suivis à l’aide de l’[API GET Jobs](/rest/api/dataprotection/jobs/get).

Utilisez la simple commande GET pour suivre le JobId indiqué dans la [réponse de déclenchement de restauration](#example-response-to-trigger-restore-request) ci-dessus.

```http
 GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852?api-version=2021-01-01

{
  "properties": {
    "activityID": "4195ca6c-e02d-11eb-b0b1-70bc105e2242",
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx",
    "backupInstanceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d",
    "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/BlobBackup-Policy",
    "dataSourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
    "vaultName": "BV-JPE-GRS",
    "backupInstanceFriendlyName": "msblobbackup",
    "policyName": "BlobBackup-Policy",
    "sourceResourceGroup": "RG-BlobBackup",
    "dataSourceName": "msblobbackup",
    "progressEnabled": false,
    "etag": "W/\"datetime'2021-07-08T20%3A48%3A36.6999667Z'\"",
    "sourceSubscriptionID": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx",
    "dataSourceLocation": "westus",
    "startTime": "2021-07-08T20:44:19.5467125Z",
    "endTime": "2021-07-08T20:48:35.8297312Z",
    "dataSourceType": "Microsoft.Storage/storageAccounts/blobServices",
    "operationCategory": "Restore",
    "operation": "Restore",
    "status": "Completed",
    "isUserTriggered": true,
    "supportedActions": [
      ""
    ],
    "duration": "PT4M16.2830187S",
    "extendedInfo": {
      "sourceRecoverPoint": {
        "recoveryPointTime": "2021-07-08T00:00:00Z"
      },
      "recoveryDestination": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "subTasks": [
        {
          "taskId": 1,
          "taskName": "Trigger Restore",
          "taskStatus": "Completed"
        }
      ]
    }
  },
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852",
  "name": "c4bd49a1-0645-4eec-b207-feb818962852",
  "type": "Microsoft.DataProtection/BackupVaults/backupJobs"
}
```

L’état du travail ci-dessus indique que le travail de restauration est terminé et que tous les blobs ont été récupérés à l’instant dans le passé spécifié.

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de la sauvegarde de blob Azure](blob-backup-overview.md)

Pour plus d’informations sur les API REST de la Sauvegarde Azure, voir les documents suivants :

- [API REST de fournisseur de protection des données Azure](/rest/api/dataprotection/)
- [Bien démarrer avec l’API REST Azure](/rest/api/azure/)