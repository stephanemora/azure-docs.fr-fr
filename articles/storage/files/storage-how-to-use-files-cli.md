---
title: Gérer les partages de fichiers Azure à l’aide d’Azure CLI
description: Découvrez comment utiliser Azure CLI pour gérer Azure Files.
services: storage
documentationcenter: na
author: wmgries
manager: aungoo
editor: tamram
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 00fd984a6bed8691712df0d4c335d2b9d4fd3ffa
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029388"
---
# <a name="manage-azure-file-shares-using-azure-cli"></a>Gérer les partages de fichiers Azure à l’aide d’Azure CLI
[Azure Files](storage-files-introduction.md) est le système de fichiers cloud facile à utiliser de Microsoft. Les partages de fichiers Azure peuvent être montés dans Windows, Linux et macOS. Cet article vous explique les bases de l’utilisation des partages de fichiers Azure à l’aide d’Azure CLI. Découvrez comment : 

> [!div class="checklist"]
> * Créer un groupe de ressources et un compte de stockage
> * Crée un partage de fichiers Azure 
> * Créer un répertoire
> * Charger un fichier 
> * Téléchargement d’un fichier
> * Créer et utiliser un instantané de partage

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous décidez d’installer et d’utiliser Azure CLI localement, pour les étapes décrites dans cet article, vous devez exécuter Azure CLI version 2.0.4 ou ultérieure. Exécutez **az --version** pour rechercher votre version d’Azure CLI. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). 

