---
title: 'Démarrage rapide : Partage de fichiers de la bibliothèque de Stockage Azure v12 - C++'
description: Ce guide de démarrage rapide explique comment partager des fichiers de la bibliothèque cliente Stockage Azure version 12 pour C++ pour créer un partage de fichiers et un fichier. Ensuite, vous découvrirez comment définir et récupérer des métadonnées, puis télécharger le fichier sur votre ordinateur local.
author: kyle-patterson
ms.author: kylepa
ms.date: 06/22/2021
ms.service: storage
ms.subservice: files
ms.topic: quickstart
ms.openlocfilehash: 4da02f46ef793ae03a11fa4895471488f78d0db1
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112894283"
---
# <a name="develop-for-azure-files-with-c"></a>Développer pour Azure Files avec C++

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="applies-to"></a>S’applique à
| Type de partage de fichiers | SMB | NFS |
|-|:-:|:-:|
| Partages de fichiers Standard (GPv2), LRS/ZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Standard (GPv2), GRS/GZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Premium (FileStorage), LRS/ZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |

## <a name="about-this-tutorial"></a>À propos de ce didacticiel

Ce tutoriel explique comment effectuer des opérations de base sur Azure Files en utilisant C++. Si vous ne connaissez pas Azure Files, l’étude des concepts abordés dans les sections suivantes vous sera utile pour comprendre les exemples. Voici quelques-uns des exemples traités :

* Créer et supprimer des partages de fichiers Azure
* Créer et supprimer des répertoires
* Charger, télécharger et supprimer un fichier
* Définir et répertorier les métadonnées d’un fichier

> [!Note]  
> Comme Azure Files est accessible sur SMB, vous pouvez écrire des applications simples qui accèdent au partage de fichiers Azure à l’aide des fonctions et des classes d’E/S C++ standard. Cet article indique comment écrire des applications qui utilisent le SDK C++ Stockage Azure, lequel a recours à l’[API REST Fichier](/rest/api/storageservices/file-service-rest-api) pour communiquer avec Azure Files.


## <a name="prerequisites"></a>Prérequis

