---
title: Activer et créer des partages de fichiers volumineux - Azure Files
description: Dans cet article, vous allez apprendre à activer et à créer des partages de fichiers volumineux.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/29/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 20f9aaf73fe0cb30b136254d57e6c9b960c16af4
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716975"
---
# <a name="enable-and-create-large-file-shares"></a>Activer et créer des partages de fichiers volumineux

Lorsque vous activez des partages de fichiers volumineux sur votre compte de stockage, l’échelle de vos partages de fichiers Azure peut augmenter jusqu’à 100 Tio. Lorsque vous activez les partages de fichiers volumineux, cela peut également augmenter les limites de débit et d’E/S par seconde de votre partage de fichiers. Vous pouvez également activer cette mise à l’échelle sur vos comptes de stockage existants pour les partages de fichiers nouveaux et existants. Pour plus d’informations sur les différences de performances, consultez [Partage de fichiers et objectifs de mise à l’échelle des fichiers](storage-files-scale-targets.md#azure-files-scale-targets).

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- Si vous envisagez d’utiliser Azure CLI, [installez-en la dernière version](/cli/azure/install-azure-cli).
- Si vous envisagez d’utiliser le module Azure PowerShell, [installez-en la dernière version](/powershell/azure/install-az-ps).

## <a name="restrictions"></a>Restrictions

Pour le moment, vous ne pouvez utiliser qu’un stockage localement redondant (LRS) ou un stockage redondant interzone (ZRS) sur des comptes de stockage activés pour le partage de fichiers volumineux. Vous ne pouvez pas utiliser un stockage géoredondant interzone (GZRS), un stockage géoredondant (GRS), un stockage géoredondant avec accès en lecture (RA-GRS) ou un stockage géoredondant interzone avec accès en lecture (RA-GZRS).

L’activation de partages de fichiers volumineux sur un compte est un processus irréversible. Une fois que vous l’avez activé, vous ne pouvez plus convertir votre compte en GZRS, GRS, RA-GRS ou RA-GZRS.

## <a name="create-a-new-storage-account"></a>Création d’un nouveau compte de stockage

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le portail Azure, sélectionnez **Tous les services**. 
1. Dans la liste des ressources, entrez **Comptes de stockage**. Lorsque vous écrivez, au fur et à mesure de votre saisie, la liste est filtrée. Sélectionnez **Comptes de stockage**.
1. Dans le panneau **Comptes de stockage** qui apparaît, sélectionnez **+ Nouveau**.
1. Dans le panneau Général, renseignez les sélections comme vous le souhaitez.
1. Vérifiez que **Performances** est défini sur **Standard**.
1. Définissez la **Redondance** sur **Stockage localement redondant** ou sur **Stockage redondant interzone**.
1. Sélectionnez le panneau **Avancé**, puis activez la case d’option **Activé** à droite **Partages de fichiers volumineux**.
1. Cliquez sur **Vérifier + créer** pour passer en revue vos paramètres de compte de stockage et créer le compte.
1. Sélectionnez **Create** (Créer).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Tout d’abord, [installez la dernière version d’Azure CLI](/cli/azure/install-azure-cli) pour pouvoir activer les partages de fichiers volumineux.

Pour créer un compte de stockage avec des partages de fichiers volumineux activés, utilisez la commande suivante. Remplacez `<yourStorageAccountName>`, `<yourResourceGroup>` et `<yourDesiredRegion>` par vos informations.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Tout d’abord, [installez la dernière version de PowerShell](/powershell/azure/install-az-ps) pour pouvoir activer les partages de fichiers volumineux.

Pour créer un compte de stockage avec des partages de fichiers volumineux activés, utilisez la commande suivante. Remplacez `<yourStorageAccountName>`, `<yourResourceGroup>` et `<yourDesiredRegion>` par vos informations.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```
---

## <a name="enable-large-files-shares-on-an-existing-account"></a>Activer les partages de fichiers volumineux sur un compte existant

Vous pouvez aussi activer les partages de fichiers volumineux sur vos comptes existants. Si vous activez les partages de fichiers volumineux, vous ne pourrez plus convertir votre compte en GZRS, GRS, RA-GRS ou RA-GZRS. L’activation des partages de fichiers volumineux est irréversible sur ce compte de stockage.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Ouvrez le [portail Azure](https://portal.azure.com) et accédez au compte de stockage sur lequel vous souhaitez activer les partages de fichiers volumineux.
1. Ouvrez le compte de stockage et sélectionnez **Partages de fichiers**.
1. Sélectionnez **Activé** pour **Partages de fichiers volumineux**, puis sélectionnez **Enregistrer**.
1. Sélectionnez **Vue d’ensemble** puis **Actualiser**.
1. Sélectionnez **Capacité de partage**, puis **100 Tio** et **Enregistrer**.

    :::image type="content" source="media/storage-files-how-to-create-large-file-share/files-enable-large-file-share-existing-account.png" alt-text="Capture d’écran du compte de stockage Azure, le panneau Partages de fichiers avec des partages de 100 Tio mis en surbrillance.":::

Vous avez maintenant activé les partages de fichiers volumineux sur votre compte de stockage. Ensuite, vous devez [mettre à jour le quota du partage existant](#expand-existing-file-shares) pour tirer parti de l’augmentation de la capacité et de l’échelle.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour activer les partages de fichiers volumineux sur votre compte existant, utilisez la commande suivante. Remplacez `<yourStorageAccountName>` et `<yourResourceGroup>` par vos informations.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

Vous avez maintenant activé les partages de fichiers volumineux sur votre compte de stockage. Ensuite, vous devez [mettre à jour le quota du partage existant](#expand-existing-file-shares) pour tirer parti de l’augmentation de la capacité et de l’échelle.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour activer les partages de fichiers volumineux sur votre compte existant, utilisez la commande suivante. Remplacez `<yourStorageAccountName>` et `<yourResourceGroup>` par vos informations.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

Vous avez maintenant activé les partages de fichiers volumineux sur votre compte de stockage. Ensuite, vous devez [mettre à jour le quota du partage existant](#expand-existing-file-shares) pour tirer parti de l’augmentation de la capacité et de l’échelle.

---

## <a name="create-a-large-file-share"></a>Créer un partage de fichiers volumineux

Une fois que vous avez activé les partages de fichiers volumineux sur votre compte de stockage, vous pouvez créer des partages de fichiers dans ce compte avec des quotas plus élevés. 

# <a name="portal"></a>[Portail](#tab/azure-portal)

La création d’un partage de fichiers volumineux est presque identique à la création d’un partage de fichiers standard. La principale différence est que vous pouvez définir un quota allant jusqu’à 100 Tio.

1. À partir de votre compte de stockage, sélectionnez **Partages de fichiers**.
1. Sélectionner **+Partage de fichiers**.
1. Entrez un nom pour votre partage de fichiers. Vous pouvez également définir la taille de quota de votre choix, jusqu’à 100 Tio. Sélectionnez ensuite **Créer**. 

![Interface utilisateur du portail Azure montrant les zones Nom et Quota](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour créer un partage de fichiers volumineux, utilisez la commande suivante. Remplacez `<yourStorageAccountName>`, `<yourStorageAccountKey>` et `<yourFileShareName>` par vos informations.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour créer un partage de fichiers volumineux, utilisez la commande suivante. Remplacez `<YourStorageAccountName>`, `<YourStorageAccountKey>` et `<YourStorageAccountFileShareName>` par vos informations.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```
---

## <a name="expand-existing-file-shares"></a>Étendre des partages de fichiers existants

Une fois que vous avez activé les partages de fichiers volumineux sur votre compte de stockage, vous pouvez aussi étendre les partages de fichiers existants dans ce compte avec un quota plus élevé. 

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. À partir de votre compte de stockage, sélectionnez **Partages de fichiers**.
1. Cliquez avec le bouton droit sur votre partage de fichiers, puis sélectionnez **Quota**.
1. Entrez la nouvelle taille souhaitée, puis sélectionnez **OK**.

![Interface utilisateur du portail Azure avec le quota des partages de fichiers existants](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour définir le quota sur la taille maximale, utilisez la commande suivante. Remplacez `<yourStorageAccountName>`, `<yourStorageAccountKey>` et `<yourFileShareName>` par vos informations.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour définir le quota sur la taille maximale, utilisez la commande suivante. Remplacez `<YourStorageAccountName>`, `<YourStorageAccountKey>` et `<YourStorageAccountFileShareName>` par vos informations.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```
---

## <a name="next-steps"></a>Étapes suivantes

* [Connecter et monter un partage de fichiers sur Windows](storage-how-to-use-files-windows.md)
* [Connecter et monter un partage de fichiers sur Linux](storage-how-to-use-files-linux.md)
* [Connecter et monter un partage de fichiers sur macOS](storage-how-to-use-files-mac.md)