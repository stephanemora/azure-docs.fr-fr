---
title: Sauvegarder des disques managés Azure à l’aide de l’interface Azure CLI
description: Découvrez comment sauvegarder des disques managés Azure avec l’interface Azure CLI.
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: ce1e4b3f88e844165581c95f74955de04686855b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659690"
---
# <a name="back-up-azure-managed-disks-using-azure-cli"></a>Sauvegarder des disques managés Azure à l’aide de l’interface Azure CLI

Cet article explique comment sauvegarder un [disque managé Azure](../virtual-machines/managed-disks-overview.md) avec l’interface Azure CLI.

> [!IMPORTANT]
> La prise en charge de la sauvegarde et de la restauration des disques Azure managés via l’interface CLI est en préversion et elle disponible en tant qu’extension dans la version Az 2.15.0 et versions ultérieures. L’extension est automatiquement installée quand vous exécutez les commandes **az dataprotection**. [Apprenez-en davantage](/cli/azure/azure-cli-extensions-overview) sur les extensions.

Dans cet article, vous allez apprendre à :

- Créer un coffre de sauvegarde

- Créer une stratégie de sauvegarde

- Configurer la sauvegarde d’un disque Azure

- Exécuter un travail de sauvegarde à la demande

Pour plus d’informations sur la disponibilité par région, les scénarios pris en charge et les limitations de la sauvegarde de disque Azure, consultez la [matrice de prise en charge](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Créer un coffre de sauvegarde

Un coffre Sauvegarde est une entité de stockage dans Azure qui stocke les données de sauvegarde de diverses charges de travail plus récentes prises en charge par la Sauvegarde Azure, notamment des serveurs Azure Database pour PostgreSQL, des objets blobs dans un compte de stockage et des disques Azure. Les coffres de sauvegarde facilitent l’organisation de vos données de sauvegarde tout en réduisant le temps nécessaire à leur gestion. Les coffres Sauvegarde sont basés sur le modèle Azure Resource Manager, qui fournit des fonctionnalités améliorées pour sécuriser les données de sauvegarde.

Avant de créer un coffre Sauvegarde, choisissez la redondance de stockage des données dans le coffre. Ensuite, procédez à la création du coffre Sauvegarde avec cette redondance de stockage et l’emplacement. Dans cet article, nous allons créer un coffre Sauvegarde _TestBkpVault_ dans la région _westus_ sous le groupe de ressources _testBkpVaultRG_. Utilisez la commande [az dataprotection vault create](/cli/azure/dataprotection/backup-vault?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_vault_create) pour créer un coffre Sauvegarde. Pour plus d’informations, consultez [Création d’un coffre Sauvegarde](./backup-vault-overview.md#create-a-backup-vault).

```azurecli-interactive
az dataprotection backup-vault create -g testBkpVaultRG --vault-name TestBkpVault -l westus --type SystemAssigned --storage-settings datastore-type="VaultStore" type="LocallyRedundant"

{
  "eTag": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault",
  "identity": {
    "principalId": "2ca1d5f7-38b3-4b61-aa45-8147d7e0edbc",
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "SystemAssigned"
  },
  "location": "westus",
  "name": "TestBkpVault",
  "properties": {
    "provisioningState": "Succeeded",
    "storageSettings": [
      {
        "datastoreType": "VaultStore",
        "type": "LocallyRedundant"
      }
    ]
  },
  "resourceGroup": "testBkpVaultRG",
  "systemData": null,
  "tags": null,
  "type": "Microsoft.DataProtection/backupVaults"
}
```

Après la création du coffre, nous allons créer une stratégie de sauvegarde pour protéger les disques Azure.

## <a name="create-a-backup-policy"></a>Créer une stratégie de sauvegarde

Pour comprendre les composants internes d’une stratégie de sauvegarde pour la sauvegarde des disques Azure, récupérez le modèle de stratégie à l’aide de la commande [az dataprotection backup-policy get-default-policy-template](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_get_default_policy_template). Cette commande retourne un modèle de stratégie par défaut pour un type de source de données donné. Utilisez ce modèle de stratégie pour créer une nouvelle stratégie.

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureDisk

{
  "datasourceTypes": [
    "Microsoft.Compute/disks"
  ],
  "name": "DiskPolicy",
  "objectType": "BackupPolicy",
  "policyRules": [
    {
      "backupParameters": {
        "backupType": "Incremental",
        "objectType": "AzureBackupParams"
      },
      "dataStore": {
        "dataStoreType": "OperationalStore",
        "objectType": "DataStoreInfoBase"
      },
      "name": "BackupHourly",
      "objectType": "AzureBackupRule",
      "trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2020-04-05T13:00:00+00:00/PT4H"
          ]
        },
        "taggingCriteria": [
          {
            "isDefault": true,
            "tagInfo": {
              "id": "Default_",
              "tagName": "Default"
            },
            "taggingPriority": 99
          }
        ]
      }
    },
    {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P7D",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "OperationalStore",
            "objectType": "DataStoreInfoBase"
          }
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
  ]
}

