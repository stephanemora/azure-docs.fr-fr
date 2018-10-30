---
title: Démarrage rapide pour la gestion de partages de fichiers Azure à l’aide d’Azure CLI
description: Utilisez ce guide de démarrage rapide pour découvrir comment utiliser Azure CLI pour gérer Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: aab248ac7c9adf7d996406ec35e0317594ce0b68
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945016"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Démarrage rapide : créer et gérer des partages de fichiers Azure à l’aide d’Azure CLI
Ce guide vous explique les bases de l’utilisation des [partages de fichiers Azure](storage-files-introduction.md) avec Azure CLI. Le partage de fichiers Azure est similaire à d’autres partages de fichiers, mais est stocké dans le cloud et s’appuie sur la plateforme Azure. Il prend en charge le protocole SMB de norme industrielle et permet le partage de fichiers entre plusieurs machines, applications et instances. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous décidez d’installer et d’utiliser Azure CLI localement, pour les étapes décrites dans cet article, vous devez exécuter Azure CLI version 2.0.4 ou ultérieure. Exécutez **az --version** pour rechercher votre version d’Azure CLI. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). 

Par défaut, les commandes Azure CLI retournent le format JavaScript Object Notation (JSON). JSON est la méthode standard d’envoi et de réception de messages des API REST. Pour simplifier l’utilisation des réponses JSON, quelques exemples dans cet article utilisent le paramètre *query* sur des commandes d’Azure CLI. Ce paramètre utilise le [langage de requête JMESPath](http://jmespath.org/) pour analyser JSON. Pour en savoir plus sur l’utilisation des résultats des commandes d’Azure CLI en suivant le langage de requête JMESPath, consultez le [didacticiel JMESPath](http://jmespath.org/tutorial.html).

## <a name="sign-in-to-azure"></a>Connexion à Azure
Si vous utilisez Azure CLI en local, ouvrez une invite et connectez-vous à Azure si ce n’est déjà fait.

```bash 
az login
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Si vous n’avez pas déjà un groupe de ressources Azure, vous pouvez utiliser la commande [az group create](/cli/azure/group#create) pour en créer un. 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *USA Est* :

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

Le nom des partages ne peut contenir que des minuscules, des nombres et des traits d’union uniques (mais ne peut commencer par un trait d’union). Pour plus d’informations sur la façon de nommer des partages de fichiers et des fichiers, consultez [Affectation de noms et références aux partages, répertoires, fichiers et métadonnées](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Utiliser votre partage de fichiers Azure
Azure Files offre deux méthodes pour utiliser les fichiers et dossiers au sein de votre partage de fichiers Azure : le [protocole Server Message Block (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) de norme industrielle et le [protocole REST de fichier](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Pour monter un partage de fichiers avec SMB, consultez le document suivant en fonction de votre système d’exploitation :
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Utilisation d’un partage de fichiers Azure avec le protocole REST de fichier 
Il est possible de travailler directement avec le protocole REST de fichier (c’est-à-dire fabriquer vous-même les appels HTTP REST manuellement), mais la méthode la plus courante d’utiliser le protocole REST de fichier consiste à utiliser Azure CLI, le [module AzureRM PowerShell](storage-how-to-use-files-powershell.md) ou le Kit de développement logiciel (SDK) du stockage Azure. Toutes ces solutions offrent un bon wrapper autour du protocole REST de fichier dans le langage de script/programmation de votre choix.  

Dans la plupart des scénarios Azure Files, vous allez utiliser votre partage de fichiers Azure sur le protocole SMB, car cela vous permet d’utiliser les applications et outils existants que l’on s’attend à utiliser. Toutefois, utiliser l’API REST de fichier plutôt que SMB est avantageux pour plusieurs raisons, par exemple :

- Vous naviguez sur votre partage de fichiers à partir d’Azure Bash Cloud Shell (qui ne peut pas monter les partages de fichiers sur SMB).
- Vous devez exécuter un script ou application à partir d’un client qui ne peut pas monter de partages SMB, notamment les clients locaux qui n’ont pas débloqué le port 445.
- Vous tirez profit de ressources serverless, comme [Azure Functions](../../azure-functions/functions-overview.md) par exemple. 

Les exemples suivants montrent comment utiliser le module AzureRM PowerShell pour manipuler votre partage de fichiers Azure avec le protocole REST de fichier. 

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

> [!div class="nextstepaction"]
> [Qu’est-ce qu’Azure Files ?](storage-files-introduction.md)