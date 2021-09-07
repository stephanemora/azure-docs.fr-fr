---
title: Restaurer des machines virtuelles Azure avec l’API REST
description: Dans cet article, découvrez comment gérer les opérations de restauration de la sauvegarde de machines virtuelles Azure à l’aide de l’API REST.
ms.topic: conceptual
ms.date: 08/26/2021
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: f82adee9690c0114fef17640672c7326cffc8481
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122966074"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>Restaurer des machines virtuelles avec l’API REST

Après la sauvegarde d’une machine virtuelle Azure à l’aide de Sauvegarde Azure, il est possible de restaurer soit la totalité de cette machine virtuelle, soit des disques ou des fichiers à partir de la même copie de sauvegarde. Cet article explique comment restaurer une machine virtuelle Azure ou des disques avec l’API REST.

Quelle que soit l’opération de restauration, il faut commencer par identifier le point de récupération pertinent.

## <a name="select-recovery-point"></a>Sélectionner un point de récupération

[L’API REST Lister les points de récupération](/rest/api/backup/recovery-points/list) permet de lister les points de récupération disponibles d’un élément de sauvegarde. Il s’agit d’une simple opération *GET* avec toutes les valeurs nécessaires.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13
```

`{containerName}` et `{protectedItemName}` sont tels qu’ils ont été créés [ici](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation). `{fabricName}` correspond à « Azure ».

L’URI *GET* contient tous les paramètres obligatoires. Il n’est pas nécessaire d’ajouter un corps de demande.

### <a name="responses"></a>Réponses

|Nom  |Type  |Description  |
|---------|---------|---------|
|200 OK     |   [RecoveryPointResourceList](/rest/api/backup/recovery-points/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Exemple de réponse

Une fois l’URI *GET* envoyé, une réponse 200 (OK) est retournée.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-client-request-id: c48f4436-ce3f-42da-b537-12710d4d1a24; c48f4436-ce3f-42da-b537-12710d4d1a24
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14998
x-ms-correlation-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-routing-request-id: SOUTHINDIA:20180604T071851Z:03453538-2f8d-46de-8374-143ccdf60f40
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 07:18:51 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/20982486783671",
      "name": "20982486783671",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "AppConsistent",
        "recoveryPointTime": "2018-06-04T06:06:00.5121087Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/23358112038108",
      "name": "23358112038108",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2018-06-03T06:20:56.3043878Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
......
```

Le point de récupération est identifié par le champ `{name}` dans la réponse ci-dessus.

## <a name="restore-operations"></a>Opérations de restauration