```

Le modèle de stratégie est constitué d’un déclencheur (qui détermine le déclenchement de la sauvegarde) et d’un cycle de vie (qui décide quand supprimer/copier/déplacer la sauvegarde). Dans la sauvegarde des disques Azure, les valeurs par défaut pour le déclencheur sont un déclenchement planifié toutes les quatre heures (PT4H) et une rétention de chaque sauvegarde de sept jours.

**Déclencheur planifié :**

```json
"trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2020-04-05T13:00:00+00:00/PT4H"
          ]
        }

```

**Cycle de vie de rétention par défaut :**

```json
"lifecycles": [
        {
          "deleteAfter": {
            "duration": "P7D",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "OperationalStore",
            "objectType": "DataStoreInfoBase"
          }
        }
      ]
```

La sauvegarde de disque Azure propose plusieurs sauvegardes par jour. Si vous avez besoin de sauvegardes plus fréquentes, choisissez la fréquence de sauvegarde **Horaire**, qui offre la possibilité d’effectuer des sauvegardes à intervalles de 4, 6, 8 ou 12 heures. Les sauvegardes sont planifiées en fonction de l’intervalle **Temps** sélectionné.

Par exemple, si vous sélectionnez **Toutes les 4 heures**, les sauvegardes sont effectuées à intervalles de 4 heures environ, de façon à être réparties équitablement sur la journée. Si une sauvegarde par jour suffit, choisissez la fréquence de sauvegarde **Quotidienne**. Vous pouvez alors spécifier l’heure à laquelle s’effectuent vos sauvegardes.

>[!IMPORTANT]
>L’heure de la journée indique l’heure de début de la sauvegarde, et pas l’heure de fin.

Le temps nécessaire pour effectuer l’opération de sauvegarde dépend de différents facteurs, notamment de la taille du disque et du taux d’attrition entre les sauvegardes consécutives. Toutefois, la Sauvegarde de disque Azure est une sauvegarde sans agent qui utilise des [instantanés incrémentiels](../virtual-machines/disks-incremental-snapshots.md) sans avoir d’impact sur le niveau de performance des applications de production.

   >[!NOTE]
   >Il est possible que le coffre sélectionné dispose actuellement du paramètre de redondance globale. Toutefois, la sauvegarde de disque Azure prend en charge uniquement le magasin de données d’instantanés. Toutes les sauvegardes sont stockées dans l’un des groupes de ressources de l’abonnement. Elles ne sont pas copiées dans le stockage du coffre Sauvegarde.

Pour plus d’informations sur la création de stratégies, reportez-vous au document sur la [stratégie de sauvegarde de disque Azure](backup-managed-disks.md#create-backup-policy).

Une fois le modèle téléchargé en tant que fichier JSON, vous pouvez le modifier pour la planification et la rétention selon vos besoins. Créez ensuite une nouvelle stratégie avec le fichier JSON obtenu. Si vous souhaitez modifier la fréquence horaire ou la période de rétention, utilisez les commandes [az dataprotection backup-policy trigger set](/cli/azure/dataprotection/backup-policy/trigger?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_trigger_set) et/ou [az dataprotection backup-policy retention-rule set](/cli/azure/dataprotection/backup-policy/retention-rule?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_retention_rule_set). Une fois que la stratégie JSON a toutes les valeurs souhaitées, passez à la création d’une nouvelle stratégie à partir de l’objet de stratégie à l’aide de la commande [az dataprotection backup-policy create](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_create).

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureDisk > policy.json
az dataprotection backup-policy create -g testBkpVaultRG --vault-name TestBkpVault -n mypolicy --policy policy.json

{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/mypolicy",
"name": "mypolicy",
"properties": {
"datasourceTypes": [
"Microsoft.Compute/disks"
],
"objectType": "BackupPolicy",
"policyRules": [
{
"backupParameters": {
"backupType": "Incremental",
"objectType": "AzureBackupParams"
},
"dataStore": {
"dataStoreType": "OperationalStore",
"objectType": "DataStoreInfoBase"
},
"name": "BackupHourly",
"objectType": "AzureBackupRule",
"trigger": {
"objectType": "ScheduleBasedTriggerContext",
"schedule": {
"repeatingTimeIntervals": [
"R/2020-04-05T13:00:00+00:00/PT4H"
]
},
"taggingCriteria": [
{
"criteria": null,
"isDefault": true,
"tagInfo": {
"eTag": null,
"id": "Default_",
"tagName": "Default"
},
"taggingPriority": 99
}
]
}
},
{
"isDefault": true,
"lifecycles": [
{
"deleteAfter": {
"duration": "P7D",
"objectType": "AbsoluteDeleteOption"
},
"sourceDataStore": {
"dataStoreType": "OperationalStore",
"objectType": "DataStoreInfoBase"
},
"targetDataStoreCopySettings": null
}
],
"name": "Default",
"objectType": "AzureRetentionRule"
}
]
},
"resourceGroup": "testBkpVaultRG",
"systemData": null,
"type": "Microsoft.DataProtection/backupVaults/backupPolicies"
}
```

