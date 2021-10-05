---
title: Démarrage rapide pour la gestion des partages de fichiers Azure
description: Découvrez comment créer et gérer des partages de fichiers Azure avec le portail Azure, l’interface Azure CLI ou le module Azure PowerShell. Créez un compte de stockage, un partage de fichiers Azure et utilisez votre partage de fichiers Azure.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 09/17/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 24a84fb0bc64c4c44d056098e8c429660b88e0e0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603521"
---
# <a name="quickstart-create-and-manage-azure-file-shares"></a>Démarrage rapide : Créer et gérer des partages de fichiers Azure
[Azure Files](storage-files-introduction.md) est le système de fichiers cloud facile à utiliser de Microsoft. Les partages de fichiers Azure peuvent être montés dans Windows, Linux et macOS. Ce guide vous explique les bases de l’utilisation des partages de fichiers Azure avec le portail Azure, l’interface Azure CLI ou le module Azure PowerShell.

## <a name="applies-to"></a>S’applique à
| Type de partage de fichiers | SMB | NFS |
|-|:-:|:-:|
| Partages de fichiers Standard (GPv2), LRS/ZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Standard (GPv2), GRS/GZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Premium (FileStorage), LRS/ZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |

## <a name="pre-requisites"></a>Conditions préalables

# <a name="portal"></a>[Portail](#tab/azure-portal)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous souhaitez installer et utiliser PowerShell localement, vous devez exécuter le module Azure PowerShell Az version 0.7 ou ultérieure pour les besoins de ce guide. Pour connaître la version du module Azure PowerShell que vous exécutez, utilisez la commande `Get-Module -ListAvailable Az`. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Login-AzAccount` pour vous connecter à votre compte Azure.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Cet article nécessite la version 2.0.4 ou ultérieure de l’interface Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

- Par défaut, les commandes Azure CLI retournent le format JavaScript Object Notation (JSON). JSON est la méthode standard d’envoi et de réception de messages des API REST. Pour simplifier l’utilisation des réponses JSON, quelques exemples dans cet article utilisent le paramètre *query* sur des commandes d’Azure CLI. Ce paramètre utilise le [langage de requête JMESPath](http://jmespath.org/) pour analyser JSON. Pour en savoir plus sur l’utilisation des résultats des commandes d’Azure CLI en suivant le langage de requête JMESPath, consultez le [didacticiel JMESPath](http://jmespath.org/tutorial.html).

---

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

# <a name="portal"></a>[Portail](#tab/azure-portal)
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)


### <a name="powershell---create-a-resource-group"></a>PowerShell – Créer un groupe de ressources
Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Si vous n’avez pas encore de groupe de ressources Azure, vous pouvez en créer un avec l’applet de commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Vous avez besoin d’un groupe de ressources pour créer un compte de stockage.

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* dans la région USA Ouest 2 :

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

### <a name="powershell---create-a-storage-account"></a>PowerShell – Créer un compte de stockage

Un compte de stockage est un pool partagé de stockage que vous pouvez utiliser pour déployer des partages de fichiers Azure.

