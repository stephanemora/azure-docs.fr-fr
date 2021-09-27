---
title: Activer les rapports d’inventaire des objets blob du Stockage Azure
description: Obtenez une vue d’ensemble de vos conteneurs, objets blob, instantanés et versions d’objets blob dans un compte de stockage.
services: storage
author: normesta
ms.service: storage
ms.date: 08/16/2021
ms.topic: how-to
ms.author: normesta
ms.reviewer: klaasl
ms.subservice: blobs
ms.openlocfilehash: 67bd943028ba321aa4fa3a5acca30e80cfc36a32
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615564"
---
# <a name="enable-azure-storage-blob-inventory-reports"></a>Activer les rapports d’inventaire des objets blob du Stockage Azure

La fonctionnalité d’inventaire des objets blob du Stockage Azure offre une vue d’ensemble de vos conteneurs, objets blob, instantanés et versions d’objets blob dans un compte de stockage. Utilisez le rapport d’inventaire pour comprendre divers attributs des blobs et des conteneurs, tels que la taille totale de vos données, leur ancienneté, l’état du chiffrement, la stratégie d’immuabilité et la conservation légale, etc. Il vous fournit une synthèse de vos données pour les besoins de l’entreprise et ses exigences de conformité.

Pour en savoir plus sur les rapports d’inventaire d’objets blob, consultez [Inventaire des objets blob du Stockage Azure](blob-inventory.md).

