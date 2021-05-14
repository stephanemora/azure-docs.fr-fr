---
title: Activer et créer des partages de fichiers volumineux - Azure Files
description: Dans cet article, vous allez apprendre à activer et à créer des partages de fichiers volumineux.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/20/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a53f964020583c41e2400d97ad244bacd33813bc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818257"
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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pour créer un compte de stockage avec des partages de fichiers volumineux activés, utilisez la commande suivante. Remplacez `<yourStorageAccountName>`, `<yourResourceGroup>` et `<yourDesiredRegion>` par vos informations.

```powershell
# This command creates a large file share–enabled account. It will not support changing the 
# redundancy to GRS, GZRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -Location <yourDesiredRegion> `
    -SkuName Standard_LRS `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pour créer un compte de stockage avec des partages de fichiers volumineux activés, utilisez la commande suivante. Remplacez `<yourStorageAccountName>`, `<yourResourceGroup>` et `<yourDesiredRegion>` par vos informations.

```azurecli-interactive
# This command creates a large file share–enabled account. It will not support changing the 
# redundancy to GRS, GZRS, RA-GRS, or RA-GZRS.
az storage account create \
    --name <yourStorageAccountName> \
    -g <yourResourceGroup> \
    -l <yourDesiredRegion> \
    --sku Standard_LRS \
    --kind StorageV2 \
    --enable-large-file-share
```

---

Après avoir créé un compte de stockage avec partage de fichiers volumineux, vous pouvez créer un partage de fichiers qui tire parti de la grande capacité et des hautes limites de mise à l’échelle. Pour plus d’informations sur la création de comptes de stockage et de partages de fichiers Azure, consultez [Créer un partage de fichiers Azure](storage-how-to-create-file-share.md).

## <a name="enable-large-files-shares-on-an-existing-account"></a>Activer les partages de fichiers volumineux sur un compte existant
Vous pouvez aussi activer les partages de fichiers volumineux sur vos comptes de stockage LRS et ZRS existants. Si vous avez un compte GRS, GZRS, RA-GRS ou RA-GZRS, vous devez le convertir en compte LRS avant de continuer.

# <a name="portal"></a>[Portail](#tab/azure-portal)
1. Ouvrez le [portail Azure](https://portal.azure.com) et accédez au compte de stockage sur lequel vous souhaitez activer les partages de fichiers volumineux.
1. Ouvrez le compte de stockage et sélectionnez **Partages de fichiers**.
1. Sélectionnez **Activé** pour **Partages de fichiers volumineux**, puis sélectionnez **Enregistrer**.
1. Sélectionnez **Vue d’ensemble** puis **Actualiser**.
1. Sélectionnez **Capacité de partage**, puis **100 Tio** et **Enregistrer**.

    :::image type="content" source="media/storage-files-how-to-create-large-file-share/files-enable-large-file-share-existing-account.png" alt-text="Capture d’écran du compte de stockage Azure, le panneau Partages de fichiers avec des partages de 100 Tio mis en surbrillance.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pour activer les partages de fichiers volumineux sur votre compte existant, utilisez la commande suivante. Remplacez `<yourStorageAccountName>` et `<yourResourceGroup>` par vos informations.

```powershell
Set-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pour activer les partages de fichiers volumineux sur votre compte existant, utilisez la commande suivante. Remplacez `<yourStorageAccountName>` et `<yourResourceGroup>` par vos informations.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

---

Vous avez maintenant activé les partages de fichiers volumineux sur votre compte de stockage. Ensuite, vous devez [mettre à jour le quota du partage existant](#expand-existing-file-shares) pour tirer parti de l’augmentation de la capacité et de l’échelle. 

> [!Important]  
> Les partages de fichiers existants ne seront pas mis à l’échelle vers les limites publiées pour les partages de fichiers volumineux, sauf si le quota a été modifié.

## <a name="expand-existing-file-shares"></a>Étendre des partages de fichiers existants
Une fois que vous avez activé les partages de fichiers volumineux sur votre compte de stockage, vous devez développer les partages de fichiers existants dans ce compte de stockage pour tirer parti de l’augmentation de la capacité et de l’échelle. 

# <a name="portal"></a>[Portail](#tab/azure-portal)
1. À partir de votre compte de stockage, sélectionnez **Partages de fichiers**.
1. Cliquez avec le bouton droit sur votre partage de fichiers, puis sélectionnez **Quota**.
1. Entrez la nouvelle taille souhaitée, puis sélectionnez **OK**.

![Interface utilisateur du portail Azure avec le quota des partages de fichiers existants](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pour définir le quota sur la taille maximale, utilisez la commande suivante. Remplacez `<YourResourceGroupName>`, `<YourStorageAccountName>` et `<YourStorageAccountFileShareName>` par vos informations.

```powershell
$resourceGroupName = "<YourResourceGroupName>"
$storageAccountName = "<YourStorageAccountName>"
$shareName="<YourStorageAccountFileShareName>"

# update quota
Set-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 102400
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pour définir le quota sur la taille maximale, utilisez la commande suivante. Remplacez `<yourResourceGroupName>`, `<yourStorageAccountName>` et `<yourFileShareName>` par vos informations.

```azurecli-interactive
az storage share-rm update \
    --resource-group <yourResourceGroupName> \
    --storage-account <yourStorageAccountName> \
    --name <yourFileShareName> \
    --quota 102400
```

---

## <a name="next-steps"></a>Étapes suivantes
* [Connecter et monter un partage de fichiers sur Windows](storage-how-to-use-files-windows.md)
* [Connecter et monter un partage de fichiers sur Linux](storage-how-to-use-files-linux.md)
* [Connecter et monter un partage de fichiers sur macOS](storage-how-to-use-files-mac.md)