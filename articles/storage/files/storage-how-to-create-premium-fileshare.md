---
title: Créer un partage de fichiers Azure premium
description: Dans cet article, découvrez comment créer un partage de fichiers Azure Premium en utilisant le portail Azure, PowerShell ou l’interface Azure CLI.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli
ms.openlocfilehash: adeb1635489441b30c15fee69922e3abef0a53f9
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87903814"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>Créer un partage de fichiers premium via le portail Azure
Les partages de fichiers premium sont proposés sur les supports de stockage SSD et sont utiles pour les charges de travail gourmandes en E/S, notamment l’hébergement de bases de données et le calcul haute performance (HPC). Les partages de fichiers premium sont hébergés dans un type de compte de stockage spécial, appelé compte FileStorage. Les partages de fichiers premium sont conçus pour les applications d’entreprise évolutives et à hautes performances, en fournissant des partages à latence faible et homogène, nombre élevé d’IOPS et débit élevé.

Cet article vous montre comment créer ce nouveau type de compte en utilisant le [portail Azure](https://portal.azure.com/), Azure PowerShell et Azure CLI.

## <a name="prerequisites"></a>Prérequis

Pour accéder aux ressources Azure, dont les partages de fichiers Azure premium, vous aurez besoin d’un abonnement Azure. Si vous n’avez pas d’abonnement, vous pouvez [créer un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Créer un partage de fichiers premium en utilisant le portail Azure

### <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com/).

### <a name="create-a-filestorage-storage-account"></a>Créer un compte de stockage FileStorage

À présent, vous êtes prêt à créer votre compte de stockage.

Chaque compte de stockage doit appartenir à un groupe de ressources Azure. Un groupe de ressources est un conteneur logique servant à grouper vos services Azure. Lorsque vous créez un compte de stockage, vous avez le choix entre créer un groupe de ressources ou utiliser un groupe de ressources existant. Cet article montre comment créer un groupe de ressources.

1. Dans le portail Azure, sélectionnez **Comptes de stockage** dans le menu de gauche.

    ![Sur la page principale du portail Azure, sélectionnez le compte de stockage](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Sur la fenêtre **Comptes de stockage**, sélectionnez **Ajouter**.
1. Sélectionnez l’abonnement dans lequel créer le compte de stockage.
1. Sous le champ **Groupe de ressources**, sélectionnez **Créer**. Entrez un nom pour votre nouveau groupe de ressources, comme indiqué dans l’image suivante.

1. Ensuite, entrez un nom pour votre compte de stockage. Le nom choisi doit être unique dans tout Azure. Le nom doit aussi contenir entre 3 et 24 caractères, et uniquement des lettres minuscules et des chiffres.
1. Sélectionnez l’emplacement de votre compte de stockage ou utilisez l’emplacement par défaut.
1. Pour **Performances**, sélectionnez **Premium**.

    Vous devez sélectionner **Premium** pour que **FileStorage** soit une option disponible dans la liste déroulante **Type de compte**.

1. Sélectionnez **Type de compte** et choisissez **FileStorage**.
1. Laissez **Réplication** défini sur sa valeur par défaut, **Stockage localement redondant (LRS)** .

    ![Création d'un compte de stockage pour un partage de fichiers premium](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Cliquez sur **Vérifier + créer** pour passer en revue vos paramètres de compte de stockage et créer le compte.
1. Sélectionnez **Create** (Créer).

Une fois que votre ressource de compte de stockage a été créée, accédez-y.

### <a name="create-a-premium-file-share"></a>Créer un partage de fichiers premium

1. Dans le menu de gauche du compte de stockage, faites défiler jusqu’à la section **Service de fichiers**, puis sélectionnez **Fichiers**.
1. Sélectionnez **Partage de fichiers** pour créer un partage de fichiers premium.
1. Saisissez un nom et le quota souhaité pour votre partage de fichiers, puis sélectionnez **Créer**.

> [!NOTE]
> Les tailles de partage configurées sont spécifiées par le quota de partage. Les partages de fichiers sont facturés selon la taille configurée. Reportez-vous à la [page de tarification](https://azure.microsoft.com/pricing/details/storage/files/) pour plus d’informations.

   ![Créer un partage de fichiers premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer les ressources créées dans cet article, vous pouvez simplement supprimer le groupe de ressources. La suppression du groupe de ressources efface également le compte de stockage associé et d’autres ressources liées au groupe de ressources.

## <a name="create-a-premium-file-share-using-powershell"></a>Créer un partage de fichiers premium avec PowerShell

### <a name="create-an-account-using-powershell"></a>Créer un compte à l’aide de PowerShell

Commencez par installer la dernière version du module [PowerShellGet](/powershell/scripting/gallery/installing-psget).

Ensuite, mettez à niveau votre module PowerShell, connectez-vous à votre abonnement Azure, et créez un groupe de ressources, puis un compte de stockage.

### <a name="upgrade-your-powershell-module"></a>Mettre à niveau votre module PowerShell

Pour interagir avec un partage de fichiers premium à partir de PowerShell, vous devrez installer le module Az.Storage 1.4.0 ou la version la plus récente.

Commencez par ouvrir une session PowerShell avec des autorisations élevées.

Installez le module Az.Storage :

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Se connecter à votre abonnement Azure

Utilisez la commande `Connect-AzAccount` et suivez les instructions à l’écran pour l’authentification.

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Pour créer un groupe de ressources avec PowerShell, utilisez la commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-storage-account"></a>Créer un compte de stockage FileStorage

Pour créer un compte de stockage FileStorage à partir de PowerShell, utilisez la commande [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) :

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Créer un partage de fichiers premium

Maintenant que vous avez un compte FileStorage, vous pouvez créer un partage de fichiers premium. Utilisez l’applet de commande [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) pour en créer un.

> [!NOTE]
> Les tailles de partage configurées sont spécifiées par le quota de partage. Les partages de fichiers sont facturés selon la taille configurée. Reportez-vous à la [page de tarification](https://azure.microsoft.com/pricing/details/storage/files/) pour plus d’informations.

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer le groupe de ressources et les ressources associées, y compris le nouveau compte de stockage, utilisez la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) : 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Créer un partage de fichiers premium avec Azure CLI

Pour démarrer Azure Cloud Shell, connectez-vous au [portail Azure](https://portal.azure.com).

Si vous souhaitez vous connecter à votre installation locale de l’interface CLI, vérifiez tout d’abord que vous avez la version la plus récente, puis exécutez la commande de connexion :

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Pour créer un groupe de ressources avec Azure CLI, utilisez la commande [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>Créer un compte de stockage FileStorage

Pour créer un compte de stockage FileStorage à partir d’Azure CLI, utilisez la commande [az storage account create](/cli/azure/storage/account).

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Obtenir la clé du compte de stockage

Les clés de compte de stockage contrôlent l’accès aux ressources dans un compte de stockage. Dans cet article, nous utilisons la clé afin de créer un partage de fichiers premium. Les clés sont automatiquement créées en même temps que le compte de stockage. Vous pouvez récupérer les clés de votre compte de stockage avec la commande [az storage account keys list](/cli/azure/storage/account/keys) :

```azurecli-interactive
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Créer un partage de fichiers premium

Maintenant que vous avez un compte FileStorage, vous pouvez créer un partage de fichiers premium. Utilisez la commande [az storage share create](/cli/azure/storage/share) pour en créer un.

> [!NOTE]
> Les tailles de partage configurées sont spécifiées par le quota de partage. Les partages de fichiers sont facturés selon la taille configurée. Reportez-vous à la [page de tarification](https://azure.microsoft.com/pricing/details/storage/files/) pour plus d’informations.

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer le groupe de ressources et les ressources associées, y compris le nouveau compte de stockage, utilisez la commande [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé un partage de fichiers premium. Pour en savoir plus sur les performances que ce compte offre, allez à la section sur le niveau de performances du guide de planification.

> [!div class="nextstepaction"]
> [Niveaux de partage de fichiers](storage-files-planning.md#storage-tiers)
