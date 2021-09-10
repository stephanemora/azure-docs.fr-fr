---
title: Activer la suppression réversible pour les objets blob
titleSuffix: Azure Storage
description: Activez la suppression réversible pour les objets blob afin de protéger les données blob contre les suppressions ou les remplacements accidentels.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 790afdb013d2721bc90238cfe0caf7aa4534c632
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289487"
---
# <a name="enable-soft-delete-for-blobs"></a>Activer la suppression réversible pour les objets blob

La suppression réversible d’objets blob protège un objet blob et ses versions, instantanés et métadonnées contre les suppressions ou les remplacements accidentels en conservant les données supprimées dans le système pendant un laps de temps spécifié. Pendant la période de rétention, vous pouvez restaurer l’objet blob à son état au moment de la suppression. Une fois la période de conservation expirée, l’objet est supprimé définitivement. Pour plus d’informations sur la suppression réversible d’objets blob, consultez [Suppression réversible pour les objets blob](soft-delete-blob-overview.md).

La suppression réversible d’objets blob fait partie d’une stratégie complète de protection des données pour les données blob. Pour en savoir plus sur les recommandations de Microsoft en matière de protection des données, consultez [Vue d’ensemble de la protection des données](data-protection-overview.md).

> [!NOTE]
> La suppression réversible d’objets BLOB peut également protéger des objets BLOB et des répertoires dans les comptes pour lesquels la fonctionnalité d’espace de noms hiérarchique est activée. La suppression réversible d’objets BLOB dans les comptes pour lesquels la fonctionnalité d’espace de noms hiérarchique est activée est actuellement en version préliminaire publique et est disponible globalement dans toutes les régions Azure. 

La suppression réversible d’objets blob est désactivée par défaut pour un nouveau compte de stockage. Vous pouvez activer ou désactiver la suppression réversible pour un compte de stockage à tout moment à l’aide du portail Azure, de PowerShell ou d’Azure CLI.

## <a name="enable-blob-soft-delete"></a>Activer la suppression réversible de blob

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour activer la suppression réversible d’objets blob pour votre compte de stockage à l’aide du portail Azure, procédez comme suit :

1. Dans le [Portail Azure](https://portal.azure.com/), accédez à votre compte de stockage.
1. Localisez l’option **Data Protection** dans **service Blob**.
1. Dans la section **Récupération**, sélectionnez **Activer la suppression réversible pour les objets blob**.
1. Spécifiez une période de conservation comprise entre 1 et 365 jours. Microsoft recommande une période de rétention minimale de sept jours.
1. Enregistrez vos modifications.

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Capture d’écran représentant l’activation de la suppression réversible dans le portail Azure":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour activer la suppression réversible d’objets blob avec PowerShell, appelez la commande [Enable-AzStorageBlobDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) en spécifiant la période de rétention en jours.

L’exemple suivant active la suppression réversible d’objets blob et définit la période de rétention sur sept jours. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

Pour vérifier les paramètres actuels de la suppression réversible d’objets blob, appelez la commande [Get-AzStorageBlobServiceProperty](/powershell/module/az.storage/get-azstorageblobserviceproperty) :

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-CLI)

Pour activer la suppression réversible d’objets blob avec Azure CLI, appelez la commande [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) en spécifiant la période de rétention en jours.

L’exemple suivant active la suppression réversible d’objets blob et définit la période de rétention sur sept jours. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Pour vérifier les paramètres actuels de la suppression réversible d’objets blob, appelez la commande [az storage account blob-service-properties show](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_show) :

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="enable-blob-soft-delete-hierarchical-namespace"></a>Activer la suppression réversible d’objet BLOB (espace de noms hiérarchique)

La suppression réversible d’objets BLOB peut également protéger des objets BLOB et des répertoires dans les comptes pour lesquels la fonctionnalité d’espace de noms hiérarchique est activée. 

> [!IMPORTANT]
> La suppression réversible dans les comptes pour lesquels la fonctionnalité d’espace de noms hiérarchique est activée est actuellement en VERSION PRÉLIMINAIRE et est disponible globalement dans toutes les régions Azure.
> Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
>
> Pour vous inscrire à la préversion, voir [ce formulaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pUOVRVOUpDRUtHVUtDUUtMVTZUR0tUMjZWNy4u).

