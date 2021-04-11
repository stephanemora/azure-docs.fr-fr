---
title: 'Démarrage rapide : Bibliothèque Stockage Blob Azure v12 - Python'
description: Dans ce guide de démarrage rapide, vous apprenez à utiliser la bibliothèque cliente Stockage Blob Azure version 12 pour Python afin de créer un conteneur et un objet blob dans le stockage (d’objets) blob. Vous apprenez ensuite à télécharger l’objet blob sur votre ordinateur local et à lister tous les objets blob dans un conteneur.
author: twooley
ms.author: twooley
ms.date: 01/28/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: a5d56b20bec7e668bb2119c9174fe03278c805c1
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278012"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Démarrage rapide : Gérer des objets blob avec le SDK Python v12

Dans ce guide de démarrage rapide, vous découvrez comment gérer des objets blob avec Python. Les objets blob sont des objets pouvant contenir de grandes quantités de texte ou de données binaires, notamment des images, des documents, des médias en streaming et des données d’archive. Vous allez charger, télécharger et lister des objets blob, et vous allez créer et supprimer des conteneurs.

Autres ressources :

* [Documentation de référence de l’API](/python/api/azure-storage-blob)
* [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob)
* [Package (Python Package Index)](https://pypi.org/project/azure-storage-blob/)
* [Exemples](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Un compte de stockage Azure. [Créer un compte de stockage](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/) 2.7 ou 3.6 et versions ultérieures.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Configuration

Cette section vous guide tout au long de la préparation d’un projet à utiliser avec la bibliothèque cliente Stockage Blob Azure v12 pour Python.

### <a name="create-the-project"></a>Créer le projet

Créez une application Python nommée *blob-quickstart-v12*.

1. Dans une fenêtre de console (telle que cmd, PowerShell ou Bash), créez un nouveau répertoire pour le projet.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Basculez vers le répertoire *blob-quickstart-v12* nouvellement créé.

    ```console
    cd blob-quickstart-v12
    ```

1. Dans le répertoire *blob-quickstart-v12*, créez un autre répertoire nommé *data*. Ce répertoire est l’endroit où les fichiers de données Blob seront créés et stockés.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Installer le package

Alors que vous êtes toujours dans le répertoire de l’application, installez le package de la bibliothèque cliente Stockage Blob Azure pour Python à l’aide de la commande `pip install`.

```console
pip install azure-storage-blob
```

Cette commande installe le package de la bibliothèque cliente Stockage Blob Azure pour Python et toutes les bibliothèques dépendantes. Dans ce cas, il s’agit simplement de la bibliothèque Azure Core pour Python.

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

À partir du répertoire de projet :

1. Ouvrez un nouveau fichier texte dans votre éditeur de code
1. Ajoutez les instructions `import`
1. Créer la structure du programme, y compris la gestion des exceptions de base

    Voici le code :

    :::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/app_framework.py":::

1. Enregistrez le nouveau fichier sous *blob-quickstart-v12.py* dans le répertoire *blob-quickstart-v12*.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modèle objet

Stockage Blob Azure est optimisé pour stocker des quantités massives de données non structurées. Les données non structurées sont des données qui n’obéissent pas à un modèle ou une définition de données en particulier, comme des données texte ou binaires. Le stockage Blob offre trois types de ressources :

* Le compte de stockage
* Un conteneur dans le compte de stockage.
* Un blob dans le conteneur

Le diagramme suivant montre la relation entre ces ressources.

![Diagramme de l’architecture du stockage Blob](./media/storage-blobs-introduction/blob1.png)

Utilisez les classes Python suivantes pour interagir avec ces ressources :

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): La classe `BlobServiceClient` vous permet de manipuler les ressources de stockage Azure et les conteneurs blob.
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient) : La classe `ContainerClient` vous permet de manipuler des conteneurs de stockage Azure et leurs blobs.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) : La classe `BlobClient` vous permet de manipuler des blobs de stockage Azure.

## <a name="code-examples"></a>Exemples de code

Ces exemples d’extraits de code vous montrent comment effectuer les tâches suivantes avec la bibliothèque cliente Stockage Blob Azure pour Python :

