---
title: Restaurer des disques managés Azure via Azure CLI
description: Découvrez comment restaurer des disques managés Azure à l’aide d’Azure CLI.
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: 87ca3597b7ad73aa1ac0ba5cff6c75b5ec880750
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114287503"
---
# <a name="restore-azure-managed-disks-using-azure-cli"></a>Restaurer des Disques managés Azure à l’aide de l’interface Azure CLI

Cet article explique comment restaurer des [Disques managés Azure](../virtual-machines/managed-disks-overview.md) à partir d’un point de restauration créé par la Sauvegarde Azure à l’aide d’Azure CLI.

> [!IMPORTANT]
> La prise en charge de la sauvegarde et de la restauration des disques Azure managés via l’interface CLI est en préversion et elle disponible en tant qu’extension dans la version Az 2.15.0 et versions ultérieures. L’extension est automatiquement installée quand vous exécutez les commandes **az dataprotection**. [Apprenez-en davantage](/cli/azure/azure-cli-extensions-overview) sur les extensions.

Actuellement, l’option OLR (Original-Location Recovery) de restauration par remplacement du disque source existant à partir duquel les sauvegardes ont été effectuées n’est pas prise en charge. Vous pouvez restaurer à partir d’un point de récupération pour créer un nouveau disque dans le même groupe de ressources que le disque source ou dans tout autre groupe de ressources. C’est ce qu’on appelle une Récupération à un autre emplacement (ALR).

Dans cet article, vous allez apprendre à :

- effectuer une restauration pour créer un disque ;

- suivre l’état de l’opération de restauration.

Nous allons faire référence au coffre de sauvegarde existant _TestBkpVault_ du groupe de ressources _testBkpVaultRG_ dans les exemples.

## <a name="restore-to-create-a-new-disk"></a>Restauration visant à créer un disque

### <a name="setting-up-permissions"></a>Configurer les autorisations

Le coffre Sauvegarde utilise l’identité managée pour accéder à d’autres ressources Azure. Il est nécessaire, pour effectuer une restauration à partir d’une sauvegarde, que l’identité managée du coffre Sauvegarde dispose d’un ensemble d’autorisations sur le groupe de ressources dans lequel le disque doit être restauré.

Le coffre Sauvegarde utilise une identité managée affectée par le système, limitée à une par ressource et liée au cycle de vie de celle-ci. Pour accorder des autorisations à l’identité managée, vous pouvez utiliser le contrôle d’accès en fonction du rôle (RBAC) Azure. L’identité managée est un principal de service d’un type spécial qui ne peut être utilisé qu’avec des ressources Azure. Pour plus d’informations, consultez [Identités managées](../active-directory/managed-identities-azure-resources/overview.md).