Par défaut, les commandes Azure CLI retournent le format JavaScript Object Notation (JSON). JSON est la méthode standard d’envoi et de réception de messages des API REST. Pour simplifier l’utilisation des réponses JSON, quelques exemples dans cet article utilisent le paramètre *query* sur des commandes d’Azure CLI. Ce paramètre utilise le [langage de requête JMESPath](http://jmespath.org/) pour analyser JSON. Pour en savoir plus sur l’utilisation des résultats des commandes d’Azure CLI en suivant le langage de requête JMESPath, consultez le [didacticiel JMESPath](http://jmespath.org/tutorial.html).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Si vous n’avez pas déjà un groupe de ressources Azure, vous pouvez utiliser la commande [az group create](/cli/azure/group#create) pour en créer un. 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *Est des États-Unis* :

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Créez un compte de stockage.
Un compte de stockage est un pool partagé de stockage dans lequel vous pouvez déployer des partages de fichiers Azure, ou d’autres ressources de stockage comme les objets blob ou les files d’attente. Un compte de stockage peut contenir un nombre illimité de partages de fichiers. Un partage peut stocker un nombre illimité de fichiers, dans les limites de capacité du compte de stockage.

L’exemple suivant crée un compte de stockage nommé *mystorageaccount\<numéro aléatoire\>* avec la commande [az storage account create](/cli/azure/storage/account#create), puis place le nom de ce compte de stockage dans la variable `$STORAGEACCT`. Les noms de comptes de stockage doivent être uniques. L’utilisation de `$RANDOM` ajoute un numéro au nom du compte de stockage pour le rendre unique. 

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>Obtenir la clé du compte de stockage
Les clés de comptes de stockage contrôlent l’accès aux ressources dans un compte de stockage. Les clés sont automatiquement créées en même temps que le compte de stockage. Vous pouvez récupérer les clés de votre compte de stockage avec la commande [az storage account keys list](/cli/azure/storage/account/keys#list) : 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Crée un partage de fichiers Azure
Vous pouvez désormais créer votre premier partage de fichiers Azure. Créez des partages de fichiers avec la commande [az storage share create](/cli/azure/storage/share#create). Cet exemple crée un partage de fichiers Azure nommé *myshare* : 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

> [!IMPORTANT]  
> Le nom des partages ne peut contenir que des minuscules, des nombres et des traits d’union uniques (mais ne peut commencer par un trait d’union). Pour plus d’informations sur la façon de nommer des partages de fichiers et des fichiers, consultez [Affectation de noms et références aux partages, répertoires, fichiers et métadonnées](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="work-with-the-contents-of-an-azure-file-share"></a>Utiliser le contenu d’un partage de fichiers Azure
Maintenant que vous avez créé un partage de fichiers Azure, vous pouvez monter le partage de fichiers à l’aide de SMB sur [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) ou [macOS](storage-how-to-use-files-mac.md). Vous pouvez également utiliser votre partage de fichiers Azure à l’aide d’Azure CLI. L’avantage d’utiliser Azure CLI au lieu de monter le partage de fichiers à l’aide de SMB est tel que toutes les requêtes effectuées avec Azure CLI le sont à l’aide de l’API REST de fichier. Vous pouvez utiliser l’API REST de fichier pour créer, modifier et supprimer des fichiers et des répertoires dans votre partage de fichiers à partir de ces emplacements :

- Bash Azure Cloud Shell (qui ne peut pas monter les partages de fichiers sur SMB)
- Les clients qui ne peuvent pas monter de partages SMB, comme les clients locaux dont le port 445 n’est pas débloqué
- Les scénarios sans serveur, comme dans [Azure Functions](../../azure-functions/functions-overview.md)

### <a name="create-a-directory"></a>Créer un répertoire
Pour créer un répertoire nommé *myDirectory* à la racine de votre partage de fichiers Azure, exécutez la commande [`az storage directory create`](/cli/azure/storage/directory#az_storage_directory_create) :

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>Charger un fichier
Pour illustrer comment charger un fichier à l’aide de la commande [`az storage file upload`](/cli/azure/storage/file#az_storage_file_upload), créez d’abord un fichier à charger sur le lecteur de travail de Cloud Shell. Dans l’exemple suivant, vous créez puis chargez le fichier :

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Si vous exécutez Azure CLI en local, remplacez `~/clouddrive` par un chemin existant sur votre machine.

Après avoir chargé le fichier, vous pouvez exécuter la commande [`az storage file list`](/cli/azure/storage/file#az_storage_file_list) pour vérifier que le fichier a bien été chargé dans votre partage de fichiers Azure :

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Téléchargement d’un fichier
Vous pouvez exécuter la commande [`az storage file download`](/cli/azure/storage/file#az_storage_file_download) pour télécharger une copie du fichier chargé sur le disque de travail de Cloud Shell :

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>Copie des fichiers
Une tâche courante consiste à copier les fichiers d’un partage de fichiers vers un autre partage de fichiers, ou dans/depuis un conteneur de stockage d’objets blob Azure. Pour illustrer cette fonctionnalité, créez un nouveau partage. Copiez le fichier que vous avez chargé dans ce nouveau partage à l’aide de la commande [az storage file copy](/cli/azure/storage/file/copy) : 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2"

az storage directory create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --name "myDirectory2"

az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-share "myshare" \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share "myshare2" \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Maintenant, si vous répertoriez les fichiers dans le nouveau partage, vous devez voir votre fichier copié :

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

Même si la commande `az storage file copy start` est pratique pour déplacer des fichiers entre des partages de fichiers Azure et des conteneurs de stockage d’objets blob Azure, nous vous recommandons d’utiliser AzCopy pour des déplacements plus volumineux. (Plus volumineux en termes de nombre ou de taille des fichiers déplacés.) En savoir plus sur [AzCopy pour Linux](../common/storage-use-azcopy-linux.md) et [AzCopy pour Windows](../common/storage-use-azcopy.md). AzCopy doit être installé localement. AzCopy n’est pas disponible dans Cloud Shell. 

## <a name="create-and-modify-share-snapshots"></a>Créer et modifier des instantanés de partage
Une autre tâche utile que vous pouvez effectuer avec un partage de fichiers Azure consiste à créer des instantanés de partage. Un instantané conserve une copie d’un point dans le temps d’un partage de fichiers Azure. Les instantanés de partage sont similaires à des technologies de systèmes d’exploitation que vous connaissez peut-être déjà :
- Les instantanés du [Gestionnaire de Volume logique (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) pour les systèmes Linux
- Les instantanés du [système de fichiers Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) pour macOS
- Le [service VSS](https://docs.microsoft.com/en-us/windows/desktop/VSS/volume-shadow-copy-service-portal) pour les systèmes de fichiers Windows comme NTFS et ReFS

Vous pouvez créer un instantané de partage à l’aide de la commande [`az storage share snapshot`](/cli/azure/storage/share#az_storage_share_snapshot) :

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Parcourir le contenu des instantanés de partage
Vous pouvez parcourir le contenu d’un instantané de partage en envoyant l’horodatage de l’instantané de partage capturé dans la variable `$SNAPSHOT` dans la commande `az storage file list` :

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>Répertorier les instantanés de partage
Pour afficher la liste d’instantanés pris pour votre partage, utilisez la commande suivante :

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>Restaurer à partir d’un instantané de partage
Vous pouvez restaurer un fichier avec la commande `az storage file copy start` que vous avez utilisée précédemment. Supprimez tout d’abord le fichier SampleUpload.txt que vous avez chargé, afin de pouvoir le restaurer à partir de l’instantané :

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"

# Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI"myshare/myDirectory/SampleUpload.txt?sharesnapshot="$SNAPSHOT

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-uri $URI \
    --destination-share "myshare" \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Supprimer un instantané de partage
Vous pouvez supprimer un instantané de partage avec la commande [`az storage share delete`](/cli/azure/storage/share#az_storage_share_delete). Utilisez la variable qui contient la référence `$SNAPSHOT` au paramètre `--snapshot` :

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --snapshot $SNAPSHOT
```

## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous avez terminé, vous pouvez exécuter la commande [`az group delete`](/cli/azure/group#delete) pour supprimer le groupe de ressources et toutes les ressources associées : 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

Vous pouvez également supprimer les ressources individuellement.
- Pour supprimer les partages de fichiers Azure que vous avez créés pour cet article :

    ```azurecli-interactive
    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare" \
        --delete-snapshots include

    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare2" \
        --delete-snapshots include
    ```

- Pour supprimer le compte de stockage. (Ceci supprime implicitement les partages de fichiers Azure que vous avez créés et toute autre ressource de stockage que vous avez peut-être créée, un conteneur de stockage d’objets Blob Azure par exemple.)

    ```azurecli-interactive
    az storage account delete \
        --resource-group "myResourceGroup" \
        --name $STORAGEACCT \
        --yes
    ```

## <a name="next-steps"></a>Étapes suivantes
- [Gérer les partages de fichiers avec le portail Azure](storage-how-to-use-files-portal.md)
- [Gérer les partages de fichiers avec Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Gérer les partages de fichiers avec l’Explorateur Stockage](storage-how-to-use-files-storage-explorer.md)
- [Planifier un déploiement Azure Files](storage-files-planning.md)