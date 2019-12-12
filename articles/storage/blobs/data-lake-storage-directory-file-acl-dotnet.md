---
title: Utiliser Azure .NET pour les fichiers et les listes de contrôle d’accès dans Azure Data Lake Storage Gen2 (préversion)
description: Utilisez la bibliothèque de client Stockage Azure pour gérer les répertoires et les listes de contrôle d’accès (listes ACL) de fichiers et de répertoires dans des comptes de stockage dotés d’un espace de noms hiérarchique (HNS) activé.
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: a756518688b5b1f8b854165de69d3444b772eabc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931224"
---
# <a name="use-net-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Utiliser .NET pour les fichiers et les listes de contrôle d’accès dans Azure Data Lake Storage Gen2 (préversion)

Cet article vous explique comment utiliser .NET pour créer et gérer des répertoires, des fichiers et des autorisations dans des comptes de stockage dotés d’un espace de noms hiérarchique (HNS) activé. 

> [!IMPORTANT]
> Le package NuGet [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6) qui fait l’objet de cet article est actuellement disponible en préversion publique.

[Package (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6) | [Exemples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [Référence d’API](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Storage.Files.DataLake/12.0.0-preview.6/api/index.html) | [Mappage de Gen1 à Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Prérequis

> [!div class="checklist"]
> * Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un compte de stockage doté d’un espace de noms hiérarchique (HNS) activé. Pour créer un test, suivez [ces](data-lake-storage-quickstart-create-account.md) instructions.

## <a name="set-up-your-project"></a>Configuration de votre projet

Pour commencer, installez le package NuGet [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/).

Pour plus d’informations sur l’installation des packages NuGet, consultez [Installer et gérer des packages dans Visual Studio à l’aide du gestionnaire de package NuGet](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio).

Ensuite, ajoutez les instructions using suivantes au début de votre fichier de code.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Se connecter au compte

Pour utiliser les extraits de code de cet article, vous devez créer une instance **DataLakeServiceClient** qui représente le compte de stockage. Le moyen le plus simple d’en obtenir un consiste à utiliser une clé de compte. 

Cet exemple crée une instance **DataLakeServiceClient** à l’aide d’une clé de compte.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

## <a name="create-a-file-system"></a>Créer un système de fichiers

Un système de fichiers agit comme un conteneur pour vos fichiers. Vous pouvez en créer un en appelant la méthode **FileSystemClient.CreateFileSystemAsync**.

Cet exemple crée un système de fichiers nommé `my-file-system`. 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Créer un répertoire

Créez une référence de répertoire en appelant la méthode **FileSystemClient.CreateDirectoryAsync**.

Cet exemple ajoute un répertoire nommé `my-directory` à un système de fichiers, puis ajoute un sous-répertoire nommé `my-subdirectory`. 

```cs
public async Task<DataLakeDirectoryClient> CreateDirectory
    (DataLakeServiceClient serviceClient, string fileSystemName)
{
    DataLakeFileSystemClient fileSystemClient =
        serviceClient.GetFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        await fileSystemClient.CreateDirectoryAsync("my-directory");

    return await directoryClient.CreateSubDirectoryAsync("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Renommer ou déplacer un répertoire

Renommez ou déplacez un répertoire en appelant la méthode **DirectoryClient.RenameAsync**. Transmettez un paramètre au chemin d’accès du répertoire souhaité. 

Cet exemple renomme un sous-répertoire avec le nom `my-subdirectory-renamed`.

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

Cet exemple déplace un répertoire nommé `my-subdirectory-renamed` vers un sous-répertoire d’un répertoire nommé `my-directory-2`. 

```cs
public async Task<DataLakeDirectoryClient> MoveDirectory
    (DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
            fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory-renamed");

    return await directoryClient.RenameAsync("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Supprimer un répertoire

Supprimez un répertoire en appelant la méthode **DirectoryClient.Delete**.

Cet exemple supprime un répertoire nommé `my-directory`.  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>Gérer la liste ACL d’un répertoire

Obtenez la liste de contrôle d’accès (ACL) d’un répertoire en appelant la méthode **directoryClient.GetAccessControlAsync** et définissez la liste ACL en appelant la méthode **DirectoryClient.SetAccessControl**.

Cet exemple obtient et définit la liste ACL d’un répertoire nommé `my-directory`. La chaîne `user::rwx,group::r-x,other::rw-` donne à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution, donne au groupe propriétaire uniquement des autorisations de lecture et d’exécution et donne à tous les autres l’accès en lecture et en écriture.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.Acl);

    directoryClient.SetAccessControl("user::rwx,group::r-x,other::rw-");

}

```

## <a name="upload-a-file-to-a-directory"></a>Charger un fichier dans un répertoire

Tout d’abord, créez une référence de fichier dans le répertoire cible en créant une instance de la classe **DataLakeFileClient**. Chargez un fichier en appelant la méthode **DataLakeFileClient.AppendAsync**. Veillez à effectuer le chargement en appelant la méthode **DataLakeFileClient.FlushAsync**.

Cet exemple charge un fichier texte dans un répertoire nommé `my-directory`.    

```cs
public async Task UploadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = await directoryClient.CreateFileAsync("uploaded-file.txt");

    FileStream fileStream = 
        File.OpenRead("C:\\file-to-upload.txt");

    long fileSize = fileStream.Length;

    await fileClient.AppendAsync(fileStream, offset: 0);

    await fileClient.FlushAsync(position: fileSize);

}
```

## <a name="manage-a-file-acl"></a>Gérer la liste ACL d’un fichier

Obtenez la liste de contrôle d’accès (ACL) d’un fichier en appelant la méthode **DataLakeFileClient.GetAccessControlAsync** et définissez la liste ACL en appelant la méthode **FileClient.SetAccessControl**.

Cet exemple obtient et définit la liste ACL d’un fichier nommé `my-file.txt`. La chaîne `user::rwx,group::r-x,other::rw-` donne à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution, donne au groupe propriétaire uniquement des autorisations de lecture et d’exécution et donne à tous les autres l’accès en lecture et en écriture.

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    Console.WriteLine(FileAccessControl.Acl);

    fileClient.SetAccessControl("user::rwx,group::r-x,other::rw-");
}
```

## <a name="download-from-a-directory"></a>Télécharger à partir d’un répertoire 

Tout d’abord, créez une instance **DataLakeFileClient** qui représente le fichier que vous souhaitez télécharger. Utilisez la méthode **FileClient.ReadAsync** et analysez la valeur retournée pour obtenir un objet [Stream](https://docs.microsoft.com/dotnet/api/system.io.stream). Utilisez n’importe quelle API de traitement de fichiers .NET pour enregistrer les octets du flux de données dans un fichier. 

Cet exemple utilise un objet [BinaryReader](https://docs.microsoft.com/dotnet/api/system.io.binaryreader) et un objet [FileStream](https://docs.microsoft.com/dotnet/api/system.io.filestream) pour enregistrer des octets dans un fichier. 

```cs
public async Task DownloadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("my-image.png");

    Response<FileDownloadInfo> downloadResponse = await fileClient.ReadAsync();

    BinaryReader reader = new BinaryReader(downloadResponse.Value.Content);

    FileStream fileStream = 
        File.OpenWrite("C:\\my-image-downloaded.png");

    int bufferSize = 4096;

    byte[] buffer = new byte[bufferSize];

    int count;

    while ((count = reader.Read(buffer, 0, buffer.Length)) != 0)
    {
        fileStream.Write(buffer, 0, count);
    }

    await fileStream.FlushAsync();

    fileStream.Close();
}
```

## <a name="list-directory-contents"></a>Afficher le contenu du répertoire

Répertoriez le contenu du répertoire en appelant la méthode **FileSystemClient.ListPathsAsync**, puis en énumérant les résultats.

Cet exemple affiche les noms de chaque fichier situé dans un répertoire nommé `my-directory`.

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.ListPathsAsync("my-directory").GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    PathItem item = enumerator.Current;

    while (item != null)
    {
        Console.WriteLine(item.Name);

        if (!await enumerator.MoveNextAsync())
        {
            break;
        }
                
        item = enumerator.Current;
    }

}
```

## <a name="see-also"></a>Voir aussi

* [Documentation de référence de l’API](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Storage.Files.DataLake/12.0.0-preview.6/api/index.html)
* [Package (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6)
* [Exemples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Mappage de Gen1 à Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Problèmes connus](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-net/issues)