Après avoir sélectionné le [point de restauration approprié](#select-recovery-point), procédez au déclenchement de l’opération de restauration.

***Toutes les opérations de restauration sur l’élément de sauvegarde sont effectuées avec la même API *POST*. Seul le corps de la requête change avec les scénarios de restauration.***

> [!IMPORTANT]
> Tous les détails sur les différentes options de restauration et leurs dépendances sont mentionnés [ici](./backup-azure-arm-restore-vms.md#restore-options). Veuillez consulter ces détails avant de commencer à déclencher ces opérations.

Le déclenchement des opérations de restauration est une requête *POST*. Pour en savoir plus sur l’API, consultez l’[API REST « déclencher la restauration »](/rest/api/backup/restores/trigger).

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

`{containerName}` et `{protectedItemName}` sont tels qu’ils ont été créés [ici](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation). `{fabricName}` correspond à « Azure » et `{recoveryPointId}` est le champ `{name}` du point de récupération mentionné [ci-dessus](#example-response).

Une fois le point de récupération obtenu, nous devons construire le corps de la requête pour le scénario de restauration correspondant. Les sections suivantes décrivent le corps de la requête pour chaque scénario.

- [Restaurer des disques](#restore-disks)
- [Remplacer des disques](#replace-disks-in-a-backed-up-virtual-machine)
- [Restaurer en tant que nouvelle machine virtuelle](#restore-as-another-virtual-machine)

### <a name="restore-response"></a>Réponse de restauration

Le déclenchement de toute opération de restauration est une [opération asynchrone](../azure-resource-manager/management/async-operations.md). ce qui signifie qu’elle crée une autre opération qui doit faire l’objet d’un suivi distinct.

Elle retourne deux réponses : 202 (Accepté) lors de la création d’une autre opération, puis 200 (OK) quand cette opération est terminée.

|Nom  |Type  |Description  |
|---------|---------|---------|
|202 Accepté     |         |     Acceptée    |

#### <a name="example-responses"></a>Exemples de réponses

Une fois l’URI *POST* envoyé pour déclencher la restauration des disques, la réponse initiale est 202 (Accepté) avec un en-tête d’emplacement ou Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Effectuez ensuite le suivi de l’opération qui en résulte en utilisant l’en-tête d’emplacement ou Azure-AsyncOperation avec une commande *GET* simple.

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
```

Une fois terminée, l’opération retourne 200 (OK) avec l’ID de la tâche de restauration obtenue dans le corps de la réponse.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-client-request-id: a7f3a144-ed80-41ee-bffe-ae6a90c35a2f; a7f3a144-ed80-41ee-bffe-ae6a90c35a2f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14973
x-ms-correlation-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-routing-request-id: SOUTHINDIA:20180604T130917Z:ea2a8011-eb83-4a4b-9ed2-e694070a966a
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:09:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "name": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "status": "Succeeded",
  "startTime": "2018-06-04T13:00:03.8068176Z",
  "endTime": "2018-06-04T13:00:03.8068176Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "3021262a-fb3a-4538-9b37-e3e97a386093"
  }
}
```

Le travail de restauration étant une opération longue, il doit faire l’objet d’un suivi, qui est détaillé dans le [document sur la supervision des travaux avec l’API REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="restore-disks"></a>Restaurer des disques

Pour personnaliser la création d’une machine virtuelle à partir des données de sauvegarde, il vous suffit de restaurer les disques dans un compte de stockage choisi et de créer une machine virtuelle à partir de ces disques en fonction des besoins. Le compte de stockage doit se trouver dans la même région que le coffre Recovery Services et ne pas être redondant interzone. Les disques, ainsi que la configuration de la machine virtuelle sauvegardée (« vmconfig.json »), sont stockés dans le compte de stockage indiqué. Comme expliqué [ci-dessus](#restore-operations), le corps de la requête approprié pour la restauration de disques est fourni ci-dessous.

#### <a name="create-request-body"></a>Créer un corps de demande

Voici les composants du corps de la demande pour déclencher une restauration de disque à partir d’une sauvegarde de machine virtuelle Azure.

|Nom  |Type  |Description  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Pour obtenir la liste complète des définitions du corps de la demande et d’autres détails, voir le [document API REST Déclencher la restauration](/rest/api/backup/restores/trigger#request-body).

##### <a name="example-request"></a>Exemple de requête

Le corps de demande suivant définit les propriétés requises pour déclencher une restauration de disque.

```json
{
  "properties": {
    "objectType": "IaasVMRestoreRequest",
    "recoveryPointId": "20982486783671",
    "recoveryType": "RestoreDisks",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
    "region": "westus",
    "createNewCloudService": false,
    "originalStorageAccountOption": false,
    "encryptionDetails": {
      "encryptionEnabled": false
    }
  }
}
```

### <a name="restore-disks-selectively"></a>Restaurer des disques de manière sélective

Si vous [sauvegardez des disques de manière sélective](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup), la liste de disques sauvegardés est fournie dans le [résumé des points de récupération](#select-recovery-point) et la [réponse détaillée](/rest/api/backup/recovery-points/get). Vous pouvez également restaurer des disques de manière sélective. Plus de détails sont disponibles [ici](selective-disk-backup-restore.md#selective-disk-restore). Pour restaurer un disque de manière sélective dans la liste des disques sauvegardés, recherchez le numéro d’unité logique du disque à partir de la réponse du point de récupération et ajoutez la propriété **restoreDiskLunList** au [corps de la demande ci-dessus](#example-request) comme indiqué ci-dessous.

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "RestoreDisks",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "encryptionDetails": {
          "encryptionEnabled": false
        },
        "restoreDiskLunList" : [0]
    }
}

```

Une fois que vous effectuez le suivi de la réponse comme expliqué [ci-dessus](#responses) et que le long travail est terminé, les disques et la configuration de la machine virtuelle sauvegardée (« VMConfig.json ») sont présents dans le compte de stockage indiqué.

### <a name="replace-disks-in-a-backed-up-virtual-machine"></a>Remplacer des disques dans une machine virtuelle sauvegardée

Si l’opération de restauration de disques crée des disques à partir du point de récupération, l’opération de remplacement de disques remplace les disques actuels de la machine virtuelle sauvegardée par ceux du point de récupération. Comme expliqué [ci-dessus](#restore-operations), le corps de la requête approprié pour le remplacement de disques est fourni ci-dessous.

#### <a name="create-request-body"></a>Créer un corps de demande

Voici les composants du corps de la requête pour déclencher un remplacement de disques à partir d’une sauvegarde de machine virtuelle Azure.

|Name  |Type  |Description  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Pour obtenir la liste complète des définitions du corps de la demande et d’autres détails, voir le [document API REST Déclencher la restauration](/rest/api/backup/restores/trigger#request-body).

#### <a name="example-request"></a>Exemple de requête

Le corps de demande suivant définit les propriétés requises pour déclencher une restauration de disque.

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "OriginalLocation",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",  
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "affinityGroup": "",
        "diskEncryptionSetId": null,
        "subnetId": null,
        "targetDomainNameId": null,
        "targetResourceGroupId": null,
        "targetVirtualMachineId": null,
        "virtualNetworkId": null
     }
}

```

### <a name="restore-as-another-virtual-machine"></a>Restaurer sous la forme d’une autre machine virtuelle

Comme expliqué [ci-dessus](#restore-operations), le corps de requête suivant définit les propriétés requises pour déclencher une restauration de machine virtuelle.

```json
{
  "properties": {
          "objectType":  "IaasVMRestoreRequest",
          "recoveryPointId": "348916168024334",
          "recoveryType": "AlternateLocation",
          "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
          "targetVirtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Compute/virtualmachines/targetVM",
          "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG",
          "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testingAccount",
          "virtualNetworkId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet",
          "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet/subnets/default",
          "region": "westus",
          "createNewCloudService": false,
          "originalStorageAccountOption": false,
          "encryptionDetails": {
            "encryptionEnabled": false
          }
     }
 }
```

La réponse doit être gérée comme [pour la restauration de disques](#responses).

## <a name="cross-region-restore"></a>Restauration interrégion

Si la restauration entre régions est activée sur le coffre avec lequel vous avez protégé vos machines virtuelles, les données de sauvegarde sont répliquées dans la région secondaire. Vous pouvez utiliser les données de sauvegarde pour effectuer une opération de restauration. Pour déclencher une opération de restauration dans la région secondaire à l’aide de l’API REST, procédez comme suit :

### <a name="select-recovery-point-in-secondary-region"></a>Sélectionnez un point de récupération dans la région secondaire

Vous pouvez répertorier les points de récupération disponibles d’un élément de sauvegarde dans la région secondaire à l’aide de l’[API REST de liste des points de récupération pour la restauration entre régions](/rest/api/backup/recovery-points-crr/list). Il s’agit d’une simple opération GET avec toutes les valeurs nécessaires.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2018-12-20

```

`{containerName}` et `{protectedItemName}` sont tels qu’ils ont été créés [ici](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation). `{fabricName}` correspond à « Azure ».

L’URI *GET* contient tous les paramètres obligatoires. Aucun corps de demande supplémentaire n’est pas requis.

>[!NOTE]
>Pour obtenir les points de récupération dans la région secondaire, utilisez l’API version 2018-12-20 comme dans l’exemple ci-dessus.

#### <a name="responses"></a>Réponses

|Nom  |Type  |Description  |
|---------|---------|---------|
|200 OK     |   [RecoveryPointResourceList](/rest/api/backup/recovery-points-crr/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Exemple de réponse

Une fois l’URI *GET* envoyé, une réponse 200 (OK) est retournée.

```http
Headers:
Pragma                        : no-cache
X-Content-Type-Options        : nosniff
x-ms-request-id               : bfc4a4e6-c585-46e0-8e38-f11a86093701
x-ms-client-request-id        : 4344a9c2-70d8-482d-b200-0ca9cc498812,4344a9c2-70d8-482d-b200-0ca9cc498812
Strict-Transport-Security     : max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-resource-requests: 149
x-ms-correlation-request-id   : bfc4a4e6-c585-46e0-8e38-f11a86093701
x-ms-routing-request-id       : SOUTHINDIA:20210731T112441Z:bfc4a4e6-c585-46e0-8e38-f11a86093701
Cache-Control                 : no-cache
Date                          : Sat, 31 Jul 2021 11:24:40 GMT
Server                        : Microsoft-IIS/10.0
X-Powered-By                  : ASP.NET

Body:
{
  "value": [
    {
      "id":
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG1;testVM/protectedItems/VM;iaasvmcontainerv2;testRG1;testVM/recoveryPoints/932895704780058094",
      "name": "932895704780058094",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2021-07-31T09:24:34.687561Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "PremiumVMOnPartialPremiumStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_D2s_v3",
        "originalStorageAccountOption": false,
        "osType": "Windows"
      }
    },
    {
      "id":
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG1;testVM/protectedItems/VM;iaasvmcontainerv2;testRG1;testVM/recoveryPoints/932891484644960954",
      "name": "932891484644960954",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2021-07-30T09:20:01.8355052Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "PremiumVMOnPartialPremiumStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_D2s_v3",
        "originalStorageAccountOption": false,
        "osType": "Windows"
      }
    },
.....
```

Le point de récupération est identifié par le champ `{name}` dans la réponse ci-dessus.

### <a name="get-access-token"></a>Obtenir un jeton d’accès

Pour effectuer une restauration entre régions, vous avez besoin d’un jeton d’accès pour autoriser votre demande à accéder aux points de restauration répliqués dans la région secondaire. Pour obtenir un jeton d’accès, procédez comme suit :

#### <a name="step-1"></a>Étape 1 :

Utilisez l’[API des propriétés AAD](/rest/api/backup/aad-properties/get) pour obtenir les propriétés AAD de la région secondaire (*westus* dans l’exemple ci-dessous) :

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.RecoveryServices/locations/westus/backupAadProperties?api-version=2018-12-20
```

##### <a name="response-example"></a>Exemple de réponse

La réponse est retournée au format suivant :

```json
{
  "properties": {
    "tenantId": "00000000-0000-0000-0000-000000000000",
    "audience": "https://RecoveryServices/IaasCoord/aadmgmt/wus",
    "servicePrincipalObjectId": "00000000-0000-0000-0000-000000000000"
  }
}
```

#### <a name="step-2"></a>Étape 2 :

Utilisez l’[API d’obtention de jeton d’accès](/rest/api/backup/recovery-points-get-access-token-for-crr/get-access-token) pour autoriser votre demande d’accès aux points de restauration répliqués dans la région secondaire :

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/accessToken?api-version=2018-12-20
```

Pour le corps de la demande, collez le contenu de la réponse renvoyée par l’API des propriétés AAD à l’étape précédente.

```json
{
  "properties": {
    "tenantId": "00000000-0000-0000-0000-000000000000",
    "audience": "https://RecoveryServices/IaasCoord/aadmgmt/wus",
    "servicePrincipalObjectId": "00000000-0000-0000-0000-000000000000"
  }
}
```

##### <a name="response-example"></a>Exemple de réponse

La réponse est retournée au format suivant :

```json
{
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG1;testVM/protectedItems/VM;iaasvmcontainerv2;testRG1;testVM/recoveryPoints/26083826328862",
  "name": "932879774590051503",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
    "properties": {
        "objectType": "CrrAccessToken",
        "accessTokenString": "<access-token-string>",
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "testVaultRG",
        "resourceName": "testVault",
        "resourceId": "000000000000000000",
        "protectionContainerId": 000000,
        "recoveryPointId": "932879774590051503",
        "recoveryPointTime": "7/26/2021 3:35:36 PM",
        "containerName": "iaasvmcontainerv2;testRG1;testVM",
        "containerType": "IaasVMContainer",
        "backupManagementType": "AzureIaasVM",
        "datasourceType": "VM",
        "datasourceName": "testvm1234",
        "datasourceId": "000000000000000000",
        "datasourceContainerName": "iaasvmcontainerv2;testRG1;testVM",
        "coordinatorServiceStampUri": "https://pod01-coord1.eus.backup.windowsazure.com",
        "protectionServiceStampId": "00000000-0000-0000-0000-000000000000",
        "protectionServiceStampUri": "https://pod01-prot1h-int.eus.backup.windowsazure.com",
        "tokenExtendedInformation": "<IaaSVMRecoveryPointMetadataBase xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" i:type=\"IaaSVMRecoveryPointMetadata_V2015_09\" xmlns=\"http://windowscloudbackup.com/CloudCommon/V2011_09\"><MetadataVersion>V2015_09</MetadataVersion><ContainerType i:nil=\"true\" /><InstantRpGCId>ef4ab5a7-c2c0-4304-af80-af49f48af3d1;AzureBackup_testvm1234_932843259176972511;AzureBackup_20210726_033536;AzureBackupRG_eastus_1</InstantRpGCId><IsBlockBlobEnabled>true</IsBlockBlobEnabled><IsManagedVirtualMachine>true</IsManagedVirtualMachine><OriginalSAOption>false</OriginalSAOption><OsType>Windows</OsType><ReadMetadaFromConfigBlob i:nil=\"true\" /><RecoveryPointConsistencyType>CrashConsistent</RecoveryPointConsistencyType><RpDiskDetails i:nil=\"true\" /><SourceIaaSVMRPKeyAndSecret i:nil=\"true\" /><SourceIaaSVMStorageType>PremiumVMOnPartialPremiumStorage</SourceIaaSVMStorageType><VMSizeDescription>Standard_D2s_v3</VMSizeDescription><Zones xmlns:d2p1=\"http://schemas.microsoft.com/2003/10/Serialization/Arrays\" i:nil=\"true\" /></IaaSVMRecoveryPointMetadataBase>",
        "rpTierInformation": {
            "InstantRP": "Valid",
            "HardenedRP": "Valid"
        },
        "rpOriginalSAOption": false,
        "rpIsManagedVirtualMachine": true,
        "rpVMSizeDescription": "Standard_D2s_v3",
        "bMSActiveRegion": "EastUS"
    }
}
```

### <a name="restore-disks-to-the-secondary-region"></a>Restaurer des disques dans la région secondaire

Utilisez l’[API de déclencheur de restauration entre régions](/rest/api/backup/cross-region-restore/trigger) pour restaurer un élément dans la région secondaire.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.RecoveryServices/locations/{azureRegion}/backupCrossRegionRestore?api-version=2018-12-20
```

Le corps de la demande doit comprendre deux parties :

1. ***crossRegionRestoreAccessDetails** _ : collez le bloc _properties* de la réponse à la demande de l’API d’obtention de jeton d’accès effectuée à l’étape précédente pour remplir ce segment du corps de la demande.

1. ***restoreRequest** _ : pour remplir le segment _restoreRequest* du corps de la demande, vous devez transmettre l’ID de point de récupération obtenu précédemment, ainsi que l’ID Azure Resource Manager (ARM) de la machine virtuelle source et les détails du compte de stockage dans la région secondaire à utiliser comme emplacement intermédiaire. Pour effectuer une restauration de disque, spécifiez *RestoreDisks* comme type de récupération.

Voici un exemple de corps de demande pour restaurer les disques d’une machine virtuelle dans la région secondaire :

```json
 {
  "crossRegionRestoreAccessDetails": {
        "objectType": "CrrAccessToken",
        "accessTokenString": "<access-token-string>",
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "azurefiles",
        "resourceName": "azurefilesvault",
        "resourceId": "000000000000000000",
        "protectionContainerId": 000000,
        "recoveryPointId": "932879774590051503",
        "recoveryPointTime": "7/26/2021 3:35:36 PM",
        "containerName": "iaasvmcontainerv2;testRG1;testVM",
        "containerType": "IaasVMContainer",
        "backupManagementType": "AzureIaasVM",
        "datasourceType": "VM",
        "datasourceName": "testvm1234",
        "datasourceId": "000000000000000000",
        "datasourceContainerName": "iaasvmcontainerv2;testRG1;testVM",
        "coordinatorServiceStampUri": "https://pod01-coord1.eus.backup.windowsazure.com",
        "protectionServiceStampId": "00000000-0000-0000-0000-000000000000",
        "protectionServiceStampUri": "https://pod01-prot1h-int.eus.backup.windowsazure.com",
        "tokenExtendedInformation": "<IaaSVMRecoveryPointMetadataBase xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" i:type=\"IaaSVMRecoveryPointMetadata_V2015_09\" xmlns=\"http://windowscloudbackup.com/CloudCommon/V2011_09\"><MetadataVersion>V2015_09</MetadataVersion><ContainerType i:nil=\"true\" /><InstantRpGCId>ef4ab5a7-c2c0-4304-af80-af49f48af3d1;AzureBackup_testvm1234_932843259176972511;AzureBackup_20210726_033536;AzureBackupRG_eastus_1</InstantRpGCId><IsBlockBlobEnabled>true</IsBlockBlobEnabled><IsManagedVirtualMachine>true</IsManagedVirtualMachine><OriginalSAOption>false</OriginalSAOption><OsType>Windows</OsType><ReadMetadaFromConfigBlob i:nil=\"true\" /><RecoveryPointConsistencyType>CrashConsistent</RecoveryPointConsistencyType><RpDiskDetails i:nil=\"true\" /><SourceIaaSVMRPKeyAndSecret i:nil=\"true\" /><SourceIaaSVMStorageType>PremiumVMOnPartialPremiumStorage</SourceIaaSVMStorageType><VMSizeDescription>Standard_D2s_v3</VMSizeDescription><Zones xmlns:d2p1=\"http://schemas.microsoft.com/2003/10/Serialization/Arrays\" i:nil=\"true\" /></IaaSVMRecoveryPointMetadataBase>",
        "rpTierInformation": {
            "InstantRP": "Valid",
            "HardenedRP": "Valid"
        },
        "rpOriginalSAOption": false,
        "rpIsManagedVirtualMachine": true,
        "rpVMSizeDescription": "Standard_D2s_v3",
        "bMSActiveRegion": "EastUS"
    },
    "restoreRequest": {
        "affinityGroup": "",
        "createNewCloudService": false,
        "encryptionDetails": {
            "encryptionEnabled": false
        },
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "932879774590051503",
        "recoveryType": "RestoreDisks",
        "sourceResourceId":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG1/providers/Microsoft.Compute/virtualMachines/testVM",
        "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG1",
        "storageAccountId":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG1/providers/Microsoft.Storage/storageAccounts/testStorageAccount",
        "region": "westus",
        "affinityGroup": "",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "restoreDiskLunList": []
    }
}
```

Comme l’opération de restauration de la région primaire, il s’agit d’une opération asynchrone qui doit être [suivie séparément](/azure/backup/backup-azure-arm-userestapi-restoreazurevms#restore-response).



## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les API REST de la Sauvegarde Azure, voir les documents suivants :

- [API REST du fournisseur Azure Recovery Services](/rest/api/recoveryservices/)
- [Bien démarrer avec l’API REST Azure](/rest/api/azure/)