<a id="enable-blob-soft-delete-hierarchical-namespace"></a>

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour activer la suppression réversible d’objets blob pour votre compte de stockage à l’aide du portail Azure, procédez comme suit :

1. Dans le [Portail Azure](https://portal.azure.com/), accédez à votre compte de stockage.
1. Repérez l’option **Protection des données** sous **Gestion des données**.
1. Dans la section **Récupération**, sélectionnez **Activer la suppression réversible pour les objets blob**.
1. Spécifiez une période de conservation comprise entre 1 et 365 jours. Microsoft recommande une période de rétention minimale de sept jours.
1. Enregistrez vos modifications.

> [!div class="mx-imgBorder"]
> ![Capture d’écran montrant comment activer la suppression réversible dans le Portail Azure dans les comptes qui ont un espace de noms hiérarchique](./media/soft-delete-blob-enable/blob-soft-delete-configuration-portal-hierarchical-namespace.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installez le dernier module **PowerShellGet**. Ensuite, fermez et rouvrez la console PowerShell.

    ```powershell
    install-Module PowerShellGet –Repository PSGallery –Force 
    ```

2.  Installez le module **Az.Storage** en préversion.

    ```powershell
    Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.7.1-preview -AllowClobber -AllowPrerelease -Force
    ```
    Pour plus d’informations sur l’installation des modules PowerShell, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps).

3. Obtenez l’autorisation de compte de stockage à l’aide d’une clé de compte de stockage, d’une chaîne de connexion ou d’Azure Active Directory (Azure AD). Voir [Se connecter au compte](data-lake-storage-directory-file-acl-powershell.md#connect-to-the-account).

   L’exemple suivant obtient l’autorisation à l’aide d’une clé de compte de stockage.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
   ```

4. Pour activer la suppression réversible d’objets blob avec PowerShell, utilisez la commande [Enable-AzStorageDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstoragedeleteretentionpolicy) et spécifiez la période de rétention en jours.

   L’exemple suivant active la suppression réversible pour un compte et définit la période de rétention sur quatre jours. 

   ```powershell
   Enable-AzStorageDeleteRetentionPolicy -RetentionDays 4  -Context $ctx
   ```
5. Pour vérifier les paramètres actuels de la suppression réversible d’objets BLOB, utilisez la commande `Get-AzStorageServiceProperty` :

   ```powershell
    Get-AzStorageServiceProperty -ServiceType Blob -Context $ctx
   ```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-CLI)

1. Ouvrez [Azure Cloud Shell](../../cloud-shell/overview.md) ou, si vous avez [installé](/cli/azure/install-azure-cli) Azure CLI localement, ouvrez une application console de commandes telle que Windows PowerShell.

2. Installer l’extension `storage-preview`.

   ```azurecli
   az extension add -n storage-preview
   ```
3. Connectez-vous à votre compte de stockage. Voir [Se connecter au compte](data-lake-storage-directory-file-acl-cli.md#connect-to-the-account).

   > [!NOTE]
   > L’exemple présenté dans cet article illustre l’autorisation Azure Active Directory (Azure AD). Pour en savoir plus sur les méthodes d’autorisation, consultez [Autoriser l’accès à des données d’objet blob ou de file d’attente avec Azure CLI](./authorize-data-operations-cli.md).
 
4. Pour activer la suppression réversible avec Azure CLI, appelez la commande `az storage fs service-properties update` en spécifiant la période de rétention en jours.

   L’exemple suivant active la suppression réversible d’objets BLOB et de répertoires et définit la période de rétention sur cinq jours. 

   ```azurecli
   az storage fs service-properties update --delete-retention --delete-retention-period 5 --auth-mode login
   ```

5. Pour vérifier les paramètres actuels de la suppression réversible d’objets BLOB, appelez la commande `az storage fs service-properties update` :

   ```azurecli
   az storage fs service-properties update --delete-retention false --connection-string $con
   ```

---

## <a name="next-steps"></a>Étapes suivantes

- [Suppression réversible pour les objets blob](soft-delete-blob-overview.md)
- [Gérer et restaurer des objets blob supprimés de manière réversible](soft-delete-blob-manage.md)