## <a name="configure-backup"></a>Configurer la sauvegarde

Une fois le coffre et la stratégie créés, il y a trois points critiques que l’utilisateur doit prendre en compte pour protéger un disque Azure.

### <a name="key-entities-involved"></a>Entités clés impliquées

#### <a name="disk-to-be-protected"></a>Disque à protéger

Récupérez (fetch) l’ID ARM et l’emplacement du disque à protéger. Il s’agit de l’identificateur du disque. Nous allons utiliser un exemple de disque nommé _CLITestDisk_, sous un groupe de ressources _diskrg_ dans un autre abonnement.

```azurecli-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk"
```

#### <a name="snapshot-resource-group"></a>Groupe de ressources d'instantanés

Les instantanés de disque sont stockés dans l’un des groupes de ressources de votre abonnement. En règle générale, nous conseillons de créer un groupe de ressources dédié comme magasin de stockage d’instantanés utilisable par le service Sauvegarde Azure. Cela permet en effet de restreindre les autorisations d’accès sur le groupe de ressources, garantissant ainsi la sécurité et la facilité de gestion des données de sauvegarde. Notez l’ID ARM du groupe de ressources dans lequel vous souhaitez placer les instantanés de disque

```azurecli-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>Archivage de sauvegarde

Les coffres Sauvegarde requièrent des autorisations sur le disque et le groupe de ressources d’instantanés pour pouvoir déclencher des instantanés et gérer leur cycle de vie. L’identité managée affectée par le système du coffre est utilisée pour affecter de telles autorisations. Utilisez la commande [az dataprotection backup-vault update](/cli/azure/dataprotection/backup-vault?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_vault_update) pour activer l’identité managée affectée par le système pour le coffre Recovery Services.

```azurecli-interactive
az dataprotection backup-vault update -g testBkpVaultRG --vault-name TestBkpVault --type SystemAssigned
```

### <a name="assign-permissions"></a>Attribuer des autorisations

Vous devez affecter quelques autorisations via RBAC au coffre (représenté par le fichier MSI de coffre) et le disque approprié et/ou le RG de disque. Elles peuvent être effectuées via le portail Azure ou l’interface CLI. Pour affecter des autorisations associées, consultez les [prérequis à la configuration de la sauvegarde de disques managés](backup-managed-disks-ps.md#assign-permissions).

### <a name="prepare-the-request"></a>Préparer la requête

Une fois que toutes les autorisations appropriées sont définies, la configuration de la sauvegarde est effectuée en deux étapes. Tout d’abord, nous préparons la requête appropriée en utilisant le coffre, la stratégie, le disque et le groupe de ressources d’instantanés appropriés à l’aide de la commande [az dataprotection backup-instance initialize](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_initialize). La commande Initialize renvoie un fichier JSON, puis vous devez mettre à jour la valeur du groupe de ressources d’instantanés. Ensuite, nous soumettons la requête pour protéger le disque à l’aide de la commande [az dataprotection backup-instance create](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_create).

```azurecli-interactive
az dataprotection backup-instance initialize --datasource-type AzureDisk  -l southeastasia --policy-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/mypolicy" --datasource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk" > backup_instance.json
```

Ouvrez le fichier JSON et modifiez **l’ID du groupe de ressources d’instantanés** dans ``` resource_group_id ``` dans la section ```data_store_parameters_list```.

```json
{
  "backup_instance_name": "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
  "properties": {
    "data_source_info": {
      "datasource_type": "Microsoft.Compute/disks",
      "object_type": "Datasource",
      "resource_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
      "resource_location": "southeastasia",
      "resource_name": "CLITestDisk",
      "resource_type": "Microsoft.Compute/disks",
      "resource_uri": ""
    },
    "data_source_set_info": null,
    "object_type": "BackupInstance",
    "policy_info": {
      "policy_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupPolicies/DiskPolicy",
      "policy_parameters": {
        "data_store_parameters_list": [
          {
            "data_store_type": "OperationalStore",
            "object_type": "AzureOperationalStoreParameters",
            "resource_group_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/snapshotrg"
          }
        ]
      }
    }
  }
}
```

> [!NOTE]
> Le nom de l’instance de sauvegarde est généré par les clients, de sorte qu’il s’agit d’une valeur unique. Il est basé sur le nom de la source de données et sur un identificateur GUID unique. Une fois que vous avez répertorié les instances de sauvegarde, vous devez être en mesure de vérifier le nom de l’instance de sauvegarde et le nom de la source de données appropriée.

Utilisez le fichier JSON modifié pour créer une instance de sauvegarde du disque managé Azure.

```azurecli-interactive
az dataprotection backup-instance create -g testBkpVaultRG --vault-name TestBkpVault --backup-instance backup_instance.json


