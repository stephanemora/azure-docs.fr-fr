---
title: 'Démarrage rapide : Créer un compte de stockage - Stockage Azure'
description: Dans ce guide de démarrage rapide, vous apprenez à créer un compte de stockage à l’aide du portail Azure, d’Azure PowerShell ou de l’interface Azure CLI. Un compte de stockage Azure fournit un espace de noms unique dans Microsoft Azure pour stocker les objets de données que vous créez dans le stockage Azure et y accéder.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 09/18/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 5086c0758e7c535b65c877917dc790dafa46f763
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994712"
---
# <a name="create-a-storage-account"></a>Créez un compte de stockage.

Dans ce guide de démarrage rapide, vous apprenez à créer un compte de stockage à l’aide du [portail Azure](https://portal.azure.com/), d’[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) ou d’[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Aucune.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Ce démarrage rapide requiert le module Azure PowerShell Az version 0.7 ou ultérieure. Exécutez `Get-Module -ListAvailable Az` pour rechercher votre version actuelle. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

Vous pouvez vous connecter à Azure et exécuter des commandes Azure CLI de l’une des deux façons :

- Vous pouvez exécuter des commandes CLI à partir du portail Azure, dans Azure Cloud Shell. 
- Vous pouvez installer la CLI et exécuter des commandes CLI localement.  

### <a name="use-azure-cloud-shell"></a>Utiliser Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes Bash gratuit, que vous pouvez exécuter directement dans le portail Azure. L’interface Azure CLI est préinstallée et configurée pour être utilisée avec votre compte. Cliquez sur le bouton **Cloud Shell** du menu situé dans l’angle supérieur droit de la fenêtre du portail Azure :

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Ce bouton lance un interpréteur de commandes interactif que vous pouvez utiliser pour exécuter les étapes de ce démarrage rapide :

[![Capture d’écran représentant la fenêtre Cloud Shell du portail](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Installer la CLI localement

Vous pouvez également installer et utiliser Azure CLI localement. Ce guide de démarrage rapide nécessite que vous exécutiez Azure CLI version 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli). 

---

## <a name="log-in-to-azure"></a>Connexion à Azure

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Connectez-vous au [portail Azure](https://portal.azure.com).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Connectez-vous à votre abonnement Azure avec la commande `Connect-AzAccount` et suivez les instructions à l’écran pour l’authentification.

```powershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

Pour lancer Azure Cloud Shell, connectez-vous au [portail Azure](https://portal.azure.com).

Pour vous connecter à votre installation locale de la CLI, exécutez la commande de connexion :

```cli
az login
```

---

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

À présent, vous êtes prêt à créer votre compte de stockage.

Chaque compte de stockage doit appartenir à un groupe de ressources Azure. Un groupe de ressources est un conteneur logique servant à grouper vos services Azure. Lorsque vous créez un compte de stockage, vous avez le choix entre créer un groupe de ressources ou utiliser un groupe de ressources existant. Ce guide de démarrage rapide montre comment créer un groupe de ressources. 

Un compte de stockage **v2 à usage général** fournit un accès à tous les services de Stockage Azure : objets blob, fichiers, files d’attente, tables et disques. Le guide de démarrage rapide crée un compte de stockage v2 à usage général, mais les étapes pour créer un autre type de compte de stockage sont similaires.   

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Tout d’abord, créez un groupe de ressources avec PowerShell au moyen de la commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) : 

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
New-AzResourceGroup -Name $resourceGroup -Location $location 
```

Si vous ne savez pas quelle région spécifier pour le paramètre `-Location`, vous pouvez récupérer la liste des régions prises en charge pour votre abonnement avec la commande [Get-AzLocation](/powershell/module/az.resources/get-azlocation) :

```powershell
Get-AzLocation | select Location 
$location = "westus"
```

Ensuite, créez un compte de stockage v2 à usage général avec le stockage localement redondant (LRS). Utilisez la commande [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) : 

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 
```

Pour créer un compte de stockage à usage général v2 avec l’option Stockage redondant interzone (ZRS en préversion), Stockage géoredondant (GRS) ou Stockage géoredondant avec accès en lecture (RA-GRS), remplacez la valeur souhaitée dans le tableau ci-dessous pour le paramètre **SkuName**. 

|Option de réplication  |Paramètre SkuName  |
|---------|---------|
|Stockage localement redondant (LRS)     |Standard_LRS         |
|Stockage redondant interzone (ZRS)     |Standard_ZRS         |
|Stockage géo-redondant (GRS)     |Standard_GRS         |
|Stockage géo-redondant avec accès en lecture (RA-GRS)     |Standard_RAGRS         |

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

Tout d’abord, créez un groupe de ressources avec Azure CLI par le biais de la commande [az group create](/cli/azure/group#az_group_create). 

```azurecli-interactive
az group create \
    --name storage-quickstart-resource-group \
    --location westus
```

Si vous ne savez pas quelle région spécifier pour le paramètre `--location`, vous pouvez récupérer la liste des régions prises en charge pour votre abonnement avec la commande [az account list-locations](/cli/azure/account#az_account_list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Ensuite, créez un compte de stockage v2 à usage général avec le stockage localement redondant. Utilisez la commande [az storage account create](/cli/azure/storage/account#az_storage_account_create) :

```azurecli-interactive
az storage account create \
    --name storagequickstart \
    --resource-group storage-quickstart-resource-group \
    --location westus \
    --sku Standard_LRS \
    --kind StorageV2
```

Pour créer un compte de stockage à usage général v2 avec l’option Stockage redondant interzone (préversion ZRS), Stockage géoredondant (GRS) ou Stockage géoredondant avec accès en lecture (RA-GRS), remplacez la valeur souhaitée dans le tableau ci-dessous pour le paramètre **sku**. 

|Option de réplication  |Paramètre sku  |
|---------|---------|
|Stockage localement redondant (LRS)     |Standard_LRS         |
|Stockage redondant interzone (ZRS)     |Standard_ZRS         |
|Stockage géo-redondant (GRS)     |Standard_GRS         |
|Stockage géo-redondant avec accès en lecture (RA-GRS)     |Standard_RAGRS         |

---

Pour plus d’informations sur les options de réplication disponibles, consultez [Options de réplication de stockage](storage-redundancy.md).

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez supprimer les ressources créées par ce démarrage rapide, vous pouvez simplement supprimer le groupe de ressources. La suppression du groupe de ressources efface également le compte de stockage associé et d’autres ressources liées au groupe de ressources.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Pour supprimer un groupe de ressources dans le portail Azure :

1. Sur le portail Azure, développez le menu de gauche pour ouvrir le menu des services, et sélectionnez **Groupes de ressources** pour afficher la liste de vos groupes de ressources.
2. Recherchez le groupe de ressources à supprimer, puis faites un clic droit sur le bouton **Plus** (**...**) se trouvant à droite de la liste.
3. Sélectionnez **Supprimer le groupe de ressources** et confirmez.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Pour supprimer le groupe de ressources et les ressources associées, y compris le nouveau compte de stockage, utilisez la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) : 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

Pour supprimer le groupe de ressources et les ressources associées, y compris le nouveau compte de stockage, utilisez la commande [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name storage-quickstart-resource-group
```

---

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un compte de stockage standard à usage général v2. Pour savoir comment charger et télécharger des objets blob vers/à partir de votre compte de stockage, passez au guide de démarrage rapide du stockage Blob.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Utiliser des objets blob avec le portail Azure](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Utiliser des objets blob avec PowerShell](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Utiliser des objets blob avec l’interface Azure CLI](../blobs/storage-quickstart-blobs-cli.md)

---