Activez les rapports d’inventaire des objets blob en ajoutant une stratégie comprenant une ou plusieurs règles à votre compte de stockage. Ajoutez, modifiez ou supprimez une stratégie à l’aide du [Portail Azure](https://portal.azure.com/).

## <a name="enable-inventory-reports"></a>Activation des rapports d’inventaire

### <a name="portal"></a>[Portail](#tab/azure-portal)

1. Pour commencer, connectez-vous au [portail Azure](https://portal.azure.com/).

2. Recherchez votre compte de stockage et affichez la vue d’ensemble du compte.

3. Sous **Gestion des données**, sélectionnez **Inventaire des objets blob**.

4. Sélectionnez **Ajouter votre première règle d’inventaire**.

   La page **Ajouter une règle** s’affiche.

5. Dans la page **Ajouter une règle**, donnez un nom à votre règle.

6. Choisissez un conteneur.

7. Sous **Type d’objet à inventorier**, choisissez si vous souhaitez créer un rapport pour les objets blob ou pour les conteneurs.

   Si vous sélectionnez **Objet blob**, sous **Sous-type de l’objet blob**, choisissez les types d’objets blob que vous souhaitez inclure dans votre rapport, et si vous souhaitez inclure des versions d’objets blob et/ou des instantanés dans votre rapport d’inventaire.

   > [!NOTE]
   > Vous devez activer les versions et les instantanés sur le compte pour pouvoir enregistrer une nouvelle règle avec l’option correspondante activée.

8. Sélectionnez les champs que vous souhaitez inclure dans votre rapport, ainsi que le format de vos rapports.

9. Choisissez la fréquence à laquelle vous souhaitez générer des rapports.

9. Si vous le souhaitez, ajoutez une correspondance de préfixe pour filtrer les objets blob de votre rapport d’inventaire.

10. Sélectionnez **Enregistrer**.

    :::image type="content" source="./media/blob-inventory-how-to/portal-blob-inventory.png" alt-text="Capture d’écran montrant comment ajouter une règle d’inventaire des objets blob à l’aide du Portail Azure":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell"></a>

Vous pouvez activer l’hébergement de site web statique à l’aide du module Azure PowerShell.

1. Ouvrez une fenêtre Commande Windows PowerShell.

2. Vérifiez que la dernière version du module Azure PowerShell est installée. Consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps).

3. Connectez-vous à votre abonnement Azure avec la commande `Connect-AzAccount` et suivez les instructions à l’écran.

   ```powershell
   Connect-AzAccount
   ```

4. Si votre identité est associée à plusieurs abonnements, définissez comme abonnement actif l’abonnement du compte de stockage qui doit héberger votre site web statique.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Remplacez la valeur d’espace réservé `<subscription-id>` par l’ID de votre abonnement.

5. Obtenez le contexte du compte de stockage qui définit le compte de stockage à utiliser.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   - Remplacez la valeur d’espace réservé `<resource-group-name>` par le nom de votre groupe de ressources.

   - Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom de votre compte de stockage.

6. Créez des règles d’inventaire à l’aide de la commande [New-AzStorageBlobInventoryPolicyRule](/powershell/module/az.storage/new-azstorageblobinventorypolicyrule). Chaque règle liste des champs de rapport. Pour obtenir la liste complète des champs de rapport, consultez [Inventaire des objets blob du Stockage Azure](blob-inventory.md).

   ```powershell
    $containerName = "my-container"

    $rule1 = New-AzStorageBlobInventoryPolicyRule -Name Test1 -Destination $containerName -Disabled -Format Csv -Schedule Daily -PrefixMatch con1,con2 `
                -ContainerSchemaField Name,Metadata,PublicAccess,Last-modified,LeaseStatus,LeaseState,LeaseDuration,HasImmutabilityPolicy,HasLegalHold

    $rule2 = New-AzStorageBlobInventoryPolicyRule -Name test2 -Destination $containerName -Format Parquet -Schedule Weekly  -BlobType blockBlob,appendBlob -PrefixMatch aaa,bbb `
                -BlobSchemaField name,Last-Modified,Metadata,LastAccessTime

    $rule3 = New-AzStorageBlobInventoryPolicyRule -Name Test3 -Destination $containerName -Format Parquet -Schedule Weekly -IncludeBlobVersion -IncludeSnapshot -BlobType blockBlob,appendBlob -PrefixMatch aaa,bbb `
                -BlobSchemaField name,Creation-Time,Last-Modified,Content-Length,Content-MD5,BlobType,AccessTier,AccessTierChangeTime,Expiry-Time,hdi_isfolder,Owner,Group,Permissions,Acl,Metadata,LastAccessTime

    $rule4 = New-AzStorageBlobInventoryPolicyRule -Name test4 -Destination $containerName -Format Csv -Schedule Weekly -BlobType blockBlob -BlobSchemaField Name,BlobType,Content-Length,Creation-Time

   ```

7. Utilisez [Set-AzStorageBlobInventoryPolicy](/powershell/module/az.storage/set-azstorageblobinventorypolicy) pour créer une stratégie d’inventaire d’objets blob. Passez les règles dans cette commande à l’aide du paramètre `-Rule`.

   ```powershell
   $policy = Set-AzStorageBlobInventoryPolicy -StorageAccount $storageAccount -Rule $rule1,$rule2,$rule3,$rule4  
   ```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli"></a>

Vous pouvez activer l’hébergement de site web statique à l’aide de l’[interface de ligne de commande Azure](/cli/azure/) (Azure CLI).

1. Commencez par ouvrir [Azure Cloud Shell](../../cloud-shell/overview.md) ou, si vous avez [installé](/cli/azure/install-azure-cli) Azure CLI localement, ouvrez une application console de commandes telle que Windows PowerShell.

2. Si votre identité est associée à plusieurs abonnements, définissez comme abonnement actif l’abonnement du compte de stockage qui doit héberger votre site web statique.

   ```azurecli
      az account set --subscription <subscription-id>
   ```

   Remplacez la valeur d’espace réservé `<subscription-id>` par l’ID de votre abonnement.

3. Définissez les règles de votre stratégie dans un document JSON. L’exemple suivant montre le contenu d’un exemple de fichier JSON nommé `policy.json`.

    ```json
    {
    "enabled": true,
    "type": "Inventory",
    "rules": [
      {
        "enabled": true,
        "name": "inventoryPolicyRule2",
        "destination": "mycontainer",
        "definition": {
          "filters": {
            "blobTypes": [
              "blockBlob"
            ],
            "prefixMatch": [
              "inventoryprefix1",
              "inventoryprefix2"
            ],
            "includeSnapshots": true,
            "includeBlobVersions": true
          },
          "format": "Csv",
          "schedule": "Daily",
          "objectType": "Blob",
          "schemaFields": [
            "Name",
            "Creation-Time",
            "Last-Modified",
            "Content-Length",
            "Content-MD5",
            "BlobType",
            "AccessTier",
            "AccessTierChangeTime",
            "Snapshot",
            "VersionId",
            "IsCurrentVersion",
            "Metadata"
          ]
        }
      }
     ]
   }

   ```

4. Créez une stratégie d’inventaire d’objets blob à l’aide de la commande [az storage account blob-inventory-policy create](/cli/azure/storage/account/blob-inventory-policy#az_storage_account_blob_inventory_policy_create). Indiquez le nom de votre document JSON à l’aide du paramètre `--policy`.

   ```azurecli
   az storage account blob-inventory-policy create -g myresourcegroup --account-name mystorageaccount --policy @policy.json
   ```

---

## <a name="next-steps"></a>Étapes suivantes

- [Calculer le nombre et la taille totale des objets blob par conteneur](calculate-blob-count-size.md)
- [Gérer le cycle de vie de Stockage Blob Azure](./lifecycle-management-overview.md)
