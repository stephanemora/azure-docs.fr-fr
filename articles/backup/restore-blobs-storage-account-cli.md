---
title: Restaurer des blobs Azure via Azure CLI
description: Découvrez comment restaurer des objets blob Azure à n’importe quel point dans le temps à l’aide d’Azure CLI.
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: 620b98d40c66b8af2360559c6f71db5ef6c07114
ms.sourcegitcommit: 75ad40bab1b3f90bb2ea2a489f8875d4b2da57e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2021
ms.locfileid: "113644308"
---
# <a name="restore-azure-blobs-to-point-in-time-using-azure-cli"></a>Restaurer des blobs Azure à un point dans le temps à l’aide d’Azure CLI

Cet article explique comment restaurer des [objets blob](blob-backup-overview.md) à n’importe quel point dans le temps à l’aide de la sauvegarde Azure.

> [!IMPORTANT]
> La prise en charge de la sauvegarde et la restauration des blobs Azure via l’interface CLI est en préversion et disponible en tant qu’extension dans la version 2.15.0 d’Az et versions ultérieures. L’extension est automatiquement installée lorsque vous exécutez les commandes **az dataprotection**. [Apprenez-en davantage](/cli/azure/azure-cli-extensions-overview) sur les extensions.

> [!IMPORTANT]
> Avant de restaurer des blobs Azure à l’aide de Sauvegarde Azure, consultez les [points importants](blob-restore.md#before-you-start).

Dans cet article, vous allez apprendre à :

- Restaurer des blobs Azure à un point dans le temps

- suivre l’état de l’opération de restauration.

Nous allons faire référence au coffre de sauvegarde existant _TestBkpVault_ du groupe de ressources _testBkpVaultRG_ dans les exemples.

## <a name="restoring-azure-blobs-within-a-storage-account"></a>Restauration de blobs Azure dans un compte de stockage

### <a name="fetching-the-valid-time-range-for-restore"></a>Récupération de l’intervalle de temps valide pour la restauration

Étant donné que la sauvegarde opérationnelle pour les objets blob est continue, il n’existe aucun point distinct à partir duquel effectuer la restauration. Au lieu de cela, nous devons extraire la plage horaire valide dans laquelle les objets blob peuvent être restaurés à n’importe quel point dans le temps. Dans cet exemple, nous allons vérifier les plages horaires valides pour la restauration au cours des 30 derniers jours.

Tout d’abord, nous devons extraire l’ID d’instance de sauvegarde approprié. Répertoriez toutes les instances de sauvegarde dans un coffre à l’aide de la commande [az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list), puis récupérez (fetch) l’instance appropriée à l’aide de la commande [az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_show). Pour les scénarios à grande échelle, vous pouvez également répertorier les instances de sauvegarde dans les coffres et les abonnements à l’aide de la commande [az dataprotection backup-instance list-from-resourcegraph](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph).

```azurecli-interactive
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureBlob --datasource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA"

[
  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
    "extendedLocation": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
    "identity": null,
    "kind": "",
    "location": "",
    "managedBy": "",
    "name": "CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
    "plan": null,
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
        "resourceLocation": "southeastasia",
        "resourceName": "CLITestSA",
        "resourceType": "Microsoft.Storage/storageAccounts",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA"
      },
      "dataSourceProperties": null,
      "dataSourceSetInfo": null,
      "datasourceAuthCredentials": null,
      "friendlyName": "CLITestSA",
      "objectType": "BackupInstance",
      "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/BlobBackup-Policy",
        "policyParameters": {
          "dataStoreParametersList": [
            {
              "dataStoreType": "OperationalStore",
              "objectType": "AzureOperationalStoreParameters",
              "resourceGroupId": ""
            }
          ]
        },
        "policyVersion": ""
      },
      "protectionErrorDetails": null,
      "protectionStatus": {
        "errorDetails": null,
        "status": "ProtectionConfigured"
      },
      "provisioningState": "Succeeded"
    },
    "protectionState": "ProtectionConfigured",
    "resourceGroup": "rg-bv",
    "sku": null,
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx",
    "tags": null,
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
    "type": "microsoft.dataprotection/backupvaults/backupinstances",
    "vaultName": "TestBkpVault",
    "zones": null
  }
]
```

Une fois l’instance identifiée, extrayez la plage de récupération appropriée à l’aide de la commande [az dataprotection restorable-time-range find](/cli/azure/dataprotection/restorable-time-range?view=azure-cli-latest&preserve-view=true#az_dataprotection_restorable_time_range_find).

```azurecli-interactive
az dataprotection restorable-time-range find --start-time 2021-05-30T00:00:00 --end-time 2021-05-31T00:00:00 --source-data-store-type OperationalStore -g testBkpVaultRG --vault-name TestBkpVault --backup-instances CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036

{
  "id": "CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
  "name": null,
  "properties": {
    "objectType": "AzureBackupFindRestorableTimeRangesResponse",
    "restorableTimeRanges": [
      {
        "endTime": "2021-05-31T00:00:00.0000000Z",
        "objectType": "RestorableTimeRange",
        "startTime": "2021-06-13T18:53:44.4465407Z"
      }
    ]
  },
  "systemData": null,
  "type": "Microsoft.DataProtection/backupVaults/backupInstances/findRestorableTimeRanges"
}
```

### <a name="preparing-the-restore-request"></a>Préparation de la demande de restauration

Une fois le point dans le temps à restaurer résolu, il existe plusieurs options de restauration.

#### <a name="restoring-all-the-blobs-to-a-point-in-time"></a>Restauration de tous les objets blob à un point dans le temps

Cette option vous permet de restaurer tous les objets blob de blocs dans le compte de stockage à l’instant dans le passé sélectionné. La restauration des comptes de stockage contenant de grandes quantités de données ou témoin d’une attrition élevée peut prendre plus de temps. Pour restaurer tous les objets blob de blocs, utilisez la commande [az dataprotection backup-instance restore initialize-for-data-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery). L’emplacement de restauration et l’ID de ressource cible seront identiques à ceux du compte de stockage protégé.

```azurecli-interactive
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --target-resource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA" --point-in-time 2021-06-02T18:53:44.4465407Z

{
  "object_type": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "recovery_point_time": "2021-06-02T18:53:44.4465407Z.0000000Z",
  "restore_target_info": {
    "datasource_info": {
      "datasource_type": "Microsoft.Storage/storageAccounts/blobServices",
      "object_type": "Datasource",
      "resource_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
      "resource_location": "southeastasia",
      "resource_name": "CLITestSA",
      "resource_type": "Microsoft.Storage/storageAccounts",
      "resource_uri": ""
    },
    "object_type": "RestoreTargetInfo",
    "recovery_option": "FailIfExists",
    "restore_location": "southeastasia"
  },
  "source_data_store_type": "OperationalStore"
}


az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --target-resource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA" --point-in-time 2021-06-02T18:53:44.4465407Z > restore.json
```

#### <a name="restoring-selected-containers"></a>Restauration des conteneurs sélectionnés

Cette option vous permet de rechercher et sélectionner jusqu’à 10 conteneurs à restaurer. Pour restaurer les conteneurs sélectionnés, utilisez la commande [az dataprotection backup-instance restore initialize-for-item-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_item_recovery).

```azurecli-interactive
az dataprotection backup-instance restore initialize-for-item-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --backup-instance-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036" --point-in-time 2021-06-02T18:53:44.4465407Z --container-list container1 container2

{
  "object_type": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "recovery_point_time": "2021-06-02T18:53:44.4465407Z.0000000Z",
  "restore_target_info": {
    "datasource_info": {
      "datasource_type": "Microsoft.Storage/storageAccounts/blobServices",
      "object_type": "Datasource",
      "resource_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
      "resource_location": "southeastasia",
      "resource_name": "CLITestSA",
      "resource_type": "Microsoft.Storage/storageAccounts",
      "resource_uri": ""
    },
    "object_type": "ItemLevelRestoreTargetInfo",
    "recovery_option": "FailIfExists",
    "restore_criteria": [
      {
        "max_matching_value": "container1-0",
        "min_matching_value": "container1",
        "object_type": "RangeBasedItemLevelRestoreCriteria"
      },
      {
        "max_matching_value": "container2-0",
        "min_matching_value": "container2",
        "object_type": "RangeBasedItemLevelRestoreCriteria"
      }
    ],
    "restore_location": "southeastasia"
  },
  "source_data_store_type": "OperationalStore"
}


az dataprotection backup-instance restore initialize-for-item-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --backup-instance-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036" --point-in-time 2021-06-02T18:53:44.4465407Z --container-list container1 container2 > restore.json
```

#### <a name="restoring-containers-using-a-prefix-match"></a>Restauration de conteneurs à l’aide d’une correspondance de préfixe

Cette option vous permet de restaurer un sous-ensemble d’objets blob à l’aide d’une correspondance de préfixe. Vous pouvez spécifier jusqu’à 10 plages lexicographiques de blobs dans un ou plusieurs conteneurs pour ramener ces blobs à l’état qui était le leur à un instant donné dans le passé. Voici quelques points à prendre en compte :

- Vous pouvez utiliser une barre oblique (/) pour délimiter le nom de conteneur du préfixe de blob.
- Le début de la plage spécifiée est inclusif mais la plage spécifiée est exclusive.

[En savoir plus](blob-restore.md#use-prefix-match-for-restoring-blobs) sur l’utilisation de préfixes pour restaurer des plages d’objets blob.

Pour restaurer les conteneurs sélectionnés, utilisez la commande [az dataprotection backup-instance restore initialize-for-item-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_item_recovery).

```azurecli-interactive
az dataprotection backup-instance restore initialize-for-item-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --backup-instance-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036" --point-in-time 2021-06-02T18:53:44.4465407Z --from-prefix-pattern container1/text1 container2/text4 --to-prefix-pattern container1/text4 container2/text41

{
  "object_type": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "recovery_point_time": "2021-06-02T18:53:44.4465407Z.0000000Z",
  "restore_target_info": {
    "datasource_info": {
      "datasource_type": "Microsoft.Storage/storageAccounts/blobServices",
      "object_type": "Datasource",
      "resource_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
      "resource_location": "southeastasia",
      "resource_name": "CLITestSA",
      "resource_type": "Microsoft.Storage/storageAccounts",
      "resource_uri": ""
    },
    "object_type": "ItemLevelRestoreTargetInfo",
    "recovery_option": "FailIfExists",
    "restore_criteria": [
       {
        "max_matching_value": "container1/text4",
        "min_matching_value": "container1/text1",
        "object_type": "RangeBasedItemLevelRestoreCriteria"
      },
      {
        "max_matching_value": "container2/text41",
        "min_matching_value": "container2/text4",
        "object_type": "RangeBasedItemLevelRestoreCriteria"
      }
    ],
    "restore_location": "southeastasia"
  },
  "source_data_store_type": "OperationalStore"
}



az dataprotection backup-instance restore initialize-for-item-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --backup-instance-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036" --point-in-time 2021-06-02T18:53:44.4465407Z --from-prefix-pattern container1/text1 container2/text4 --to-prefix-pattern container1/text4 container2/text41 > restore.json
```

### <a name="trigger-the-restore"></a>Déclencher la restauration

Utilisez la commande [az dataprotection backup-instance restore trigger](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_trigger) pour déclencher la restauration avec la demande préparée ci-dessus.

```azurecli-interactive
az dataprotection backup-instance restore trigger -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036 --restore-request-object restore.json
```

## <a name="tracking-job"></a>Suivi des travaux

Effectuez le suivi de tous les travaux à l’aide de la commande [az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list). Vous pouvez répertorier tous les travaux et extraire le détail d’un travail particulier.

Vous pouvez également utiliser la commande pour effectuer le suivi de tous les travaux dans l’ensemble des coffres Sauvegarde. Utilisez la commande [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) pour obtenir le travail approprié qui peut se trouver dans n’importe quel coffre de sauvegarde.

```azurepowershell-interactive
az dataprotection job list-from-resourcegraph --datasource-type AzureBlob --operation Restore
```

## <a name="next-steps"></a>Étapes suivantes

[Matrice de prise en charge pour la sauvegarde des objets blob Azure](blob-backup-support-matrix.md)
