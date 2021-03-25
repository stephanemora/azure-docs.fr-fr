---
title: Mettre à jour la stratégie existante de sauvegarde des machines virtuelles à l’aide de CLI
description: Découvrez comment mettre à jour la stratégie existante de sauvegarde des machines virtuelles à l’aide d’Azure CLI.
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: 33083d6585d2b9296cd184ba258b8d2143d685b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98728576"
---
# <a name="update-the-existing-vm-backup-policy-using-cli"></a>Mettre à jour la stratégie existante de sauvegarde des machines virtuelles à l’aide de CLI

Vous pouvez utiliser Azure CLI pour mettre à jour une stratégie existante de sauvegarde des machines virtuelles. Cet article explique comment exporter la stratégie existante dans un fichier JSON, modifier le fichier, puis utiliser Azure CLI pour mettre à jour la stratégie avec la stratégie modifiée.

## <a name="modify-an-existing-policy"></a>Modifier une stratégie existante

Pour modifier une stratégie existante de sauvegarde des machines virtuelles, procédez comme suit :

1. Exécutez la commande [az backup policy show](/cli/azure/backup/policy#az_backup_policy_show) pour récupérer les détails de la stratégie que vous souhaitez mettre à jour.

    Exemple :

    ```azurecli
    az backup policy show --name testing123 --resource-group rg1234 --vault-name testvault
    ```

    L’exemple ci-dessus montre les détails d’une stratégie de machine virtuelle portant le nom *testing123*.

    Sortie :

    ```json
    {
    "eTag": null,
    "id": "/Subscriptions/efgsf-123-test-subscription/resourceGroups/rg1234/providers/Microsoft.RecoveryServices/vaults/testvault/backupPolicies/testing123",
    "location": null,
    "name": "testing123",
    "properties": {
        "backupManagementType": "AzureIaasVM",
        "instantRpDetails": {
        "azureBackupRgNamePrefix": null,
        "azureBackupRgNameSuffix": null
        },
        "instantRpRetentionRangeInDays": 2,
        "protectedItemsCount": 0,
        "retentionPolicy": {
        "dailySchedule": {
            "retentionDuration": {
            "count": 180,
            "durationType": "Days"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "monthlySchedule": null,
        "retentionPolicyType": "LongTermRetentionPolicy",
        "weeklySchedule": {
            "daysOfTheWeek": [
            "Sunday"
            ],
            "retentionDuration": {
            "count": 30,
            "durationType": "Weeks"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "yearlySchedule": null
        },
        "schedulePolicy": {
        "schedulePolicyType": "SimpleSchedulePolicy",
        "scheduleRunDays": null,
        "scheduleRunFrequency": "Daily",
        "scheduleRunTimes": [
            "2020-08-03T04:30:00+00:00"
        ],
        "scheduleWeeklyFrequency": 0
        },
        "timeZone": "UTC"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
    }
    ```

1. Enregistrez la sortie ci-dessus dans un fichier .json. Par exemple, enregistrons-la sous le nom *Policy.json*.
1. Mettez à jour le fichier JSON selon vos besoins et enregistrez les modifications.

    Exemple :  Pour mettre à jour la rétention hebdomadaire à 60 jours, mettez à jour la section suivante du fichier JSON en modifiant le nombre par 60.

    ```json
            "retentionDuration": {
          "count": 60,
          "durationType": "Weeks"
        }

    ```

1. Enregistrez les modifications.
1. Exécutez la commande [az backup policy set](/cli/azure/backup/policy#az_backup_policy_set) et transmettez le chemin d’accès complet du fichier JSON mis à jour en tant que valeur du paramètre **--policy**.

    ```azurecli
    az backup policy set --resource-group rg1234 --vault-name testvault --policy C:\temp2\Policy.json --name testing123
    ```

>[!NOTE]
>Vous pouvez également récupérer l’exemple de stratégie JSON en exécutant la commande [az backup policy get-default-for-vm](/cli/azure/backup/policy#az_backup_policy_get_default_for_vm).

## <a name="next-steps"></a>Étapes suivantes

- [Gérer les sauvegardes de machines virtuelles Azure avec le service Sauvegarde Azure](backup-azure-manage-vms.md)