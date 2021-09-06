---
title: Activer et gérer la suppression réversible pour les conteneurs
titleSuffix: Azure Storage
description: Activez la suppression réversible de conteneur afin de récupérer plus facilement vos données en cas de modification ou de suppression malencontreuses.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/06/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 2284344e525608c2c1498503f3bf479df1cea559
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113504497"
---
# <a name="enable-and-manage-soft-delete-for-containers"></a>Activer et gérer la suppression réversible pour les conteneurs

La suppression réversible de conteneurs protège vos données d’une modification ou d’une suppression accidentelle. Lorsque la suppression réversible de conteneur est activée pour un compte de stockage, un conteneur et son contenu peuvent être récupérés après suppression pendant la période de rétention que vous spécifiez. Pour en savoir plus sur la suppression réversible de conteneurs, consultez [Suppression réversible pour les conteneurs](soft-delete-container-overview.md).

Pour la protection des données de bout en bout, Microsoft vous recommande également d’activer la suppression réversible pour les blobs et le contrôle de version des blobs. Pour savoir comment activer la suppression réversible pour les blobs, consultez [Activer et gérer la suppression réversible pour les blobs](soft-delete-blob-enable.md). Pour savoir comment activer le contrôle de version des blobs, consultez [Contrôle de version des blobs](versioning-overview.md).

## <a name="enable-container-soft-delete"></a>Activer la suppression réversible de conteneur

Vous pouvez activer ou désactiver la suppression réversible de conteneurs pour le compte de stockage à tout moment à l’aide du portail Azure, de PowerShell, d’Azure CLI ou d’un modèle Resource Manager. Microsoft recommande de définir la période de rétention pour la suppression réversible de conteneur sur un minimum de sept jours.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Pour activer la suppression réversible de conteneur pour votre compte de stockage à l’aide du portail Azure, procédez comme suit :

1. Dans le [Portail Azure](https://portal.azure.com/), accédez à votre compte de stockage.
1. Localisez les paramètres **Protection des données** sous **Gestion des données**.
1. Sélectionnez **Activer la suppression réversible pour les conteneurs**.
1. Spécifiez une période de conservation comprise entre 1 et 365 jours.
1. Enregistrez vos modifications.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-configure.png" alt-text="Capture d’écran représentant l’activation de la suppression réversible de conteneur dans le portail Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour activer la suppression réversible de conteneurs avec PowerShell, commencez par installer le module [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage), version 3.9.0 ou ultérieure. Ensuite, appelez la commande **Enable-AzStorageContainerDeleteRetentionPolicy** et spécifiez le nombre de jours pour la période de rétention. N’oubliez pas de remplacer les valeurs entre crochets par vos propres valeurs :

```azurepowershell-interactive
Enable-AzStorageContainerDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7 
```

Pour désactiver la suppression réversible de conteneurs, appelez la commande **Disable-AzStorageContainerDeleteRetentionPolicy**.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour activer la suppression réversible de conteneurs avec Azure CLI, commencez par installer Azure CLI, version 2.26.0 ou ultérieure. Ensuite, appelez la commande [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) et spécifiez le nombre de jours pour la période de rétention. N’oubliez pas de remplacer les valeurs entre crochets par vos propres valeurs :

```azurecli-interactive
az storage account blob-service-properties update \
    --enable-container-delete-retention true \
    --container-delete-retention-days 7 \
    --account-name <storage-account> \
    --resource-group <resource_group>
```

Pour désactiver la suppression réversible de conteneurs, spécifiez `false` pour le paramètre `--enable-container-delete-retention`.

# <a name="template"></a>[Modèle](#tab/template)

Pour activer la suppression réversible de conteneur avec un modèle Resource Manager, créez un modèle qui définit la propriété **containerDeleteRetentionPolicy**. Les étapes suivantes montrent comment créer un modèle dans le Portail Azure.

1. Dans le Portail Azure, choisissez **Créer une ressource**.
1. Dans **Rechercher sur la Place de marché**, tapez **déploiement de modèle**, puis appuyez sur **Entrée**.
1. Choisissez **Template deployment**, **Créer**, puis **Créer votre propre modèle dans l’éditeur**.
1. Dans l’éditeur de modèle, collez le code JSON suivant. Remplacez la valeur d’espace réservé `<account-name>` par le nom de votre compte de stockage.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts/blobServices",
              "apiVersion": "2019-06-01",
              "name": "<account-name>/default",
              "properties": {
                  "containerDeleteRetentionPolicy": {
                      "enabled": true,
                      "days": 7
                  }
              }
          }
      ]
    }
    ```

1. Spécifiez la période de rétention. La valeur par défaut est 7.
1. Enregistrez le modèle.
1. Spécifiez le groupe de ressources du compte, puis choisissez le bouton **Vérifier + créer** pour déployer le modèle et activer la suppression réversible de conteneur.

---

## <a name="view-soft-deleted-containers"></a>Afficher les conteneurs supprimés de manière réversible

Lorsque la suppression réversible est activée, vous pouvez afficher les conteneurs supprimés de manière réversible dans le portail Azure. Les conteneurs supprimés de manière réversible sont visibles pendant la période de rétention spécifiée. Après l’expiration de la période de rétention, un conteneur supprimé de manière réversible est définitivement supprimé et n’est plus visible.

Pour afficher les conteneurs supprimés de manière réversible dans le portail Azure, procédez comme suit :

1. Accédez à votre compte de stockage dans le portail Azure et affichez la liste de vos conteneurs.
1. Activez le bouton bascule Afficher les conteneurs supprimés pour inclure les conteneurs supprimés dans la liste.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-list.png" alt-text="Capture d’écran montrant comment afficher les conteneurs supprimés de manière réversible dans le portail Azure":::

## <a name="restore-a-soft-deleted-container"></a>Restaurer un conteneur supprimé de manière réversible

Vous pouvez restaurer un conteneur supprimé de manière réversible et son contenu pendant la période de rétention. Pour restaurer un conteneur supprimé de manière réversible dans le portail Azure, procédez comme suit :

1. Accédez à votre compte de stockage dans le portail Azure et affichez la liste de vos conteneurs.
1. Affichez le menu contextuel du conteneur que vous souhaitez restaurer, puis choisissez **Annuler la suppression** dans le menu.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-restore.png" alt-text="Capture d’écran montrant comment restaurer un conteneur supprimé de manière réversible dans le portail Azure":::

## <a name="next-steps"></a>Étapes suivantes

- [Suppression réversible des conteneurs](soft-delete-container-overview.md)
- [Suppression réversible pour les objets blob](soft-delete-blob-overview.md)
- [Contrôle de version des blobs](versioning-overview.md)
