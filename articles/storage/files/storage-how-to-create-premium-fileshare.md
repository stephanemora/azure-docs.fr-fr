---
title: Créer un compte de stockage fichier Azure premium
description: Dans cet article, vous allez apprendre à créer un compte de stockage fichier Azure premium et un partage de fichiers premium.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 72dec14dde47580313e57bb3b8d7315604929277
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844554"
---
# <a name="how-to-create-an-azure-premium-file-share"></a>Comment créer un partage de fichiers Azure premium

Le type de compte de stockage FileStorage (version préliminaire) représente un nouveau niveau pour les fichiers Azure, ce qui vous permet de créer des partages de fichiers avec les caractéristiques de performances premium. Ces partages de fichiers sont conçus pour hautes performances et des applications d’entreprise à l’échelle, fournissant une latence faible homogène, nombre élevé d’IOPS et débit élevé partages.

Cet article vous montre comment créer ce nouveau type de compte à l’aide [Azure portal](https://portal.azure.com/), Azure PowerShell et Azure CLI.

## <a name="prerequisites"></a>Conditions préalables

Pour accéder à Stockage Azure, vous avez besoin d’un abonnement Azure. Si vous n’avez pas d’abonnement, vous pouvez [créer un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Créer un partage de fichiers de premium à l’aide du portail Azure

### <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [Portail Azure](https://portal.azure.com/).

### <a name="create-a-filestorage-preview-storage-account"></a>Créer un compte de stockage FileStorage (version préliminaire)

Vous êtes maintenant prêt à créer votre compte de stockage.

Chaque compte de stockage doit appartenir à un groupe de ressources Azure. Un groupe de ressources est un conteneur logique servant à grouper vos services Azure. Lorsque vous créez un compte de stockage, vous avez le choix entre créer un groupe de ressources ou utiliser un groupe de ressources existant. Cet article explique comment créer un nouveau groupe de ressources.

1. Dans le portail Azure, sélectionnez **comptes de stockage** sur le menu de gauche.

    ![Page principale du portail Azure sélectionnez le compte de stockage](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Sur la fenêtre **Comptes de stockage**, sélectionnez **Ajouter**.
1. Sélectionnez l’abonnement dans lequel créer le compte de stockage.
1. Sous le champ **Groupe de ressources**, sélectionnez **Créer**. Entrez un nom pour votre nouveau groupe de ressources, comme indiqué dans l’image suivante.

1. Ensuite, entrez un nom pour votre compte de stockage. Le nom choisi doit être unique dans tout Azure. Le nom doit aussi contenir entre 3 et 24 caractères, et uniquement des lettres minuscules et des chiffres.
1. Sélectionnez l’emplacement de votre compte de stockage ou utilisez l’emplacement par défaut.
1. Pour **performances** sélectionnez **Premium**.
1. Sélectionnez **type de compte** et choisissez **FileStorage (version préliminaire)**.
1. Laissez **réplication** définie sur sa valeur par défaut de **stockage localement redondant (LRS)**.

    ![Comment créer un compte de stockage de fichiers premium](media/storage-how-to-create-premium-fileshare/premium-files-storage-account.png)

1. Cliquez sur **Vérifier + créer** pour passer en revue vos paramètres de compte de stockage et créer le compte.
1. Sélectionnez **Créer**.

Une fois que votre ressource de compte de stockage a été créé, accédez à celui-ci.

### <a name="create-a-premium-file-share"></a>Créer un partage de fichiers premium

1. Dans le menu de gauche pour le compte de stockage, faites défiler vers le **du service de fichiers** section, puis sélectionnez **fichiers (version préliminaire)**.
1. Sélectionnez **+ partage de fichiers** pour créer un partage de fichiers premium.
1. Entrez un nom et le quota souhaité pour votre partage de fichiers, puis sélectionnez **créer**.

> [!NOTE]
> Tailles de partage provisionnée est spécifié par le quota de partage, partages de fichiers sont facturés selon la taille configurée, reportez-vous à la [page de tarification](https://azure.microsoft.com/pricing/details/storage/files/) pour plus d’informations.

   ![Créer un partage de fichiers premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez nettoyer les ressources créées dans cet article, vous pouvez simplement supprimer le groupe de ressources. Suppression du groupe de ressources supprime également le compte de stockage associé, ainsi que d’autres ressources associées au groupe de ressources.

## <a name="create-a-premium-file-share-using-powershell"></a>Créer un partage de fichiers de premium à l’aide de PowerShell

### <a name="create-an-account-using-powershell"></a>Créer un compte à l’aide de PowerShell

Commencez par installer la dernière version du module [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).

Ensuite, mettez à niveau votre module PowerShell, connectez-vous à votre abonnement Azure, et créez un groupe de ressources, puis un compte de stockage.

### <a name="upgrade-your-powershell-module"></a>Mettre à niveau votre module powershell

Pour interagir avec les fichiers premium avec PowerShell, vous devez installer le dernier module Az.Storage.

Commencez par ouvrir une session PowerShell avec des autorisations élevées.

Installer le module Az.Storage :

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowPrerelease -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Se connecter à votre abonnement Azure

Utilisez la commande `Login-AzAccount` et suivez les instructions à l’écran pour l’authentification.

```powershell
Login-AzAccount
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

### <a name="create-a-filestorage-preview-storage-account"></a>Créer un compte de stockage filestorage (version préliminaire)

Pour créer un compte de stockage FileStorage (version préliminaire) à partir de PowerShell, utilisez le [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) commande :

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Créer un partage de fichiers premium

Maintenant que vous avez un compte FileStorage, vous pouvez créer un partage de fichiers premium. Utilisez le [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) applet de commande pour en créer un.

> [!NOTE]
> Tailles de partage provisionnée est spécifié par le quota de partage, partages de fichiers sont facturés selon la taille configurée, reportez-vous à la [page de tarification](https://azure.microsoft.com/pricing/details/storage/files/) pour plus d’informations.

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>Supprimer des ressources

Pour supprimer le groupe de ressources et les ressources associées, y compris le nouveau compte de stockage, utilisez la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) : 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Créer un partage de fichiers de premium à l’aide d’Azure CLI

Pour démarrer Azure Cloud Shell, connectez-vous au [portail Azure](https://portal.azure.com).

Si vous souhaitez vous connecter à votre installation locale de la CLI, exécutez la commande de connexion :

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-premium-files"></a>Ajouter l’extension CLI pour les fichiers de niveau premium d’Azure

Pour interagir avec les fichiers de premium à l’aide de l’interface CLI, vous devrez ajouter une extension à votre interpréteur de commandes.

Pour ce faire, entrez la commande ci-après à l’aide de Cloud Shell ou d’un interpréteur de commandes local : `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Pour créer un groupe de ressources avec Azure CLI, utilisez la commande [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-preview-storage-account"></a>Créer un compte de stockage FileStorage (version préliminaire)

Pour créer un compte de stockage FileStorage (version préliminaire) à partir de l’interface CLI, utilisez le [créer de compte de stockage az](/cli/azure/storage/account) commande.

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Obtenir la clé du compte de stockage

Clés de compte de stockage contrôlent l’accès aux ressources dans un compte de stockage, dans cet article, nous utilisons la clé afin de créer un partage de fichiers premium. Les clés sont automatiquement créées en même temps que le compte de stockage. Vous pouvez récupérer les clés de votre compte de stockage avec la commande [az storage account keys list](/cli/azure/storage/account/keys) :

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Créer un partage de fichiers premium

Maintenant que vous avez un compte FileStorage, vous pouvez créer un partage de fichiers premium. Utilisez le [créer de partage de stockage az](/cli/azure/storage/share) commande pour créer un.

> [!NOTE]
> Tailles de partage provisionnée est spécifié par le quota de partage, partages de fichiers sont facturés selon la taille configurée, reportez-vous à la [page de tarification](https://azure.microsoft.com/pricing/details/storage/files/) pour plus d’informations.

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>Supprimer des ressources

Pour supprimer le groupe de ressources et les ressources associées, y compris le nouveau compte de stockage, utilisez la commande [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé un compte de stockage de fichiers premium. Pour en savoir plus sur les performances de que l’offre de ce compte, passez à la section de niveau de performances du guide de planification.

> [!div class="nextstepaction"]
> [Niveaux de performances de partage de fichiers](storage-files-planning.md#file-share-performance-tiers)