* [Obtenir la chaîne de connexion](#get-the-connection-string)
* [Créer un conteneur](#create-a-container)
* [Charger des objets blob sur un conteneur](#upload-blobs-to-a-container)
* [Lister les objets blob d’un conteneur](#list-the-blobs-in-a-container)
* [Télécharger des objets blob](#download-blobs)
* [Supprimer un conteneur](#delete-a-container)

### <a name="get-the-connection-string"></a>Obtenir la chaîne de connexion

Le code ci-dessous récupère la chaîne de connexion du compte de stockage à partir de la variable d’environnement créée dans la section [Configurer votre chaîne de connexion de stockage](#configure-your-storage-connection-string).

Ajoutez ce code dans le bloc `try` :

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Créez un conteneur.

Choisissez un nom pour le nouveau conteneur. Le code ci-dessous ajoute une valeur UUID au nom du conteneur pour garantir son unicité.

> [!IMPORTANT]
> Les noms de conteneurs doivent être en minuscules. Pour plus d’informations sur l’affectation de noms aux conteneurs et objets blob, consultez [Affectation de noms et références aux conteneurs, objets blob et métadonnées](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Créez une instance de la classe [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) en appelant la méthode [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-). Ensuite, appelez la méthode [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) pour effectivement créer le conteneur dans votre compte de stockage.

Ajoutez ce code à la fin du bloc `try` :

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Charger des objets blob sur un conteneur

L’extrait de code suivant :

1. Crée un répertoire local où seront stockés les fichiers de données.
1. Crée un fichier texte dans le répertoire local.
1. Obtient une référence à un objet [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) en appelant la méthode [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) sur le [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) à partir de la section [Créer un conteneur](#create-a-container).
1. Charge le fichier texte local dans l’objet Blob en appelant la méthode [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-).

Ajoutez ce code à la fin du bloc `try` :

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

Répertoriez les objets Blob dans le conteneur en appelant la méthode [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-). Dans ce cas, un seul objet blob a été ajouté au conteneur. Il n’y a donc qu’un objet blob répertorié.

Ajoutez ce code à la fin du bloc `try` :

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Télécharger des objets blob

Téléchargez l’objet Blob créé précédemment en appelant la méthode [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-). L’exemple de code ajoute le suffixe « DOWNLOAD » au nom de fichier afin que vous puissiez voir les deux fichiers dans votre système de fichiers local.

Ajoutez ce code à la fin du bloc `try` :

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Supprimer un conteneur

Le code suivant nettoie les ressources créées par l’application en supprimant l’ensemble du conteneur avec la méthode [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-). Si vous voulez, vous pouvez aussi supprimer les fichiers locaux.

L’application s’interrompt pour une entrée de l’utilisateur en appelant `input()` avant de supprimer l’objet blob, le conteneur et les fichiers locaux. Vérifiez que les ressources ont été créées correctement, avant de les supprimer.

Ajoutez ce code à la fin du bloc `try` :

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CleanUp":::

## <a name="run-the-code"></a>Exécuter le code

Cette application crée un fichier de test dans votre dossier local et le charge dans Stockage Blob Azure. L’exemple liste ensuite le ou les objets blob du conteneur et télécharge le fichier sous un nouveau nom. Vous pouvez comparer les anciens fichiers avec les nouveaux.

Accédez au répertoire contenant le fichier *blob-quickstart-v12.py*, puis exécutez la commande `python` suivante pour exécuter l’application.

```console
python blob-quickstart-v12.py
```

La sortie de l’application ressemble à l’exemple suivant :

```output
Azure Blob Storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Avant de commencer le processus de nettoyage, vérifiez la présence des deux fichiers dans votre dossier *data*. Vous pouvez les ouvrir et constater qu’ils sont identiques.

Une fois les fichiers vérifiés, appuyez sur **Entrée** pour supprimer les fichiers de test et terminer la démonstration.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à charger, télécharger et répertorier des objets blob avec Python.

Pour afficher des exemples d’applications de stockage blob, passez à :

> [!div class="nextstepaction"]
> [Exemples Python du SDK Stockage Blob Azure v12](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Pour plus d’informations, consultez les [bibliothèques de client Stockage Azure pour Python](/azure/developer/python/sdk/storage/overview).
* Pour des tutoriels, des exemples, des guides de démarrage rapide et autre documentation, visitez [Azure pour les développeurs Python](/azure/python/).
