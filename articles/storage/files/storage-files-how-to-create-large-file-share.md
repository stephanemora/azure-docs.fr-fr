---
title: Activer et créer des partages de fichiers volumineux - Azure Files
description: Dans cet article, vous allez apprendre à activer et à créer des partages de fichiers volumineux.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 34b8af56a8f2f108b96ca07fa73f90bb9eb5bf13
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422731"
---
# <a name="how-to-enable-and-create-large-file-shares"></a>Guide pratique pour activer et créer des partages de fichiers volumineux

À l’origine, les partages de fichiers standard pouvaient uniquement être mis à l’échelle jusqu’à 5 Tio. Désormais, avec les partages de fichiers volumineux, ils peuvent subir un scale-up jusqu’à 100 Tio. Par défaut, vous pouvez effectuer un scale-up des partages de fichiers Premium jusqu’à 100 Tio. 

Pour un scale-up jusqu’à 100 Tio à l’aide de partages de fichiers standard, vous devez autoriser votre compte de stockage à utiliser des partages de fichiers volumineux. Vous pouvez autoriser un compte existant ou créer un nouveau compte pour utiliser des partages de fichiers volumineux.

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- Si vous envisagez d’utiliser Azure CLI, assurez-vous [d’installer la dernière version](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Si vous envisagez d’utiliser Azure PowerShell, assurez-vous [d’installer la dernière version](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="restrictions"></a>Restrictions

Les comptes activés pour les partages de fichiers volumineux prennent en charge LRS ou ZRS. Pour l’instant, les comptes activés pour les partages de fichiers volumineux ne prennent pas en charge GZRS, GRS ou RA-GRS. L’activation des partages de fichiers volumineux sur un compte est un processus irréversible. Une fois l’activation effectuée, votre compte ne peut pas être converti en GZRS, GRS ou RA-GRS.

## <a name="create-a-new-storage-account"></a>Création d’un nouveau compte de stockage

### <a name="portal"></a>Portail

Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Dans le portail Azure, sélectionnez **Tous les services**. Dans la liste des ressources, tapez **Comptes de stockage**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Comptes de stockage**.
1. Sur la fenêtre **Comptes de stockage**, sélectionnez **Ajouter**.
1. Sélectionnez l’abonnement dans lequel créer le compte de stockage.
1. Sous le champ **Groupe de ressources**, sélectionnez **Créer**. Entrez un nom pour votre nouveau groupe de ressources, comme indiqué dans l’image suivante.

    ![Capture d’écran montrant comment créer un groupe de ressources sur le portail](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Ensuite, entrez un nom pour votre compte de stockage. Le nom choisi doit être unique dans tout Azure. Le nom doit aussi contenir entre 3 et 24 caractères, et uniquement des lettres minuscules et des chiffres.
1. Sélectionnez un emplacement pour votre compte de stockage et vérifiez qu’il s’agit de l’[une des régions prises en charge pour LFS](storage-files-planning.md#regional-availability).
1. Affectez **Stockage localement redondant** ou **Stockage redondant interzone** comme valeur pour la réplication.
1. Laissez ces champs définis sur leur valeur par défaut :

   |Champ  |Valeur  |
   |---------|---------|
   |Modèle de déploiement     |Gestionnaire de ressources         |
   |Performances     |standard         |
   |Type de compte     |StorageV2 (usage général v2)         |
   |Niveau d’accès     |À chaud         |

1. Sélectionnez **Avancé**, puis **Activé** pour **Partages de fichiers volumineux**.
1. Cliquez sur **Vérifier + créer** pour passer en revue vos paramètres de compte de stockage et créer le compte.

    ![large-file-shares-advanced-enable.png](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Sélectionnez **Create** (Créer).


### <a name="cli"></a>Interface de ligne de commande

Tout d’abord, assurez-vous [d’installer la dernière version](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) afin de pouvoir activer cette fonctionnalité.

Pour créer un compte de stockage avec des partages de fichiers volumineux activés, remplacez `<yourStorageAccountName>`, `<yourResourceGroup>` et `<yourDesiredRegion>` par vos valeurs, puis utilisez la commande suivante :

```azurecli-interactive
## This command creates a large file share enabled account, it will not support GZRS, GRS, or RA-GRS
az storage account create –name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> –sku Standard_LRS --kind StorageV2 –enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Tout d’abord, assurez-vous [d’installer la dernière version](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) afin de pouvoir activer cette fonctionnalité.

Pour créer un compte de stockage avec des partages de fichiers volumineux activés, remplacez `<yourStorageAccountName>`, `<yourResourceGroup>` et `<yourDesiredRegion>` par vos valeurs, puis utilisez la commande suivante :

```PowerShell
## This command creates a large file share enabled account, it will not support GZRS, GRS, or RA-GRS
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-on-existing-account"></a>Activer sur un compte existant

### <a name="portal"></a>Portail

Vous pouvez également activer les partages de fichiers volumineux sur des comptes existants. Dans ce cas, le compte ne pourra plus être converti en GZRS, GRS ou RA-GRS. Ce choix est irréversible sur ce compte.

1. Ouvrez le [portail Azure](https://portal.azure.com) et accédez au compte de stockage sur lequel vous souhaitez activer les partages de fichiers volumineux.
1. Ouvrez le compte de stockage et sélectionnez **Configuration**.
1. Sélectionnez **Activé** pour **Partages de fichiers volumineux**, puis sélectionnez Enregistrer.
1. Sélectionnez **Vue d’ensemble** puis **Actualiser**.

![enable-large-file-shares-on-existing.png](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Vous avez maintenant activé les partages de fichiers volumineux sur votre compte de stockage.

Si vous recevez l’erreur suivante : « Les grands partages de fichiers ne sont pas encore disponibles pour le compte. » Vous pouvez patienter un peu, car votre région est probablement en cours de déploiement, ou, si vous avez des besoins urgents, contactez le support.

### <a name="cli"></a>Interface de ligne de commande

Vous pouvez activer les partages de fichiers volumineux sur vos comptes existants. Dans ce cas, le compte ne pourra plus être converti en GZRS, GRS ou RA-GRS. Ce choix est irréversible sur ce compte.

Remplacez `<yourStorageAccountName>` et `<yourResourceGroup>` dans la commande suivante, puis utilisez celle-ci pour activer les partages de fichiers volumineux sur votre compte existant :

```azurecli-interactive
az storage account update –name <yourStorageAccountName> -g <yourResourceGroup> –enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Vous pouvez activer les partages de fichiers volumineux sur vos comptes existants. Dans ce cas, le compte ne pourra plus être converti en GZRS, GRS ou RA-GRS. Ce choix est irréversible sur ce compte.

Remplacez `<yourStorageAccountName>` et `<yourResourceGroup>` dans la commande suivante, puis utilisez celle-ci pour activer les partages de fichiers volumineux sur votre compte existant :

```PowerShell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Créer un partage de fichiers volumineux

### <a name="portal"></a>Portail

La création d’un partage de fichiers volumineux est presque identique à la création d’un partage de fichiers standard. La principale différence est que vous pouvez définir un quota allant jusqu’à 100 Tio.

1. À partir de votre compte de stockage, sélectionnez **Partages de fichiers**.
1. Sélectionnez **+ Partage de fichiers**.
1. Entrez un nom pour votre partage de fichiers et (éventuellement) la taille de quota souhaitée, jusqu’à 100 Tio, puis sélectionnez **Créer**. 

![large-file-shares-create-share.png](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>Interface de ligne de commande

Une fois que vous avez activé les partages de fichiers volumineux sur votre compte de stockage, vous pouvez créer des partages de fichiers dans ce compte avec des quotas supérieurs. Remplacez `<yourStorageAccountName>`, `<yourStorageAccountKey>` et `<yourFileShareName>` dans la commande suivante par vos valeurs. Vous pouvez ensuite l’utiliser pour créer un partage de fichiers volumineux :

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Une fois que vous avez activé les partages de fichiers volumineux sur votre compte de stockage, vous pouvez créer des partages de fichiers dans ce compte avec des quotas supérieurs. Remplacez `<YourStorageAccountName>`, `<YourStorageAccountKey>` et `<YourStorageAccountFileShareName>` dans la commande suivante par vos valeurs. Vous pouvez ensuite l’utiliser pour créer un partage de fichiers volumineux :

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Étendre des partages de fichiers existants

### <a name="portal"></a>Portail

Une fois que vous avez activé les partages de fichiers volumineux sur votre compte de stockage, vous pouvez étendre des partages existants au quota supérieur.

1. À partir de votre compte de stockage, sélectionnez **Partages de fichiers**.
1. Cliquez avec le bouton droit sur votre partage de fichiers et sélectionnez **Quota**.
1. Entrez la nouvelle taille souhaitée, puis sélectionnez **OK**.

![update-large-file-share-quota.png](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>Interface de ligne de commande

Une fois que vous avez activé les partages de fichiers volumineux sur votre compte de stockage, vous pouvez étendre les partages de fichiers existants dans ce compte aux quotas supérieurs. Remplacez `<yourStorageAccountName>`, `<yourStorageAccountKey>` et `<yourFileShareName>` dans la commande suivante par vos valeurs. Vous pouvez ensuite l’utiliser pour définir le quota sur la taille maximale :

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Une fois que vous avez activé les partages de fichiers volumineux sur votre compte de stockage, vous pouvez étendre les partages de fichiers existants dans ce compte aux quotas supérieurs. Remplacez `<YourStorageAccountName>`, `<YourStorageAccountKey>` et `<YourStorageAccountFileShareName>` dans la commande suivante par vos valeurs. Vous pouvez ensuite l’utiliser pour définir le quota sur la taille maximale :

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Étapes suivantes

* [Connexion et montage du partage de fichiers – Windows](storage-how-to-use-files-windows.md)
* [Connexion et montage du partage de fichiers – Linux](../storage-how-to-use-files-linux.md)
* [Connexion et montage du partage de fichiers – MacOS](storage-how-to-use-files-mac.md)