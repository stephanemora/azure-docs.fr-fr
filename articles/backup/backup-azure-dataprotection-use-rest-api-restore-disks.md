---
title: Restaurer des disques Azure à l’aide de l’API REST Protection des données Azure
description: Dans cet article, découvrez comment restaurer des disques Azure à l’aide de l’API REST Protection des données Azure.
ms.topic: conceptual
ms.date: 10/06/2021
ms.assetid: 30f4e7ff-2a55-4a85-be44-55feedc24607
ms.openlocfilehash: d8898b407fdcbea154d9282ff7964a3d0fa0264c
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621060"
---
# <a name="restore-azure-disks-using-azure-data-protection-rest-api"></a>Restaurer des disques Azure à l’aide de l’API REST Protection des données Azure

Cet article explique comment restaurer des [disques](disk-backup-overview.md) à l’aide de la Sauvegarde Azure.

>[!Note]
>- Actuellement, l’option Récupération à l’emplacement d’origine (OLR) pour restaurer en remplaçant les disques sources existants à partir desquels les sauvegardes ont été effectuées n’est pas prise en charge.
>- Vous pouvez restaurer à partir d’un point de récupération pour créer un nouveau disque dans le même groupe de ressources que le disque source ou dans tout autre groupe de ressources. C’est ce qu’on appelle une Récupération à un autre emplacement (ALR).

Dans cet article, vous allez apprendre à :

- effectuer une restauration pour créer un disque ;

- suivre l’état de l’opération de restauration.

## <a name="prerequisites"></a>Configuration requise