- [Abonnement Azure](https://azure.microsoft.com/free/)
- [Compte Azure Storage](../common/storage-account-create.md)
- [Compilateur C++](https://azure.github.io/azure-sdk/cpp_implementation.html#supported-platforms)
- [CMake](https://cmake.org/)
- [Vcpkg - Gestionnaire de package C et C++](https://github.com/microsoft/vcpkg/blob/master/docs/README.md)

## <a name="setting-up"></a>Configuration

Cette section vous guide tout au long de la préparation d’un projet pour utiliser la bibliothèque de client Stockage Blob Azure v12 pour C++.

### <a name="install-the-packages"></a>Installer les packages

La commande `vcpkg install` installe le kit de développement logiciel (SDK) d’Azure Storage Blob pour C++ et les dépendances nécessaires :

```console
vcpkg.exe install azure-storage-files-shares-cpp:x64-windows
```

Pour plus d’informations, visitez GitHub pour acquérir et créer le [SDK Azure pour C++](https://github.com/Azure/azure-sdk-for-cpp/).

### <a name="create-the-project"></a>Créer le projet

Dans Visual Studio, créez une nouvelle application console C++ pour Windows appelée *FilesShareQuickstartV12*.

:::image type="content" source="./media/quickstart-files-c-plus-plus/visual-studio-create-project.png" alt-text="Boîte de dialogue Visual Studio pour la configuration d’une nouvelle application console Windows C++":::

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="code-examples"></a>Exemples de code

Ces exemples d’extraits de code vous montrent comment effectuer les tâches suivantes avec la bibliothèque de partage de fichiers Stockage Azure pour C++ :

- [Ajouter des fichiers include](#add-include-files)
- [Obtenir la chaîne de connexion](#get-the-connection-string)
- [Créer un partage de fichiers](#create-a-files-share)
- [Charger des fichiers dans un partage de fichiers](#upload-files-to-a-files-share)
- [Définir les métadonnées d’un fichier](#set-the-metadata-of-a-file)
- [Répertorier les métadonnées d’un fichier](#list-the-metadata-of-a-file)
- [Télécharger des fichiers](#download-files)
- [Supprimer un fichier](#delete-a-file)
- [Supprimer un partage de fichiers](#delete-a-files-share)

### <a name="add-include-files"></a>Ajouter des fichiers include

À partir du répertoire de projet :

1. Ouvrez le fichier solution *FilesShareQuickstartV12.sln* dans Visual Studio.
1. Dans Visual Studio, ouvrez le fichier source *FilesShareQuickstartV12.cpp*.
1. Supprimez le code dans `main` qui a été généré automatiquement.
1. Ajoutez des instructions `#include`.

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_Includes":::

### <a name="get-the-connection-string"></a>Obtenir la chaîne de connexion

Le code ci-dessous récupère la chaîne de connexion pour votre compte de stockage dans la variable d’environnement créée dans [Configurer votre chaîne de connexion de stockage](#configure-your-storage-connection-string).

Ajoutez ce code dans `main()` :

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_ConnectionString":::

### <a name="create-a-files-share"></a>Créer un partage de fichiers

Créez une instance de la classe [ShareClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_client.html) en appelant la fonction [CreateFromConnectionString](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_client.html#a7462bcad8a9144f84b0acc70735240e2). Ensuite, appelez [CreateIfNotExists](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_client.html#a6432b4cc677ac03257c7bf234129ec5b) pour créer le partage de fichiers réel dans votre compte de stockage.

Ajoutez ce code à la fin de `main()` :

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_CreateFilesShare":::

### <a name="upload-files-to-a-files-share"></a>Charger des fichiers dans un partage de fichiers

L’extrait de code suivant :

1. Déclare une chaîne contenant « Hello Azure! ».
1. Obtient une référence à un objet [ShareFileClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html) en récupérant le [ShareDirectoryClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_directory_client.html) racine, puis en appelant [GetFileClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_directory_client.html#a93545b8d82ee94f9de183c4d277059d8) sur le partage de fichiers à partir de la section [Créer un partage de fichiers](#create-a-files-share).
1. Charge la chaîne dans le fichier en appelant la fonction [UploadFrom](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#aa80c1f0da6e6784aa0a67cff03d128ba). Cette fonction crée le fichier s’il n’existe pas déjà ou le remplace s’il existe.

Ajoutez ce code à la fin de `main()` :

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_UploadFile":::

### <a name="set-the-metadata-of-a-file"></a>Définir les métadonnées d’un fichier

Définissez les propriétés de métadonnées d’un fichier en appelant la fonction [ShareFileClient.SetMetadata](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#a2f5a40b71040003e41ba8495101f67bb).

Ajoutez ce code à la fin de `main()` :

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_SetFileMetadata":::

### <a name="list-the-metadata-of-a-file"></a>Répertorier les métadonnées d’un fichier

Obtenez les propriétés de métadonnées d’un fichier en appelant la fonction [ShareFileClient.GetProperties](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#a220017815847877cfe8f244b482fb45c). Les métadonnées se trouvent sous le champ `Metadata` du `Value` retourné. Les métadonnées sont une paire clé-valeur, similaire à l’exemple de [définition des métadonnées d’un fichier](#set-the-metadata-of-a-file).

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_GetFileMetadata":::

### <a name="download-files"></a>Télécharger les fichiers

Après avoir récupéré les propriétés du fichier dans, [Répertorier les métadonnées d’un fichier](#list-the-metadata-of-a-file) un nouvel objet `std::vector<uint8_t>` est créé à l’aide des propriétés du fichier téléchargé. Téléchargez le fichier créé précédemment dans le nouvel objet `std::vector<uint8_t>` en appelant la fonction [DownloadTo](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#a256e7b317fbf762c4f8f5023a2cd8cb9) dans la classe de base [ShareFileClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html). Enfin, affichez les données du fichier téléchargé.

Ajoutez ce code à la fin de `main()` :

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_DownloadFile":::

### <a name="delete-a-file"></a>Supprimer un fichier

Le code suivant supprime l’objet blob du partage de fichiers Stockage Azure en appelant la fonction [ShareFileClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#a86036b445dce67a96f7bf6c45f163f59).

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_DeleteFile":::

### <a name="delete-a-files-share"></a>Supprimer un partage de fichiers

Le code suivant nettoie les ressources que l’application a créées en supprimant l’intégralité du partage de fichiers à l’aide de [ShareClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_client.html#a9915630503f1675b5f49eb9c01ca2601).

Ajoutez ce code à la fin de `main()` :

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_DeleteFilesShare":::

## <a name="run-the-code"></a>Exécuter le code

Cette application crée un conteneur et charge un fichier texte dans Stockage Blob Azure. L’exemple liste ensuite le ou les objets blob du conteneur, télécharge le fichier et affiche le contenu du fichier. Enfin, l’application supprime l’objet blob et le conteneur.

La sortie de l’application ressemble à l’exemple suivant :

```output
Azure Files Shares storage v12 - C++ quickstart sample
Creating files share: sample-share
Uploading file: sample-file
Listing file metadata...
key1:value1
key2:value2
Downloaded file contents: Hello Azure!
Deleting file: sample-file
Deleting files share: sample-share
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez découvert comment charger, télécharger et lister des fichiers en utilisant C++. Vous avez également découvert comment créer et supprimer un partage de fichiers Stockage Azure.

Pour voir un exemple d’application Stockage Blob C++, passez à :

> [!div class="nextstepaction"]
> [Exemples du SDK de partage de fichiers Stockage Azure v12 pour C++](https://github.com/Azure/azure-sdk-for-cpp/tree/main/sdk/storage/azure-storage-files-shares/sample)
