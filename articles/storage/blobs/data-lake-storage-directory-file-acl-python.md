---
title: Utiliser Python pour gérer les données dans Azure Data Lake Storage Gen2
description: Utiliser Python pour gérer les répertoires et les fichiers dans les comptes de stockage dotés d’un espace de noms hiérarchique activé.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: a143c0aa19241b532cabff95fe6bf85679e4007c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652290"
---
# <a name="use-python-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Utiliser Python pour gérer les répertoires et les fichiers dans Azure Data Lake Storage Gen2

Cet article explique comment utiliser Python pour créer et gérer des répertoires et des fichiers dans les comptes de stockage dotés d’un espace de noms hiérarchique.

Pour en savoir plus sur la façon d’obtenir, de définir et de mettre à jour les listes de contrôle d’accès (ACL, Access Control List) des répertoires et des fichiers, consultez [Utiliser Python pour gérer les listes de contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-acl-python.md).

[Package (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/) | [Exemples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [Référence de l’API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake) | [Mappage de Gen1 à Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un compte de stockage doté d’un espace de noms hiérarchique activé. Pour créer un test, suivez [ces](create-data-lake-storage-account.md) instructions.

## <a name="set-up-your-project"></a>Configuration de votre projet

Installez la bibliothèque cliente Azure Data Lake Storage pour Python à l’aide de [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Ajoutez les instructions d’importation suivantes au début de votre fichier de code.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Se connecter au compte

Pour utiliser les extraits de code de cet article, vous devez créer une instance **DataLakeServiceClient** qui représente le compte de stockage. 

### <a name="connect-by-using-an-account-key"></a>Connexion avec une clé de compte

Il s’agit du moyen le plus simple de se connecter à un compte. 

Cet exemple crée une instance de **DataLakeServiceClient** à l’aide d’une clé de compte.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::

- Remplacez la valeur d’espace réservé `storage_account_name` par le nom de votre compte de stockage.

- Remplacez la valeur d’espace réservé `storage_account_key` par la clé d’accès de votre compte de stockage.

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Se connecter avec Azure Active Directory (Azure AD)

Vous pouvez utiliser la [bibliothèque de client Azure Identity pour Python](https://pypi.org/project/azure-identity/) pour authentifier votre application auprès d’Azure AD.

Cet exemple crée une instance de **DataLakeServiceClient** à l’aide d’un ID client, d’une clé secrète client et d’un ID locataire.  Pour obtenir ces valeurs, consultez [Obtenir un jeton à partir d’Azure AD pour autoriser les requêtes à partir d’une application cliente](../common/storage-auth-aad-app.md).

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Pour obtenir plus d’exemples, consultez la documentation [Bibliothèque de client Azure Identity pour Python](https://pypi.org/project/azure-identity/).

## <a name="create-a-container"></a>Créez un conteneur.

Un conteneur fait office de système de fichiers pour vos fichiers. Vous pouvez en créer un en appelant la méthode **FileSystemDataLakeServiceClient.create_file_system**.

Cet exemple crée un conteneur nommé `my-file-system`.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Créer un répertoire

Créez une référence de répertoire en appelant la méthode **FileSystemClient.create_directory**.

Cet exemple ajoute un répertoire nommé `my-directory` à un conteneur. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Renommer ou déplacer un répertoire

Renommez ou déplacez un répertoire en appelant la méthode **DataLakeDirectoryClient.rename_directory**. Transmettez un paramètre au chemin d’accès du répertoire souhaité. 

Cet exemple renomme un sous-répertoire avec le nom `my-subdirectory-renamed`.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_RenameDirectory":::

## <a name="delete-a-directory"></a>Supprimer un répertoire

Supprimez un répertoire en appelant la méthode **DataLakeDirectoryClient.delete_directory**.

Cet exemple supprime un répertoire nommé `my-directory`.  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Charger un fichier dans un répertoire

Tout d’abord, créez une référence de fichier dans le répertoire cible en créant une instance de la classe **DataLakeFileClient**. Téléchargez un fichier en appelant la méthode **DataLakeFileClient.append_data**. Veillez à effectuer le chargement en appelant la méthode **DataLakeFileClient.flush_data**.

Cet exemple charge un fichier texte dans un répertoire nommé `my-directory`.   

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFile":::

> [!TIP]
> Si la taille de votre fichier est importante, votre code devra effectuer plusieurs appels à **DataLakeFileClient.append_data**. Utilisez plutôt la méthode **DataLakeFileClient.upload_data**. Vous pourrez ainsi charger la totalité du fichier en un seul appel. 

## <a name="upload-a-large-file-to-a-directory"></a>Charger un fichier volumineux dans un répertoire

Utilisez la méthode **DataLakeFileClient.upload_data** pour charger des fichiers volumineux sans avoir à effectuer plusieurs appels à la méthode **DataLakeFileClient.append_data**.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Télécharger à partir d’un répertoire 

Ouvrez un fichier local en écriture. Ensuite, créez une instance **DataLakeFileClient** qui représente le fichier que vous souhaitez télécharger. Appelez **DataLakeFileClient.read_file** pour lire les octets à partir du fichier, puis écrivez ces octets dans le fichier local. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DownloadFromDirectory":::

## <a name="list-directory-contents"></a>Afficher le contenu du répertoire

Répertoriez le contenu du répertoire en appelant la méthode **FileSystemClient. get_paths**, puis en énumérant les résultats.

Cet exemple imprime le chemin d’accès de chaque sous-répertoire et fichier qui se trouve dans un répertoire nommé `my-directory`.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Voir aussi

- [Documentation de référence de l’API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Package (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/)
- [Exemples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Mappage de Gen1 à Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Problèmes connus](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-python/issues)