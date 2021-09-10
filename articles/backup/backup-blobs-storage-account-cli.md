---
title: Sauvegarder des blobs Azure à l’aide d’Azure CLI
description: Découvrez comment sauvegarder des blobs Azure à l’aide d’Azure CLI.
ms.topic: conceptual
ms.date: 08/06/2021
ms.openlocfilehash: e8ffd1d369df816bd1020b0b2ec2c1696e6c433e
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122525748"
---
# <a name="back-up-azure-blobs-in-a-storage-account-using-azure-cli"></a>Sauvegarder des blobs Azure dans un compte de stockage à l’aide d’Azure CLI

Cet article explique comment sauvegarder des [blobs Azure](./blob-backup-overview.md) à l’aide d’Azure CLI.

> [!IMPORTANT]
> La prise en charge de la sauvegarde et la restauration des blobs Azure via l’interface CLI est en préversion et disponible en tant qu’extension dans la version 2.15.0 d’Az et versions ultérieures. L’extension est automatiquement installée lorsque vous exécutez les commandes **az dataprotection**. [Apprenez-en davantage](/cli/azure/azure-cli-extensions-overview) sur les extensions.

Dans cet article, vous allez apprendre à :

- Avant de commencer

- Créer un coffre de sauvegarde

- Créer une stratégie de sauvegarde

- Configurer la sauvegarde d’un blob Azure

- Exécuter un travail de sauvegarde à la demande

Pour plus d’informations sur la disponibilité par région, les scénarios pris en charge et les limites relatifs aux blobs Azure, consultez la [matrice de prise en charge](blob-backup-support-matrix.md).

## <a name="before-you-start"></a>Avant de commencer

