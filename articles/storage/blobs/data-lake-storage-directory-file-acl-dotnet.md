---
title: Kit SDK .NET Azure Data Lake Storage Gen2 pour les fichiers et les listes de contrôle d’accès
description: Utilisez la bibliothèque de client Stockage Azure pour gérer les répertoires et les listes de contrôle d’accès (listes ACL) de fichiers et de répertoires dans des comptes de stockage dotés d’un espace de noms hiérarchique (HNS) activé.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: b83d0d2d765b60585832f1a3e7c610f05eac075c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061585"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Gestion des répertoires, des fichiers et des listes de contrôle d’accès dans Azure Data Lake Storage Gen2 avec .NET

Cet article vous explique comment utiliser .NET pour créer et gérer des répertoires, des fichiers et des autorisations dans des comptes de stockage dotés d’un espace de noms hiérarchique (HNS) activé. 

[Package (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [Exemples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [Référence d’API](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake) | [Mappage de Gen1 à Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-net/issues)

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
using Azure.Storage;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Se connecter au compte

Pour utiliser les extraits de code de cet article, vous devez créer une instance [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) qui représente le compte de stockage. 

### <a name="connect-by-using-an-account-key"></a>Connexion avec une clé de compte

Il s’agit du moyen le plus simple de se connecter à un compte. 

Cet exemple crée une instance [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) à l’aide d’une clé de compte.

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

### <a name="connect-by-using-azure-active-directory-ad"></a>Connexion avec Azure Active Directory (AD)

Vous pouvez utiliser la [bibliothèque de client Azure Identity pour .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) pour authentifier votre application auprès d’Azure AD.

Cet exemple crée une instance [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) à l’aide d’un ID client, d’une clé secrète client et d’un ID locataire.  Pour récupérer ces valeurs, consultez [Acquérir un jeton auprès d’Azure AD pour autoriser les demandes provenant d’une application cliente](../common/storage-auth-aad-app.md).

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

```

> [!NOTE]
> Pour plus d’exemples, consultez la documentation [Bibliothèque de client Azure Identity pour .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

## <a name="create-a-file-system"></a>Créer un système de fichiers

Un système de fichiers agit comme un conteneur pour vos fichiers. Vous pouvez en créer un en appelant la méthode [DataLakeServiceClient.CreateFileSystem](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync).

Cet exemple crée un système de fichiers nommé `my-file-system`. 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Créer un répertoire

Créez une référence de répertoire en appelant la méthode [DataLakeFileSystemClient.CreateDirectoryAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync).

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

Renommez ou déplacez un répertoire en appelant la méthode [DataLakeDirectoryClient.RenameAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync). Transmettez un paramètre au chemin d’accès du répertoire souhaité. 

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

Supprimez un répertoire en appelant la méthode [DataLakeDirectoryClient.Delete](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete).

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

Obtenez la liste de contrôle d’accès (ACL) d’un répertoire en appelant la méthode [DataLakeDirectoryClient.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) et définissez la liste ACL en appelant la méthode [DataLakeDirectoryClient.SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist).

> [!NOTE]
> Si votre application autorise l’accès à l’aide d’Azure Active Directory (Azure AD), assurez-vous que le [rôle de propriétaire de données d’objet blob de stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) a été attribué au principal de sécurité utilisé par votre application pour autoriser l’accès. Pour en savoir plus sur l’application des autorisations ACL et les conséquences de leur modification, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Cet exemple obtient et définit la liste ACL d’un répertoire nommé `my-directory`. La chaîne `user::rwx,group::r-x,other::rw-` donne à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution, donne au groupe propriétaire uniquement des autorisations de lecture et d’exécution et donne à tous les autres l’accès en lecture et en écriture.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    directoryClient.SetAccessControlList(accessControlList);

}

```

## <a name="upload-a-file-to-a-directory"></a>Charger un fichier dans un répertoire

Tout d’abord, créez une référence de fichier dans le répertoire cible en créant une instance de la classe [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient). Chargez un fichier en appelant la méthode [DataLakeFileClient.AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Veillez à effectuer le chargement en appelant la méthode [DataLakeFileClient.FlushAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync).

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

> [!TIP]
> Si la taille de votre fichier est importante, votre code devra effectuer plusieurs appels à [DataLakeFileClient.AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Utilisez plutôt la méthode [DataLakeFileClient.UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_). Vous pourrez ainsi charger la totalité du fichier en un seul appel. 
>
> Vous en trouverez un exemple dans la section suivante.

## <a name="upload-a-large-file-to-a-directory"></a>Chargement d’un fichier volumineux dans un répertoire

Utilisez la méthode [DataLakeFileClient.UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) pour charger des fichiers volumineux sans avoir à effectuer plusieurs appels à la méthode [DataLakeFileClient.AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync).

```cs
public async Task UploadFileBulk(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.GetFileClient("uploaded-file.txt");

    FileStream fileStream =
        File.OpenRead("C:\\file-to-upload.txt");

    await fileClient.UploadAsync(fileStream);

}

```

## <a name="manage-a-file-acl"></a>Gérer la liste ACL d’un fichier

Obtenez la liste de contrôle d’accès (ACL) d’un fichier en appelant la méthode [DataLakeFileClient.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) et définissez la liste ACL en appelant la méthode [DataLakeFileClient.SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist).

> [!NOTE]
> Si votre application autorise l’accès à l’aide d’Azure Active Directory (Azure AD), assurez-vous que le [rôle de propriétaire de données d’objet blob de stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) a été attribué au principal de sécurité utilisé par votre application pour autoriser l’accès. Pour en savoir plus sur l’application des autorisations ACL et les conséquences de leur modification, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

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

    Console.WriteLine(FileAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    fileClient.SetAccessControlList(accessControlList);
}
```

## <a name="download-from-a-directory"></a>Télécharger à partir d’un répertoire 

Tout d’abord, créez une instance [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) qui représente le fichier que vous souhaitez télécharger. Utilisez la méthode [DataLakeFileClient.ReadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) et analysez la valeur retournée pour obtenir un objet [Stream](https://docs.microsoft.com/dotnet/api/system.io.stream). Utilisez n’importe quelle API de traitement de fichiers .NET pour enregistrer les octets du flux de données dans un fichier. 

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

Répertoriez le contenu du répertoire en appelant la méthode [FileSystemClient.GetPathsAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync), puis en énumérant les résultats.

Cet exemple affiche les noms de chaque fichier situé dans un répertoire nommé `my-directory`.

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.GetPathsAsync("my-directory").GetAsyncEnumerator();

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

* [Documentation de référence de l’API](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)
* [Package (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [Exemples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Mappage de Gen1 à Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Problèmes connus](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-net/issues)