{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
  "name": "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
  "properties": {
    "currentProtectionState": "ProtectionConfigured",
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Compute/disks",
      "objectType": "Datasource",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
      "resourceLocation": "southeastasia",
      "resourceName": "CLITestDisk",
      "resourceType": "Microsoft.Compute/disks",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk"
    },
    "dataSourceSetInfo": null,
    "friendlyName": "CLITestDisk",
    "objectType": "BackupInstance",
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupPolicies/DiskPolicy",
      "policyParameters": {
        "dataStoreParametersList": [
          {
            "dataStoreType": "OperationalStore",
            "objectType": "AzureOperationalStoreParameters",
            "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/sarath-rg"
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
  "resourceGroup": "testBkpVaultRG",
  "systemData": null,
  "type": "Microsoft.DataProtection/backupVaults/backupInstances"
}
```

Une fois l’instance de sauvegarde créée, vous pouvez procéder au déclenchement d’une sauvegarde à la demande si vous ne souhaitez pas attendre la planification de la stratégie.

## <a name="run-an-on-demand-backup"></a>Exécuter une sauvegarde à la demande

Répertoriez toutes les instances de sauvegarde dans un coffre à l’aide de la commande [az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true), puis récupérez (fetch) l’instance appropriée à l’aide de la commande [az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true). Pour les scénarios à grande échelle, vous pouvez également répertorier les instances de sauvegarde dans les coffres et les abonnements à l’aide de la commande [az dataprotection backup-instance list-from-resourcegraph](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph).

```azurecli-interactive
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureDisk --datasource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk


[
  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
    "extendedLocation": null,
    "id": "//subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
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

Vous pouvez spécifier une règle de rétention lors du déclenchement de la sauvegarde. Pour afficher les règles de rétention dans la stratégie, examinez le fichier JSON de stratégie pour les règles de rétention. Dans l’exemple ci-dessous, la règle portant le nom _défaut_ s’affiche. Nous allons utiliser cette règle pour la sauvegarde à la demande.

```JSON
{
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P7D",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "OperationalStore",
            "objectType": "DataStoreInfoBase"
          }
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
```

Déclenchez une sauvegarde à la demande à l’aide de la commande [az dataprotection backup-instance adhoc-backup](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_adhoc_backup).

```azurecli-interactive
az dataprotection backup-instance adhoc-backup --name "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166" --rule-name "Default" --resource-group "000pikumar" --vault-name "PratikPrivatePreviewVault1"
```

## <a name="tracking-jobs"></a>Suivi des travaux

Effectuez le suivi de tous les travaux à l’aide de la commande [az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list). Vous pouvez répertorier tous les travaux et extraire le détail d’un travail particulier.

Vous pouvez également utiliser la commande pour effectuer le suivi de tous les travaux dans l’ensemble des coffres Sauvegarde. Utilisez la commande [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) pour obtenir le travail approprié, qui peut se trouver dans n’importe quel coffre Sauvegarde.

```azurepowershell-interactive
az dataprotection job list-from-resourcegraph --datasource-type AzureDisk --status Completed
```

## <a name="next-steps"></a>Étapes suivantes

[Azure Disques managés à l’aide de l’interface Azure CLI](restore-managed-disks-cli.md)