Consultez les [conditions préalables](./blob-backup-configure-manage.md#before-you-start) et la [matrice de prise en charge](./blob-backup-support-matrix.md) avant de commencer.

## <a name="create-a-backup-vault"></a>Créer un coffre de sauvegarde

Un coffre Sauvegarde est une entité de stockage dans Azure qui stocke les données de sauvegarde de diverses charges de travail plus récentes prises en charge par Sauvegarde Azure, notamment des serveurs Azure Database pour PostgreSQL et des blobs dans un compte de stockage et des disques Azure. Les coffres de sauvegarde facilitent l’organisation de vos données de sauvegarde tout en réduisant le temps nécessaire à leur gestion. Les coffres Sauvegarde sont basés sur le modèle Azure Resource Manager, qui fournit des fonctionnalités améliorées pour sécuriser les données de sauvegarde.

Avant de créer un coffre Sauvegarde, choisissez la redondance de stockage des données dans le coffre. Ensuite, procédez à la création du coffre de Sauvegarde avec cette redondance de stockage et l’emplacement. Dans cet article, nous allons créer un coffre Sauvegarde _TestBkpVault_ dans la région _westus_ sous le groupe de ressources _testBkpVaultRG_. Utilisez la commande [az dataprotection vault create](/cli/azure/dataprotection/backup-vault?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_vault_create) pour créer un coffre Sauvegarde. Pour plus d’informations, consultez [Création d’un coffre Sauvegarde](./backup-vault-overview.md#create-a-backup-vault).

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

> [!IMPORTANT]
> Même si vous voyez la redondance du stockage Sauvegarde du coffre, celle-ci ne s’applique pas à la sauvegarde opérationnelle des blobs. Cela est dû au fait que la sauvegarde est locale par nature et qu’aucune donnée n’est stockée dans le coffre Sauvegarde. Le coffre Sauvegarde est ici l’entité de gestion destinée à vous aider à gérer la protection des objets blob de blocs dans vos comptes de stockage.

Après avoir créé un coffre, nous allons créer une stratégie Sauvegarde pour protéger les blobs Azure dans un compte de stockage.

## <a name="create-a-backup-policy"></a>Créer une stratégie de sauvegarde

> [!IMPORTANT]
> Lisez [cette section](blob-backup-configure-manage.md#before-you-start) avant de créer la stratégie et de configurer des sauvegardes pour des blobs Azure.

Pour comprendre les composants internes d’une stratégie Sauvegarde pour la sauvegarde des blobs Azure, récupérez le modèle de stratégie à l’aide de la commande [az dataprotection backup-policy get-default-policy-template](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_get_default_policy_template). Cette commande retourne un modèle de stratégie par défaut pour un type de source de données donné. Utilisez ce modèle de stratégie pour créer une nouvelle stratégie.

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureBlob

{
  "datasourceTypes": [
    "Microsoft.Storage/storageAccounts/blobServices"
  ],
  "name": "BlobPolicy1",
  "objectType": "BackupPolicy",
  "policyRules": [
    {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P30D",
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

Le modèle de stratégie se compose d’un cycle de vie uniquement (qui décide quand supprimer/copier/déplacer la sauvegarde). Étant donné que la sauvegarde opérationnelle d’objets blob est continue par nature, vous n’avez pas besoin d’une planification pour effectuer des sauvegardes.

```json
"policyRules": [
    {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P30D",
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
```

> [!NOTE]
> Restaurer sur de longues durées peut conduire à des opérations de restauration plus longues. De plus, le temps nécessaire à la restauration d’un jeu de données dépend du nombre d’opérations d’écriture et de suppression effectuées au cours de la période de restauration. Par exemple, la restauration à un point situé 30 jours auparavant d’un compte avec 1 million d’objets, 3 000 objets ajoutés par jour et 1 000 objets supprimés par jour nécessite environ deux heures.<br><br>Une période de rétention et une restauration plus de 90 jours dans le passé ne sont pas recommandées pour un compte avec ce taux de change.

Une fois que la stratégie JSON a toutes les valeurs souhaitées, passez à la création d’une nouvelle stratégie à partir de l’objet de stratégie en utilisant la commande [az dataprotection backup-policy create](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_create).

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureBlob > policy.json
az dataprotection backup-policy create -g testBkpVaultRG --vault-name TestBkpVault -n BlobBackup-Policy --policy policy.json

{
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/BlobBackup-Policy",
    "name": "BlobBackup-Policy",
    "properties": {
      "datasourceTypes": [
        "Microsoft.Storage/storageAccounts/blobServices"
      ],
      "objectType": "BackupPolicy",
      "policyRules": [
        {
          "isDefault": true,
          "lifecycles": [
            {
              "deleteAfter": {
                "duration": "P2D",
                "objectType": "AbsoluteDeleteOption"
              },
              "sourceDataStore": {
                "dataStoreType": "OperationalStore",
                "objectType": "DataStoreInfoBase"
              },
              "targetDataStoreCopySettings": []
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

Une fois le coffre et la stratégie créés, il y a deux points critiques que vous devez prendre en compte pour protéger tous les blobs Azure dans un compte de stockage.

### <a name="key-entities-involved"></a>Entités clés impliquées

#### <a name="storage-account-that-contains-the-blobs-to-be-protected"></a>Compte de stockage qui contient les blobs à protéger

Récupérez l’ID Azure Resource Manager du compte de stockage qui contient les blobs à protéger. Il s’agit de l’identificateur du compte de stockage. Nous allons utiliser un exemple de compte de stockage nommé _CLITestSA_, sous le groupe de ressources _blobrg_, dans un autre abonnement présent dans la région Asie Sud-Est.

```azurecli-interactive
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA"
```

#### <a name="backup-vault"></a>Archivage de sauvegarde

Le coffre de sauvegarde requiert des autorisations sur le compte de stockage pour permettre l’activation des sauvegardes sur les objets blob présents dans le compte de stockage. L’identité managée affectée par le système du coffre est utilisée pour affecter de telles autorisations.

### <a name="assign-permissions"></a>Attribuer des autorisations

Vous devez affecter quelques autorisations via RBAC au coffre (représenté par le fichier MSI de coffre) et le compte de stockage approprié. Elles peuvent être effectuées via le Portail ou PowerShell. En savoir plus sur toutes les [autorisations associées](blob-backup-configure-manage.md#grant-permissions-to-the-backup-vault-on-storage-accounts).

### <a name="prepare-the-request"></a>Préparer la requête

Une fois que toutes les autorisations appropriées sont définies, la configuration de la sauvegarde est effectuée en 2 étapes. Tout d’abord, nous préparons la requête appropriée en utilisant le coffre, la stratégie et le compte de stockage appropriés à l’aide de la commande [az dataprotection backup-instance initialize](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_initialize). Ensuite, nous soumettons la requête pour protéger le disque à l’aide de la commande [az dataprotection backup-instance create](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_create).

```azurecli-interactive
az dataprotection backup-instance initialize --datasource-type AzureBlob  -l southeastasia --policy-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/BlobBackup-Policy" --datasource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA" > backup_instance.json
```

```azurecli-interactive
az dataprotection backup-instance create -g testBkpVaultRG --vault-name TestBkpVault --backup-instance backup_instance.json

{
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
    "name": "CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
        "objectType": "Datasource",
        "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
        "resourceLocation": "southeastasia",
        "resourceName": "CLITestSA",
        "resourceType": "Microsoft.Storage/storageAccounts",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA"
      },
      "dataSourceSetInfo": null,
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
    "resourceGroup": "testBkpVaultRG",
    "systemData": null,
    "type": "Microsoft.DataProtection/backupVaults/backupInstances"
  }
```

> [!IMPORTANT]
> Une fois qu’un compte de stockage est configuré pour la sauvegarde des objets blob, certaines fonctionnalités sont affectées, telles que le flux de modification et le verrou de suppression. [Plus d’informations](blob-backup-configure-manage.md#effects-on-backed-up-storage-accounts)

## <a name="next-steps"></a>Étapes suivantes

[Restaurer des blobs Azure à l’aide d’Azure CLI](restore-blobs-storage-account-cli.md)