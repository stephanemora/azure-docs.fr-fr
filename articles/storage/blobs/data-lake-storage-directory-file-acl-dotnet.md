---
title: Utiliser .NET pour gérer les données dans Azure Data Lake Storage Gen2
description: Utilisez la bibliothèque de client Stockage Azure pour .NET qui permet de gérer les répertoires et les fichiers dans les comptes de stockage dotés d’un espace de noms hiérarchique activé.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 52e993a22a512a94c8b5b8b050205db0c4ce0b1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100650318"
---
# <a name="use-net-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Utiliser .NET pour gérer les répertoires et les fichiers dans Azure Data Lake Storage Gen2

Cet article explique comment utiliser .NET pour créer et gérer les répertoires et les fichiers dans les comptes de stockage dotés d’un espace de noms hiérarchique.

Pour en savoir plus sur la façon d’obtenir, de définir et de mettre à jour les listes de contrôle d’accès (ACL, Access Control List) des répertoires et des fichiers, consultez [Utiliser .NET pour gérer les listes de contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-acl-dotnet.md).

[Package (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [Exemples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [Référence d’API](/dotnet/api/azure.storage.files.datalake) | [Mappage de Gen1 à Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un compte de stockage doté d’un espace de noms hiérarchique activé. Pour créer un test, suivez [ces](create-data-lake-storage-account.md) instructions.

## <a name="set-up-your-project"></a>Configuration de votre projet

Pour commencer, installez le package NuGet [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/).

Pour plus d’informations sur l’installation des packages NuGet, consultez [Installer et gérer des packages dans Visual Studio à l’aide du gestionnaire de package NuGet](/nuget/consume-packages/install-use-packages-visual-studio).

Ensuite, ajoutez les instructions using suivantes au début de votre fichier de code.

```csharp
using Azure;
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;

```

## <a name="connect-to-the-account"></a>Se connecter au compte

Pour utiliser les extraits de code de cet article, vous devez créer une instance [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) qui représente le compte de stockage. 

### <a name="connect-by-using-an-account-key"></a>Connexion avec une clé de compte

Il s’agit du moyen le plus simple de se connecter à un compte. 

Cet exemple crée une instance de [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) à l’aide d’une clé de compte.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Se connecter avec Azure Active Directory (Azure AD)

Vous pouvez utiliser la [bibliothèque de client Azure Identity pour .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) pour authentifier votre application auprès d’Azure AD.

Cet exemple crée une instance [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) à l’aide d’un ID client, d’une clé secrète client et d’un ID locataire.  Pour récupérer ces valeurs, consultez [Acquérir un jeton auprès d’Azure AD pour autoriser les demandes provenant d’une application cliente](../common/storage-auth-aad-app.md).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Pour plus d’exemples, consultez la documentation [Bibliothèque de client Azure Identity pour .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

## <a name="create-a-container"></a>Créez un conteneur.

Un conteneur fait office de système de fichiers pour vos fichiers. Vous pouvez en créer un en appelant la méthode [DataLakeServiceClient.CreateFileSystem](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync).

Cet exemple crée un conteneur nommé `my-file-system`. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Créer un répertoire

Créez une référence de répertoire en appelant la méthode [DataLakeFileSystemClient.CreateDirectoryAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync).

Cet exemple ajoute un répertoire nommé `my-directory` à un conteneur, puis ajoute un sous-répertoire nommé `my-subdirectory`. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Renommer ou déplacer un répertoire

Renommez ou déplacez un répertoire en appelant la méthode [DataLakeDirectoryClient.RenameAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync). Transmettez un paramètre au chemin d’accès du répertoire souhaité. 

Cet exemple renomme un sous-répertoire avec le nom `my-subdirectory-renamed`.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_RenameDirectory":::

Cet exemple déplace un répertoire nommé `my-subdirectory-renamed` vers un sous-répertoire d’un répertoire nommé `my-directory-2`. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>Supprimer un répertoire

Supprimez un répertoire en appelant la méthode [DataLakeDirectoryClient.Delete](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete).

Cet exemple supprime un répertoire nommé `my-directory`.  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Charger un fichier dans un répertoire

Tout d’abord, créez une référence de fichier dans le répertoire cible en créant une instance de la classe [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient). Chargez un fichier en appelant la méthode [DataLakeFileClient.AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Veillez à effectuer le chargement en appelant la méthode [DataLakeFileClient.FlushAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync).

Cet exemple charge un fichier texte dans un répertoire nommé `my-directory`. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFile":::

> [!TIP]
> Si la taille de votre fichier est importante, votre code devra effectuer plusieurs appels à [DataLakeFileClient.AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Utilisez plutôt la méthode [DataLakeFileClient.UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_). Vous pourrez ainsi charger la totalité du fichier en un seul appel.
>
> Vous en trouverez un exemple dans la section suivante.

## <a name="upload-a-large-file-to-a-directory"></a>Chargement d’un fichier volumineux dans un répertoire

Utilisez la méthode [DataLakeFileClient.UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) pour charger des fichiers volumineux sans avoir à effectuer plusieurs appels à la méthode [DataLakeFileClient.AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Télécharger à partir d’un répertoire 

Tout d’abord, créez une instance [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) qui représente le fichier que vous souhaitez télécharger. Utilisez la méthode [DataLakeFileClient.ReadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) et analysez la valeur retournée pour obtenir un objet [Stream](/dotnet/api/system.io.stream). Utilisez n’importe quelle API de traitement de fichiers .NET pour enregistrer les octets du flux de données dans un fichier. 

Cet exemple utilise un objet [BinaryReader](/dotnet/api/system.io.binaryreader) et un objet [FileStream](/dotnet/api/system.io.filestream) pour enregistrer des octets dans un fichier. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DownloadBinaryFromDirectory":::

## <a name="list-directory-contents"></a>Afficher le contenu du répertoire

Répertoriez le contenu du répertoire en appelant la méthode [FileSystemClient.GetPathsAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync), puis en énumérant les résultats.

Cet exemple affiche les noms de chaque fichier situé dans un répertoire nommé `my-directory`.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Voir aussi

- [Documentation de référence de l’API](/dotnet/api/azure.storage.files.datalake)
- [Package (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [Exemples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Mappage de Gen1 à Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [Problèmes connus](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-net/issues)