Affectez les autorisations appropriées à l’identité managée affectée par le système pour le coffre-fort correspondant au groupe de ressources cible où les disques vont être restaurés/créés, comme indiqué [ici](restore-managed-disks.md#restore-to-create-a-new-disk).

### <a name="fetching-the-relevant-recovery-point"></a>Extraction du point de récupération approprié

Répertoriez toutes les instances de sauvegarde dans un coffre à l’aide de la commande [az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list), puis récupérez (fetch) l’instance appropriée à l’aide de la commande [az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_show). Pour les scénarios à grande échelle, vous pouvez également répertorier les instances de sauvegarde dans les coffres et les abonnements à l’aide de [az dataprotection backup-instance list-from-resourcegraph](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph).

```azurecli-interactive
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureDisk --datasource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk


[
  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
    "extendedLocation": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
    "identity": null,
    "kind": "",
    "location": "",
    "managedBy": "",
    "name": "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
    "plan": null,
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.Compute/disks",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
        "resourceLocation": "westus",
        "resourceName": "CLITestDisk",
        "resourceType": "Microsoft.Compute/disks",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk"
      },
      "dataSourceProperties": null,
      "dataSourceSetInfo": null,
      "datasourceAuthCredentials": null,
      "friendlyName": "CLITestDisk",
      "objectType": "BackupInstance",
      "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupPolicies/DiskPolicy",
        "policyParameters": {
          "dataStoreParametersList": [
            {
              "dataStoreType": "OperationalStore",
              "objectType": "AzureOperationalStoreParameters",
              "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/snapshotrg"
            }
          ]
        },
        "policyVersion": null
      },
      "protectionErrorDetails": null,
      "protectionStatus": {
        "errorDetails": null,
        "status": "ProtectionConfigured"
      },
      "provisioningState": "Succeeded"
    },
    "protectionState": "ProtectionConfigured",
    "resourceGroup": "testBkpVaultRG",
    "sku": null,
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "tags": null,
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "microsoft.dataprotection/backupvaults/backupinstances",
    "vaultName": "TestBkpVault",
    "zones": null
  }
]


```

Une fois l’instance identifiée, extrayez le point de récupération approprié à l’aide de la commande [az dataprotection recovery-point list](/cli/azure/dataprotection/recovery-point?view=azure-cli-latest&preserve-view=true#az_dataprotection_recovery_point_list).

```azurecli-interactive
az dataprotection recovery-point list --backup-instance-name diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 -g testBkpVaultRG --vault-name TestBkpVault

{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166/recoveryPoints/5081ad8f1e6c4548ae89536d0d45c493",
"name": "5081ad8f1e6c4548ae89536d0d45c493",
"properties": {
"friendlyName": "0f598ced-cbfe-4169-b962-ee94b0210490",
"objectType": "AzureBackupDiscreteRecoveryPoint",
"policyName": "DiskPSPolicy2",
"policyVersion": null,
"recoveryPointDataStoresDetails": [
{
"creationTime": "2021-06-08T09:01:57.708319+00:00",
"expiryTime": "2021-06-15T09:01:57.708319+00:00",
"id": "c2ad4629-f2ef-49b6-b3f8-50f3eb5404f4",
"metaData": null,
"rehydrationExpiryTime": null,
"rehydrationStatus": null,
"state": "COMMITTED",
"type": "OperationalStore",
"visible": true
}
],
"recoveryPointId": "5081ad8f1e6c4548ae89536d0d45c493",
"recoveryPointTime": "2021-06-08T09:01:57.708319+00:00",
"recoveryPointType": "Incremental",
"retentionTagName": "Default",
"retentionTagVersion": "637553616953961153"
},
"resourceGroup": "testBkpVaultRG",
"systemData": null,
"type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints"
},
{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166/recoveryPoints/039322cc563049bcbdb77bd695d4c02c",
"name": "039322cc563049bcbdb77bd695d4c02c",
"properties": {
"friendlyName": "af6512b6-aa38-4966-b8e1-660c4eccdc0d",
"objectType": "AzureBackupDiscreteRecoveryPoint",
"policyName": "DiskPSPolicy2",
"policyVersion": null,
"recoveryPointDataStoresDetails": [
{
"creationTime": "2021-06-08T05:01:55.426507+00:00",
"expiryTime": "2021-06-15T05:01:55.426507+00:00",
"id": "c2ad4629-f2ef-49b6-b3f8-50f3eb5404f4",
"metaData": null,
"rehydrationExpiryTime": null,
"rehydrationStatus": null,
"state": "COMMITTED",
"type": "OperationalStore",
"visible": true
}
],
"recoveryPointId": "039322cc563049bcbdb77bd695d4c02c",
"recoveryPointTime": "2021-06-08T05:01:55.426507+00:00",
"recoveryPointType": "Incremental",
"retentionTagName": "Default",
"retentionTagVersion": "637553616953961153"
},
"resourceGroup": "testBkpVaultRG",
"systemData": null,
"type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints"
}
]
```

Par exemple, la requête ci-dessous retourne le dernier point de récupération.

```azurecli-interactive
az dataprotection recovery-point list --backup-instance-name diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 -g testBkpVaultRG --vault-name TestBkpVault --query "[0].id"

"/subscriptions/62b829ee-7936-40c9-a1c9-47a93f9f3965/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/sarath-vault/backupInstances/clitest-clitest-3165cfe7-a932-11eb-9d24-9cfce85d4fae/recoveryPoints/5081ad8f1e6c4548ae89536d0d45c493"
```

### <a name="preparing-the-restore-request"></a>Préparation de la demande de restauration

Construisez l’ID ARM du disque à créer avec le groupe de ressources cible, auquel des autorisations ont été affectées comme indiqué [ci-dessus](#setting-up-permissions), et le nom de disque nécessaire. Nous allons utiliser un exemple de disque nommé _CLITestDisk2_, sous un groupe de ressources _targetrg_ dans un autre abonnement.

```azurecli-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/CLITestDisk2
```

Utilisez la commande [az dataprotection backup-instance restore initialize-for-data-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery) pour préparer la demande de restauration avec tous les détails appropriés.

```azurecli-interactive
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureDisk --restore-location southeastasia --source-datastore OperationalStore --recovery-point-id /subscriptions/62b829ee-7936-40c9-a1c9-47a93f9f3965/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/sarath-vault/backupInstances/clitest-clitest-3165cfe7-a932-11eb-9d24-9cfce85d4fae/recoveryPoints/5081ad8f1e6c4548ae89536d0d45c493 --target-resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/CLITestDisk2 > restore.json
```

```json
{
  "object_type": "AzureBackupRecoveryPointBasedRestoreRequest",
  "recovery_point_id": "77594ce0470849e79b86a6875b726dca",
  "restore_target_info": {
    "datasource_info": {
      "datasource_type": "Microsoft.Compute/disks",
      "object_type": "Datasource",
      "resource_id": "//subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/CLITestDisk2",
      "resource_location": "southeastasia",
      "resource_name": "CLITestDisk2",
      "resource_type": "Microsoft.Compute/disks",
      "resource_uri": ""
    },
    "object_type": "RestoreTargetInfo",
    "recovery_option": "FailIfExists",
    "restore_location": "southeastasia"
  },
  "source_data_store_type": "OperationalStore"
}

```

Vous pouvez également vérifier si le fichier JSON est correctement créé lors de la création de nouvelles ressources à l’aide de la commande [az dataprotection backup-instance validate-for-restore](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_validate_for_restore).

```azurecli-interactive
az dataprotection backup-instance validate-for-restore -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 --restore-request-object restore.json
```

### <a name="trigger-the-restore"></a>Déclencher la restauration

Utilisez la commande [az dataprotection backup-instance restore trigger](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_trigger) pour déclencher la restauration avec la demande préparée ci-dessus.

```azurecli-interactive
az dataprotection backup-instance restore trigger -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 --parameters restore.json
```

## <a name="tracking-job"></a>Suivi des travaux

Effectuez le suivi de tous les travaux à l’aide de la commande [az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list). Vous pouvez répertorier tous les travaux et extraire le détail d’un travail particulier.

Vous pouvez également utiliser la commande pour effectuer le suivi de tous les travaux dans l’ensemble des coffres Sauvegarde. Utilisez la commande [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) pour obtenir le travail approprié, qui peut se trouver dans n’importe quel coffre Sauvegarde.

```azurepowershell-interactive
az dataprotection job list-from-resourcegraph --datasource-type AzureDisk --operation Restore
```

## <a name="next-steps"></a>Étapes suivantes

[FAQ sur la sauvegarde de disque Azure](./disk-backup-faq.yml)