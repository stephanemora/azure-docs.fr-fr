---
title: SDK Python Azure Data Lake Storage Gen2 pour les fichiers et les listes de contrôle d’accès (préversion)
description: Utilisez Python pour gérer les répertoires et les listes de contrôle d’accès de fichiers et de répertoires dans des comptes de stockage dotés d’un espace de noms hiérarchique (HNS) activé.
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: e07bb6aa9d1fe22baaebb7bc7239ce03a728c6b9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431811"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Utilisez Python pour gérer les répertoires, les fichiers et les listes de contrôle d’accès dans Azure Data Lake Storage Gen2 (préversion)

Cet article vous explique comment utiliser Python pour créer et gérer des répertoires, des fichiers et des autorisations dans des comptes de stockage dotés d’un espace de noms hiérarchique (HNS) activé. 

> [!IMPORTANT]
> La bibliothèque cliente Azure Data Lake Storage pour Python est actuellement en version préliminaire publique.

[Package (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/) | [Exemples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [Référence de l’API](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html) | [Mappage de Gen1 à Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Conditions préalables requises

> [!div class="checklist"]
> * Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un compte de stockage doté d’un espace de noms hiérarchique (HNS) activé. Pour créer un test, suivez [ces](data-lake-storage-quickstart-create-account.md) instructions.

## <a name="set-up-your-project"></a>Configuration de votre projet

Installez la bibliothèque cliente Azure Data Lake Storage pour Python à l’aide de [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake --pre
```

Ajoutez les instructions d’importation suivantes au début de votre fichier de code.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
```

## <a name="connect-to-the-account"></a>Se connecter au compte

Pour utiliser les extraits de code de cet article, vous devez créer une instance **DataLakeServiceClient** qui représente le compte de stockage. Le moyen le plus simple d’en obtenir un consiste à utiliser une clé de compte. 

Cet exemple utilise une clé de compte pour créer une instance **DataLakeServiceClient** qui représente le compte de stockage. 

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Remplacez la valeur d’espace réservé `storage_account_name` par le nom de votre compte de stockage.

- Remplacez la valeur d’espace réservé `storage-account-key` par la clé d’accès de votre compte de stockage.

## <a name="create-a-file-system"></a>Créer un système de fichiers

Un système de fichiers agit comme un conteneur pour vos fichiers. Vous pouvez en créer un en appelant la méthode **FileSystemDataLakeServiceClient.create_file_system**.

Cet exemple crée un système de fichiers nommé `my-file-system`.

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>Créer un répertoire

Créez une référence de répertoire en appelant la méthode **FileSystemClient.create_directory**.

Cet exemple ajoute un répertoire nommé `my-directory` dans un système de fichiers. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>Renommer ou déplacer un répertoire

Renommez ou déplacez un répertoire en appelant la méthode **DataLakeDirectoryClient.rename_directory**. Transmettez un paramètre au chemin d’accès du répertoire souhaité. 

Cet exemple renomme un sous-répertoire avec le nom `my-subdirectory-renamed`.

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>Supprimer un répertoire

Supprimez un répertoire en appelant la méthode **DataLakeDirectoryClient.delete_directory**.

Cet exemple supprime un répertoire nommé `my-directory`.  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>Gérer les autorisations de répertoire

Obtenez la liste de contrôle d’accès (ACL) d’un répertoire en appelant la méthode **DataLakeDirectoryClient.get_access_control** et définissez l’ACL en appelant la méthode **DataLakeDirectoryClient.set_access_control**.

Cet exemple obtient et définit la liste ACL d’un répertoire nommé `my-directory`. La chaîne `rwxr-xrw-` donne à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution, donne au groupe propriétaire uniquement des autorisations de lecture et d’exécution et donne à tous les autres l’accès en lecture et en écriture.

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

## <a name="upload-a-file-to-a-directory"></a>Charger un fichier dans un répertoire 

Tout d’abord, créez une référence de fichier dans le répertoire cible en créant une instance de la classe **DataLakeFileClient**. Téléchargez un fichier en appelant la méthode **DataLakeFileClient.append_data**. Veillez à effectuer le chargement en appelant la méthode **DataLakeFileClient.flush_data**.

Cet exemple charge un fichier texte dans un répertoire nommé `my-directory`.   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>Gérer les autorisations de fichiers

Obtenez la liste de contrôle d’accès (ACL) d’un fichier en appelant la méthode **DataLakeFileClient.get_access_control** et définissez l’ACL en appelant la méthode **DataLakeFileClient.set_access_control**.

Cet exemple obtient et définit la liste ACL d’un fichier nommé `my-file.txt`. La chaîne `rwxr-xrw-` donne à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution, donne au groupe propriétaire uniquement des autorisations de lecture et d’exécution et donne à tous les autres l’accès en lecture et en écriture.

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

## <a name="download-from-a-directory"></a>Télécharger à partir d’un répertoire 

Ouvrez un fichier local en écriture. Ensuite, créez une instance **DataLakeFileClient** qui représente le fichier que vous souhaitez télécharger. Appelez **DataLakeFileClient.read_file** pour lire les octets à partir du fichier, puis écrivez ces octets dans le fichier local. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        downloaded_bytes = file_client.read_file()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>Afficher le contenu du répertoire

Répertoriez le contenu du répertoire en appelant la méthode **FileSystemClient. get_paths**, puis en énumérant les résultats.

Cet exemple imprime le chemin d’accès de chaque sous-répertoire et fichier qui se trouve dans un répertoire nommé `my-directory`.

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="see-also"></a>Voir aussi

* [Documentation de référence de l’API](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [Package (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/)
* [Exemples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Mappage de Gen1 à Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Problèmes connus](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-python/issues)