Cet exemple crée un compte de stockage à l’aide de l’applet de commande [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Le compte de stockage est nommé *mystorageaccount\<random number>* et une référence à ce compte de stockage est stockée dans la variable **$storageAcct**. Les noms de compte de stockage doivent être uniques. Par conséquent, utilisez `Get-Random` pour ajouter un numéro au nom pour le rendre unique. 

```azurepowershell-interactive 
$storageAccountName = "mystorageacct$(Get-Random)"

$storageAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $region `
    -Kind StorageV2 `
    -SkuName Standard_LRS `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="cli---create-a-resource-group"></a>CLI – Créer un groupe de ressources
Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Si vous n’avez pas déjà un groupe de ressources Azure, vous pouvez utiliser la commande [az group create](/cli/azure/group) pour en créer un. Vous avez besoin d’un groupe de ressources pour créer un compte de stockage.

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *USA Ouest 2* :

```azurecli-interactive 
export resourceGroupName="myResourceGroup"
region="westus2"

az group create \
    --name $resourceGroupName \
    --location $region \
    --output none
```

### <a name="cli---create-a-storage-account"></a>CLI – Créer un compte de stockage
Un compte de stockage est un pool partagé de stockage dans lequel vous pouvez déployer des partages de fichiers Azure.

L’exemple suivant crée un compte de stockage à l’aide de la commande [az storage account create](/cli/azure/storage/account). Les noms de compte de stockage doivent être uniques. Par conséquent, utilisez `$RANDOM` pour ajouter un numéro au nom pour le rendre unique.

```azurecli-interactive 
export storageAccountName="mystorageacct$RANDOM"

az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --location $region \
    --kind StorageV2 \
    --sku Standard_LRS \
    --enable-large-file-share \
    --output none
```


---


## <a name="create-an-azure-file-share"></a>Créer un partage de fichiers Azure


# <a name="portal"></a>[Portail](#tab/azure-portal)

Pour créer un partage de fichiers Azure :

1. Sélectionnez le compte de stockage dans votre tableau de bord.
1. Dans la page du compte de stockage, dans la section **Services**, sélectionnez **Fichiers**.
    
    ![Capture d’écran de la section Stockage des données du compte de stockage, avec la sélection des partages de fichiers.](media/storage-how-to-use-files-portal/create-file-share-1.png)

1. Dans le menu situé en haut de la page **Service de fichiers**, cliquez sur **Partage de fichiers**. La page **Nouveau partage de fichier** s’affiche.
1. Dans **Nom**, tapez *myshare*, entrez un quota et laissez l’option **Transaction optimisée** sélectionnée pour **Niveaux**.
1. Sélectionnez **Créer** pour créer le partage de fichiers Azure.

Le nom des partages ne doit contenir que des minuscules, des nombres et des traits d’union uniques, mais ne peut commencer par un trait d’union. Pour plus d’informations sur la façon de nommer des partages de fichiers et des fichiers, consultez la rubrique [Affectation de noms et références aux partages, répertoires, fichiers et métadonnées](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Maintenant que vous avez créé un compte de stockage, vous pouvez créer votre premier partage de fichiers Azure. Créez un partage de fichiers à l’aide de l’applet de commande [New-AzRmStorageShare](/powershell/module/az.storage/New-AzRmStorageShare). Cet exemple crée un partage nommé **myshare**.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Maintenant que vous avez créé un compte de stockage, vous pouvez créer votre premier partage de fichiers Azure. Créez des partages de fichiers avec la commande [az storage share-rm create](/cli/azure/storage/share-rm?view=azure-cli-latest&preserve-view=false#az_storage_share_rm_create). Cet exemple crée un partage de fichiers Azure nommé **myshare** : 

```azurecli-interactive
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --quota 1024 \
    --enabled-protocols SMB \
    --output none
```

---

#### <a name="create-a-directory"></a>Créer un répertoire

# <a name="portal"></a>[Portail](#tab/azure-portal)

Pour créer un répertoire nommé *myDirectory* à la racine de votre partage de fichiers Azure :

1. Dans la page **Service de fichiers**, sélectionnez le partage de fichiers **myshare**. La page de votre partage de fichiers s’ouvre.
1. Dans le menu situé en haut de la page, sélectionnez **+ Ajouter un répertoire**. La page **Nouveau répertoire** s’affiche.
1. Saisissez *myDirectory* et cliquez sur **OK**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour créer un répertoire nommé **myDirectory** à la racine de votre partage de fichiers Azure, utilisez l’applet de commande [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory).

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour créer un répertoire nommé **myDirectory** à la racine de votre partage de fichiers Azure, exécutez la commande [`az storage directory create`](/cli/azure/storage/directory) :

```azurecli-interactive
az storage directory create \
   --account-name $storageAccountName \
   --account-key $storageAccountKey \
   --share-name $shareName \
   --name "myDirectory" \
   --output none
```

---


#### <a name="upload-a-file"></a>Charger un fichier 
# <a name="portal"></a>[Portail](#tab/azure-portal)


Pour charger un fichier, vous devez tout d’abord créer ou sélectionner un fichier à charger. Vous pouvez le faire selon la méthode de votre choix. Une fois que vous avez sélectionné le fichier que vous voulez charger :

1. Sélectionnez le répertoire **myDirectory**. Le volet **myDirectory** s’ouvre.
1. Dans le menu supérieur, sélectionnez **Charger**. Le panneau **Télécharger des fichiers** s’ouvre.  
    
    ![Capture d’écran du panneau Télécharger des fichiers](media/storage-how-to-use-files-portal/upload-file-1.png)

1. Sélectionnez l’icône de dossier pour ouvrir une fenêtre permettant de parcourir vos fichiers locaux. 
1. Sélectionnez un fichier, puis sélectionnez **Ouvrir**. 
1. Dans la page **Charger des fichiers**, vérifiez le nom du fichier, puis sélectionnez **Télécharger**.
1. Une fois terminé, le fichier apparaît dans la liste sur la page **myDirectory**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour montrer comment charger un fichier avec l’applet de commande [Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent), nous devons d’abord créer un fichier dans le disque de travail de PowerShell Cloud Shell à charger. 

Cet exemple place la date et l’heure actuelles dans un nouveau fichier sur votre disque de travail, puis charge le fichier vers le partage de fichiers.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
cd "~/CloudDrive/"
Get-Date | Out-File -FilePath "SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Source "SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Si vous exécutez PowerShell en local, remplacez `~/CloudDrive/` par un chemin existant sur votre machine.

Après avoir chargé le fichier, vous pouvez exécuter l’applet de commande [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) pour vérifier que le fichier a bien été chargé dans votre partage de fichiers Azure. 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour illustrer comment charger un fichier à l’aide de la commande [`az storage file upload`](/cli/azure/storage/file), créez d’abord un fichier à charger sur le lecteur de travail de Cloud Shell. Dans l’exemple suivant, vous créez puis chargez le fichier :

```azurecli-interactive
cd ~/clouddrive/
date > SampleUpload.txt

az storage file upload \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --source "SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Si vous exécutez Azure CLI en local, remplacez `~/clouddrive` par un chemin existant sur votre machine.

Après avoir chargé le fichier, vous pouvez exécuter la commande [`az storage file list`](/cli/azure/storage/file) pour vérifier que le fichier a bien été chargé dans votre partage de fichiers Azure :

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory" \
    --output table
```


---

#### <a name="download-a-file"></a>Téléchargement d’un fichier
# <a name="portal"></a>[Portail](#tab/azure-portal)

Vous pouvez télécharger une copie du fichier que vous avez chargé en cliquant avec le bouton droit sur le fichier. Après avoir sélectionné le bouton de téléchargement, l’expérience exacte dépend du système d’exploitation et du navigateur que vous utilisez.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vous pouvez utiliser l’applet de commande [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) pour télécharger une copie du fichier que vous avez chargé sur le disque de travail de Cloud Shell.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
    -Path "SampleDownload.txt" `
    -Force `
    -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt" `
    -Destination "SampleDownload.txt"
```

Une fois le fichier téléchargé, vous pouvez utiliser `Get-ChildItem` pour voir si le fichier a été téléchargé sur votre disque de travail de PowerShell Cloud Shell.

```azurepowershell-interactive
Get-ChildItem | Where-Object { $_.Name -eq "SampleDownload.txt" }
``` 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Vous pouvez exécuter la commande [`az storage file download`](/cli/azure/storage/file) pour télécharger une copie du fichier chargé sur le disque de travail de Cloud Shell :

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -f SampleDownload.txt

az storage file download \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --dest "SampleDownload.txt" \
    --output none
```

---

## <a name="clean-up-resources"></a>Nettoyer les ressources
# <a name="portal"></a>[Portail](#tab/azure-portal)

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Une fois que vous avez terminé, vous pouvez utiliser l’applet de commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources et toutes les ressources contenues dans le groupe de ressources. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Une fois que vous avez terminé, vous pouvez utiliser la commande [`az group delete`](/cli/azure/group) pour supprimer le groupe de ressources et toutes les ressources contenues dans le groupe de ressources : 

```azurecli-interactive 
az group delete --name $resourceGroupName
```


---

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Qu’est-ce qu’Azure Files ?](storage-files-introduction.md)