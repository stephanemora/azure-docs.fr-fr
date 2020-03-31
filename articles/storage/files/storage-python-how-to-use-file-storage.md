---
title: Développement pour Azure Files avec Python | Microsoft Docs
description: Découvrez comment développer des services et applications Python qui utilisent Azure Files pour stocker les données de fichiers.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 139e3009722761172b7bbd57805a7f5b07e55fc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68699398"
---
# <a name="develop-for-azure-files-with-python"></a>Développement pour Azure Files avec Python
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

Ce didacticiel décrit les principes fondamentaux de l’utilisation de Python pour développer des applications ou services qui utilisent Azure Files pour stocker les données de fichiers. Dans ce didacticiel, nous allons créer une application de console simple et effectuer des actions de base avec Python et Azure Files :

* Créer des partages de fichiers Azure
* Créer des répertoires
* Énumérer des fichiers et répertoires dans un partage de fichiers Azure
* Charger, télécharger et supprimer un fichier

> [!Note]  
> Comme Azure Files est accessible sur SMB, vous pouvez écrire des applications simples qui accèdent au partage de fichiers Azure à l’aide des fonctions et classes d’E/S Python standard. Cet article indique comment écrire des applications qui utilisent le SDK Python de stockage Azure, lequel utilise l’[API REST Azure Files](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) pour communiquer avec Azure Files.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Télécharger et installer le SDK Stockage Azure pour Python

Le [SDK Stockage Azure pour Python](https://github.com/azure/azure-storage-python) nécessite Python 2.7, 3.3, 3.4, 3.5 ou 3.6.
 
## <a name="install-via-pypi"></a>Effectuer l’installation via PyPi

Pour effectuer l’installation via PyPI (Python Package Index), tapez :

```bash
pip install azure-storage-file
```

> [!NOTE]
> Si vous mettez à niveau la version 0.36 ou antérieure du SDK Stockage Azure pour Python, désinstallez l’ancien SDK à l’aide de `pip uninstall azure-storage` avant d’installer le dernier package.

Pour connaître les autres méthodes d’installation, rendez-vous sur la page consacrée au [SDK Stockage Azure pour Python sur GitHub](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Voir l’exemple d’application
Pour voir et exécuter un exemple d’application qui montre comment utiliser Python avec Azure Files, consultez [Azure Storage: Getting Started with Azure Files in Python](https://github.com/Azure-Samples/storage-file-python-getting-started). 

Pour exécuter l’exemple d’application, vous devez avoir installé les packages `azure-storage-file` et `azure-storage-common`.

## <a name="set-up-your-application-to-use-azure-files"></a>Configurer votre application pour utiliser Azure Files
Ajoutez ce qui suit vers le début de chaque fichier source Python dans lequel vous souhaitez accéder au stockage Azure Storage par programme.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Configurer une connexion à Azure Files 
L’objet `FileService` vous permet d’utiliser des partages, des répertoires et des fichiers. Le code suivant crée un objet `FileService` à l’aide du nom et de la clé du compte de stockage. Remplacez `<myaccount>` et `<mykey>` par le nom et la clé de votre compte.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Crée un partage de fichiers Azure
Dans l’exemple de code suivant, vous pouvez utiliser un objet `FileService` pour créer le partage s’il n’existe pas.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Créer un répertoire
Vous pouvez également organiser le stockage en plaçant des fichiers dans des sous-répertoires, plutôt que de tous les mettre dans le répertoire racine. Azure Files vous permet de créer autant de répertoires que le permet votre compte. Le code ci-dessous crée un sous-répertoire nommé **sampledir** sous le répertoire racine.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Énumérer des fichiers et répertoires dans un partage de fichiers Azure
Pour répertorier les fichiers et répertoires d’un partage, utilisez la méthode **list\_directories\_and\_files**. Cette méthode retourne un générateur. Le code suivant sort le **nom** de chaque fichier et répertoire d'un partage sur la console.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Charger un fichier 
Un partage de fichiers Azure contient au minimum un répertoire racine où les fichiers peuvent résider. Cette section décrit comment télécharger un fichier du stockage local vers le répertoire racine d’un partage.

Pour créer un fichier et charger des données, utilisez les méthodes `create_file_from_path`, `create_file_from_stream`, `create_file_from_bytes` ou `create_file_from_text`. Il s'agit de méthodes de haut niveau qui effectuent la segmentation nécessaire lorsque la taille des données est supérieure à 64 Mo.

`create_file_from_path` charge le contenu d’un fichier à partir du chemin spécifié, et `create_file_from_stream` charge le contenu à partir d’un flux/fichier déjà ouvert. `create_file_from_bytes` charge un tableau d’octets, et `create_file_from_text` charge la valeur texte spécifiée à l’aide de l’encodage spécifié (par défaut UTF-8).

L’exemple suivant charge le contenu du fichier **sunset.png** dans le fichier **myfile**.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="download-a-file"></a>Téléchargement d’un fichier
Pour télécharger des données à partir d’un fichier, utilisez `get_file_to_path`, `get_file_to_stream`, `get_file_to_bytes`, ou `get_file_to_text`. Il s'agit de méthodes de haut niveau qui effectuent la segmentation nécessaire lorsque la taille des données est supérieure à 64 Mo.

L’exemple suivant illustre l’utilisation de `get_file_to_path` pour télécharger le contenu du fichier **myfile** et le stocker dans le fichier **out-sunset.png**.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Supprimer un fichier
Enfin, pour supprimer un fichier, appelez `delete_file`.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>Créer un instantané de partage
Vous pouvez créer une copie à un point dans le temps de la totalité de votre partage de fichier.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Créer un instantané de partage avec des métadonnées**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Répertorier les partages et les instantanés 
Vous pouvez répertorier tous les instantanés pour un partage particulier.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Parcourir un instantané de partage
Vous pouvez parcourir le contenu d’un instantané de partage spécifique pour récupérer les fichiers et les répertoires à partir du point dans le temps correspondant.

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Récupérer un fichier d’un instantané de partage
Vous pouvez télécharger un fichier à partir d’un instantané de partage pour votre scénario de restauration.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Supprimer un instantané de partage spécifique  
Vous pouvez supprimer un instantané de partage spécifique.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Supprimer un partage quand existent des instantanés de partage
Pour supprimer un partage qui a des instantanés, vous devez d’abord supprimer la totalité de ces derniers.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris comment manipuler Azure Files avec Python, suivez ces liens pour en savoir plus.

* [Centre de développement Python](https://azure.microsoft.com/develop/python/)
* [API REST des services d’Azure Storage](https://msdn.microsoft.com/library/azure/dd179355)
* [Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python](https://github.com/Azure/azure-storage-python)