- [Créer un coffre de sauvegarde](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [Créer une stratégie de sauvegarde de disque](backup-azure-dataprotection-use-rest-api-create-update-disk-policy.md)

- [Configurer une sauvegarde de disque](backup-azure-dataprotection-use-rest-api-backup-disks.md)

Dans l’exemple, nous ferons référence à un coffre Sauvegarde existant _TestBkpVault_, sous le groupe de ressources _testBkpVaultRG_, où un disque Azure est nommé _msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed_.

## <a name="restore-to-create-a-new-azure-disk"></a>Effectuer une restauration visant à créer un disque Azure

### <a name="set-up-permissions"></a>Définir des autorisations

Le coffre Sauvegarde utilise l’identité managée pour accéder à d’autres ressources Azure. Il est nécessaire, pour effectuer une restauration à partir d’une sauvegarde, que l’identité managée du coffre Sauvegarde dispose d’un ensemble d’autorisations sur le groupe de ressources dans lequel le disque doit être restauré.

Le coffre Sauvegarde utilise une identité managée affectée par le système, limitée à une par ressource et liée au cycle de vie de celle-ci. Pour accorder des autorisations à l’identité managée, utilisez le contrôle d’accès en fonction du rôle (RBAC) Azure. L’identité managée est un principal de service spécifique que vous ne pouvez utiliser qu’avec des ressources Azure. Pour plus d’informations, consultez [Identités managées](../active-directory/managed-identities-azure-resources/overview.md).

Affectez les autorisations appropriées à l’identité managée affectée par le système pour le coffre-fort correspondant au groupe de ressources cible où les disques vont être restaurés/créés. [Plus d’informations](restore-managed-disks.md#restore-to-create-a-new-disk)

### <a name="fetch-the-list-of-recovery-points"></a>Récupérer la liste de points de récupération

Pour répertorier tous les points de récupération disponibles pour une instance de sauvegarde, utilisez l’API de [liste des points de récupération](/rest/api/dataprotection/recovery-points/list).

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/recoveryPoints?api-version=2021-01-01
```

Par exemple, cette API se traduit en :

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/recoveryPoints?api-version=2021-01-01
```

#### <a name="responses-for-list-of-recovery-points"></a>Réponses pour la liste des points de récupération

Une fois que vous avez envoyé la requête *GET*, la réponse 200 (OK) est renvoyée et la liste de tous les points de récupération discrets s’affiche avec tous les détails appropriés.

|Name  |Type  |Description  |
|---------|---------|---------|
|200 OK     |    [AzureBackupRecoveryPointResourceList](/rest/api/dataprotection/recovery-points/list#azurebackuprecoverypointresourcelist)     |   Ok      |
|Autres codes d’état     |    [CloudError](/rest/api/dataprotection/recovery-points/list#clouderror)     |     La réponse d’erreur décrit la raison de l’échec de l’opération.    |

##### <a name="example-response-for-list-of-recovery-points"></a>Exemple de réponse pour la liste des points de récupération

```http
HTTP/1.1 200 OK
Content-Length: 7550
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 11999
x-ms-correlation-request-id: f01e2448-bdc5-4971-aee4-2edd1945c719
x-ms-routing-request-id: CENTRALUSEUAP:20210830T173435Z:0063423e-8b5e-493e-bb2e-74b7c7947c6c
Cache-Control: no-cache
Date: Mon, 30 Aug 2021 17:34:34 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/recoveryPoints/a3d02fc3ab8a4c3a8cc26688c26d3356",
      "name": "a3d02fc3ab8a4c3a8cc26688c26d3356",
      "type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints",
      "properties": {
        "objectType": "AzureBackupDiscreteRecoveryPoint",
        "recoveryPointId": "a3d02fc3ab8a4c3a8cc26688c26d3356",
        "recoveryPointTime": "2021-08-30T10:02:11.6354913Z",
        "recoveryPointType": "Incremental",
        "friendlyName": "119ac243-a789-4a41-be24-0461bceb3888",
        "recoveryPointDataStoresDetails": [
          {
            "id": "13e7c1fe-005d-4b80-8532-c58d937132bb",
            "type": "OperationalStore",
            "creationTime": "2021-08-30T10:02:11.6354913Z",
            "expiryTime": "2021-09-06T10:02:11.6354913Z",
            "metaData": null,
            "visible": true,
            "state": "COMMITTED",
            "rehydrationExpiryTime": null,
            "rehydrationStatus": null
          }
        ],
        "retentionTagName": "Default",
        "retentionTagVersion": "637607428336647408",
        "policyName": "DiskBackupPolicy-03",
        "policyVersion": null
      }
    },
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/recoveryPoints/8f76ebc4c54847c09455d5785a150ce2",
      "name": "8f76ebc4c54847c09455d5785a150ce2",
      "type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints",
      "properties": {
        "objectType": "AzureBackupDiscreteRecoveryPoint",
        "recoveryPointId": "8f76ebc4c54847c09455d5785a150ce2",
        "recoveryPointTime": "2021-08-29T10:01:50.7749008Z",
        "recoveryPointType": "Incremental",
        "friendlyName": "1ac5aa6b-3583-464f-9604-7490c04c2b22",
        "recoveryPointDataStoresDetails": [
          {
            "id": "13e7c1fe-005d-4b80-8532-c58d937132bb",
            "type": "OperationalStore",
            "creationTime": "2021-08-29T10:01:50.7749008Z",
            "expiryTime": "2021-09-05T10:01:50.7749008Z",
            "metaData": null,
            "visible": true,
            "state": "COMMITTED",
            "rehydrationExpiryTime": null,
            "rehydrationStatus": null
          }
        ],
        "retentionTagName": "Default",
        "retentionTagVersion": "637607428336647408",
        "policyName": "DiskBackupPolicy-03",
        "policyVersion": null
      }
    },
    .
    .
    .
    .
}
```

Sélectionnez les points de récupération appropriés dans la liste ci-dessus, puis passez à la préparation de la demande de restauration. Nous allons choisir un point de récupération nommé _a3d02fc3ab8a4c3a8cc26688c26d3356_ dans la liste ci-dessus pour le restaurer.

### <a name="prepare-the-restore-request"></a>Préparer la demande de restauration

Construisez l’ID Azure Resource Manager (ARM) du disque à créer avec le groupe de ressources cible (auquel des autorisations ont été affectées comme indiqué [ci-dessus](#set-up-permissions)) et le nom de disque nécessaire.

Par exemple, nous allons utiliser un disque nommé _APITestDisk2_, sous un groupe de ressources _targetrg_, présent dans la même région que le disque sauvegardé, mais sous un autre abonnement.

#### <a name="construct-the-request-body-for-restore-request"></a>Construire le corps de la demande pour la demande de restauration

Le corps de la demande suivante contient l’ID du point de récupération et les détails de la cible de restauration.

```json
{
  "recoveryPointId": "a3d02fc3ab8a4c3a8cc26688c26d3356",
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "OperationalStore",
    "restoreTargetInfo": {
      "objectType": "restoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "dataSourceInfo": {
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
        "resourceName": "APITestDisk2",
        "resourceType": "Microsoft.Compute/disks",
        "resourceLocation": "westUS",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
        "datasourceType": "Microsoft.Compute/disks"
      },
      "restoreLocation": "westUS"
    }
  }
}
```

#### <a name="validate-restore-requests"></a>Valider les demandes de restauration

Une fois le corps de la demande préparé, validez-le à l’aide de l’[API de validation de la restauration](/rest/api/dataprotection/backup-instances/validate-for-restore). Comme pour l’API de validation de la restauration, il s’agit d’une opération *POST*.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/validateRestore?api-version=2021-01-01
```

Par exemple, cette API se traduit en :

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/validateRestore?api-version=2021-01-01"
```

[En savoir plus](/rest/api/dataprotection/backup-instances/validate-for-restore#request-body) sur le corps de la demande pour cette API POST. 

##### <a name="request-body-to-validate-restore-request"></a>Corps de la demande de validation de la demande de restauration

Nous avons créé une section semblable dans la [section ci-dessus](#construct-the-request-body-for-restore-request). À présent, nous allons ajouter le type d’objet et l’utiliser pour déclencher une opération de validation.

```json

{
  "objectType": "ValidateRestoreRequestObject",
  "recoveryPointId": "a3d02fc3ab8a4c3a8cc26688c26d3356",
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "OperationalStore",
    "restoreTargetInfo": {
      "objectType": "restoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "dataSourceInfo": {
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
        "resourceName": "APITestDisk2",
        "resourceType": "Microsoft.Compute/disks",
        "resourceLocation": "westUS",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
        "datasourceType": "Microsoft.Compute/disks"
      },
      "restoreLocation": "westUS"
    }
  }
}
```

##### <a name="response-to-validate-restore-requests"></a>Réponse aux demandes de validation de restauration

La _demande de validation de restauration_ est une [opération asynchrone](../azure-resource-manager/management/async-operations.md). Aussi, cette opération crée-t-elle une autre opération qui doit faire l’objet d’un suivi distinct.

Elle retourne deux réponses : 202 (Accepté) lors de la création d’une autre opération, puis 200 (OK) quand cette opération est terminée.

|Nom  |Type  |Description  |
|---------|---------|---------|
|200 OK     |         |  État de la demande de validation       |
|202 Accepté     |         |     Acceptée    |

###### <a name="example-response-to-restore-validate-request"></a>Exemple de réponse à une demande de validation de restauration

Une fois l’opération *POST* envoyée, elle renvoie la réponse initiale 202 (Acceptée) avec un en-tête _Azure-asyncOperation_.

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

Suivez l’en-tête _Azure-AsyncOperation_ avec une simple requête *GET*. Lorsque la demande aboutit, elle retourne 200 (OK) avec une réponse d’état de réussite.

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

#### <a name="trigger-restore-requests"></a>Demandes de déclenchement de restauration

L’opération de déclenchement de restauration est une API ***POST***. [En savoir plus](/rest/api/dataprotection/backup-instances/trigger-restore) sur l’opération de déclenchement de restauration.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/restore?api-version=2021-01-01
```

Par exemple, cette API se traduit en :

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/restore?api-version=2021-01-01"
```

##### <a name="create-a-request-body-for-restore-operations"></a>Création d’un corps de demande pour les opérations de restauration

Une fois les demandes validées, utilisez le même corps de demande pour déclencher la _demande de restauration_ avec des modifications mineures.

###### <a name="example-request-body-for-restore"></a>Exemple de corps de demande pour la restauration

La seule modification du corps de la demande de validation de restauration consiste à supprimer l’objet _restoreRequest_ au début et à changer le type d’objet.

```json
{
  "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
  "recoveryPointId": "a3d02fc3ab8a4c3a8cc26688c26d3356",
  "sourceDataStoreType": "OperationalStore",
  "restoreTargetInfo": {
    "datasourceInfo": {
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
      "datasourceType": "Microsoft.Compute/disks",
      "resourceName": "APITestDisk2",
      "resourceType": "Microsoft.Compute/disks",
      "resourceLocation": "westUS",
      "objectType": "Datasource"
    },
    "restoreLocation": "westUS",
    "recoveryOption": "FailIfExists",
    "objectType": "RestoreTargetInfo"
  }
}
```

#### <a name="response-to-trigger-restore-requests"></a>Réponse aux demandes de déclenchement de restauration

La _demande de déclenchement de restauration_ est une [opération asynchrone](../azure-resource-manager/management/async-operations.md). Aussi, cette opération crée-t-elle une autre opération qui doit faire l’objet d’un suivi distinct.

Elle retourne deux réponses : 202 (Accepté) lors de la création d’une autre opération, puis 200 (OK) quand cette opération est terminée.

|Nom  |Type  |Description  |
|---------|---------|---------|
|200 OK     |         |  État de la demande de restauration       |
|202 Accepté     |         |     Acceptée    |

##### <a name="example-response-to-trigger-restore-request"></a>Exemple de réponse à une demande de déclenchement de restauration

Une fois l’opération *POST* envoyée, elle renvoie la réponse initiale 202 (Acceptée) avec un en-tête _Azure-asyncOperation_.

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

Suivez l’en-tête _Azure-AsyncOperation_ avec une simple requête *GET*. Lorsque la demande aboutit, elle renvoie 200 (OK) avec un ID de travail qui doit être suivi pour l’accomplissement de la demande de restauration.

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

#### <a name="track-jobs"></a>Suivre les travaux

Les _demandes de déclenchement de restauration_ ont déclenché le travail de restauration. Pour suivre l’ID de travail résultant, utilisez l’[API des travaux GET](/rest/api/dataprotection/jobs/get).

Utilisez la simple commande GET pour suivre le _JobId_ indiqué dans la [réponse de déclenchement de restauration](#example-response-to-trigger-restore-request) ci-dessus.

```http
 GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852?api-version=2021-01-01

{
  "properties": {
    "activityID": "2881cc22-f527-4af4-9b34-46c6c7b72076-Ibz",
    "subscriptionId": "62b829ee-7936-40c9-a1c9-47a93f9f3965",
    "backupInstanceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed",
    "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/DiskBackup-Policy",
    "dataSourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup",
    "vaultName": "testBkpVault",
    "backupInstanceFriendlyName": "msdiskbackup",
    "policyName": "DiskBackup-Policy",
    "sourceResourceGroup": "RG-DiskBackup",
    "dataSourceSetName": null,
    "dataSourceName": "msdiskbackup",
    "sourceDataStoreName": null,
    "destinationDataStoreName": null,
    "progressEnabled": false,
    "etag": "W/\"datetime'2021-08-26T07%3A18%3A16.157629Z'\"",
    "sourceSubscriptionID": "62b829ee-7936-40c9-a1c9-47a93f9f3965",
    "dataSourceLocation": "westUS",
    "startTime": "2021-08-26T07:12:09.940517Z",
    "endTime": "2021-08-26T07:18:15.6815066Z",
    "dataSourceType": "Microsoft.Compute/disks",
    "operationCategory": "Restore",
    "operation": "Restore",
    "status": "Completed",
    "restoreType": null,
    "isUserTriggered": true,
    "rehydrationPriority": null,
    "supportedActions": [
      ""
    ],
    "duration": "PT6M5.7409896S",
    "progressUrl": null,
    "errorDetails": null,
    "extendedInfo": {
      "backupInstanceState": null,
      "dataTransferedInBytes": null,
      "targetRecoverPoint": null,
      "sourceRecoverPoint": {
        "recoveryPointID": "3a512290ec6b43d6b9a644869f4a3b38",
        "recoveryPointTime": "2021-08-25T09:03:11.6889015Z"
      },
      "recoveryDestination": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
      "subTasks": [
        {
          "taskId": 1,
          "taskName": "Trigger Restore",
          "taskStatus": "Completed",
          "taskProgress": null,
          "additionalDetails": null
        }
      ],
      "additionalDetails": null
    }
  },
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/3bc62c80-913f-47fa-b829-b7df476682be",
  "name": "3bc62c80-913f-47fa-b829-b7df476682be",
  "type": "Microsoft.DataProtection/BackupVaults/backupJobs"
}
```

L’état du travail ci-dessus indique que le travail de restauration est terminé et que les disques ont été récupérés dans l’abonnement spécifié et le groupe de ressources cible.

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de la sauvegarde des disques Azure](disk-backup-overview.md)

Pour plus d’informations sur les API REST de la Sauvegarde Azure, voir les articles suivants :

- [API REST de fournisseur de protection des données Azure](/rest/api/dataprotection/)
- [Bien démarrer avec l’API REST Azure](/rest/api/azure/)