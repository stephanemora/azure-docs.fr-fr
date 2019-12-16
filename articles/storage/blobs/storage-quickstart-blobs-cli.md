---
title: 'Démarrage rapide : Créer un blob avec Azure CLI'
titleSuffix: Azure Storage
description: Dans ce guide de démarrage rapide, vous allez apprendre à utiliser Azure CLI pour charger un objet blob dans Stockage Azure, télécharger un objet blob et répertorier les objets blob d’un conteneur.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c913cb978796abeed5766ffa030aaeb6142320ec
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892921"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Démarrage rapide : Créer, télécharger et lister des objets blob avec Azure CLI

L’interface de ligne de commande Azure (Azure CLI) est l’expérience de ligne de commande d’Azure pour gérer les ressources Azure. Vous pouvez l’utiliser dans votre navigateur avec Azure Cloud Shell. Vous pouvez également l’installer sur Windows, Linux ou MacOS, et l’exécuter à partir de la ligne de commande. Dans ce guide de démarrage rapide, vous apprenez à utiliser Azure CLI pour charger et télécharger des données vers et à partir du stockage Blob Azure.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans ce guide de démarrage rapide. Exécutez `az --version` pour déterminer votre version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Créez un conteneur.

Les objets blob sont toujours chargés dans un conteneur. Vous pouvez organiser des groupes d’objets blob de la même façon que vous organisez vos fichiers dans les dossiers de l’ordinateur.

Créez un conteneur pour stocker des objets blob avec la commande [az storage container create](/cli/azure/storage/container).

```azurecli-interactive
az storage container create --name sample-container
```

## <a name="upload-a-blob"></a>Charger un objet blob

Stockage Blob prend en charge les objets blob de blocs, d’ajout et de pages. Les exemples de ce guide de démarrage rapide montrent comment utiliser des objets blob de blocs.

Commencez par créer un fichier à charger dans un objet blob de blocs. Si vous utilisez Azure Cloud Shell, utilisez la commande suivante pour créer un fichier :

```bash
vi helloworld
```

Lorsque le fichier s’ouvre, appuyez sur **Insérer**. Entrez *Hello world*, puis appuyez sur **Échap**. Entrez ensuite *:x*, puis appuyez sur **Entrée**.

Dans cet exemple, vous chargez un objet blob dans le conteneur que vous avez créé à la dernière étape avec la commande [az storage blob upload](/cli/azure/storage/blob). Il n’est pas nécessaire de spécifier un chemin d’accès, car le fichier a été créé dans le répertoire racine :

```azurecli-interactive
az storage blob upload \
    --container-name sample-container \
    --name helloworld \
    --file helloworld
```

Cette opération crée l’objet blob s’il n’existe pas déjà, et le remplace s’il existe. Chargez autant de fichiers que vous le souhaitez avant de continuer.

Pour charger plusieurs fichiers à la fois, vous pouvez utiliser la commande [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

Listez les objets blob d’un conteneur avec la commande [az storage blob list](/cli/azure/storage/blob).

```azurecli-interactive
az storage blob list \
    --container-name sample-container \
    --output table
```

## <a name="download-a-blob"></a>Télécharger un objet blob

Utilisez la commande [az storage blob download](/cli/azure/storage/blob) pour télécharger l’objet blob chargé au préalable.

```azurecli-interactive
az storage blob download \
    --container-name sample-container \
    --name helloworld \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Transfert de données avec AzCopy

L’utilitaire [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) est une autre option hautes performances pour transférer des données par script avec le stockage Azure. Vous pouvez utiliser AzCopy pour transférer des données à destination et à partir du stockage Blob, Table et Fichier.

L’exemple suivant utilise AzCopy pour charger un fichier appelé *myfile.txt* dans le conteneur *sample-container*. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://<account-name>.blob.core.windows.net/sample-container \
    --dest-key <account-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des ressources de votre groupe de ressources, y compris du compte de stockage que vous avez créé dans ce guide de démarrage rapide, supprimez le groupe de ressources avec la commande [az group delete](/cli/azure/group). N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli-interactive
az group delete --name <resource-group-name>
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à transférer des fichiers entre un système de fichiers local et un conteneur du stockage Blob Azure. Pour en savoir plus sur l’utilisation des objets blob dans le stockage Azure, consultez le didacticiel sur l’utilisation du stockage Blob Azure.

> [!div class="nextstepaction"]
> [Guide pratique pour des opérations de stockage Blob avec Azure CLI](storage-how-to-use-blobs-cli.md)
