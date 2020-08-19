---
title: Développement pour Azure Files avec .NET | Microsoft Docs
description: Découvrez comment développer des services et applications .NET qui utilisent Azure Files pour stocker les données de fichiers.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 10/7/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bbee9881addf66d762f1eadb837e5a4e41bf79de
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167893"
---
# <a name="develop-for-azure-files-with-net"></a>Développer pour Azure Files avec .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Ce didacticiel décrit les principes fondamentaux de l’utilisation de .NET pour développer des applications qui utilisent [Azure Files](storage-files-introduction.md) pour stocker les données de fichiers. Ce tutoriel crée une application console simple pour effectuer des actions de base avec .NET et Azure Files :

* Obtenir le contenu d’un fichier
* Définir la taille maximale ou *quota* pour le partage de fichiers
* Créez une signature d’accès partagé (clé SAS) pour un fichier qui utilise une stratégie d’accès stockée définie sur le partage.
* Copier un fichier dans un autre fichier au sein du même compte de stockage
* Copier un fichier dans un objet blob au sein du même compte de stockage.
* Utiliser Azure Storage Metrics pour la résolution des problèmes

Pour en savoir plus sur Azure Files, consultez [Qu’est-ce qu’Azure Files ?](storage-files-introduction.md).

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Compréhension des API .NET

Azure Files fournit deux grandes approches pour les applications clientes : Server Message Block (SMB) et REST. Dans .NET, l’abstraction de ces approches est fournie par les API `System.IO` et `WindowsAzure.Storage`.

API | Quand l’utiliser | Notes
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | Votre application : <ul><li>Doit lire/écrire des fichiers à l’aide de SMB.</li><li>Est en cours d’exécution sur un périphérique qui a accès via le port 445 à votre compte Azure Files</li><li>N’a pas besoin de gérer les paramètres d’administration du partage de fichiers</li></ul> | Les E/S de fichiers implémentées avec Azure Files sur SMB sont généralement similaires aux E/S avec un partage de fichiers réseau ou un périphérique de stockage local. Pour découvrir une présentation de certaines fonctionnalités de .NET, notamment des E/S de fichiers, consultez le tutoriel [Application console](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter).
[Microsoft.Azure.Storage.File](/dotnet/api/overview/azure/storage?view=azure-dotnet#version-11x) | Votre application : <ul><li>Ne peut pas accéder à Azure Files à l’aide de SMB sur le port 445 en raison des contraintes de pare-feu ou d’ISP.</li><li>Nécessite une fonctionnalité d’administration, telle que la capacité de définir le quota d’un partage de fichier ou de créer une signature d’accès partagé</li></ul> | Cet article décrit l’utilisation de `Microsoft.Azure.Storage.File` pour les E/S de fichiers utilisant REST plutôt que SMB et la gestion du partage de fichiers.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Création de l’application console et obtention de l’assembly

Dans Visual Studio, créez une application de console Windows. Les étapes suivantes vous montrent comment créer une application console dans Visual Studio 2019. Les étapes sont semblables pour d’autres versions de Visual Studio.

1. Démarrez Visual Studio et sélectionnez **Créer un projet**.
1. Dans **Créer un projet**, choisissez **Application console (.NET Framework)** pour C#, puis sélectionnez **Suivant**.
1. Dans **Configurer votre nouveau projet**, entrez un nom pour l’application, puis sélectionnez **Créer**.

Vous pouvez ajouter tous les exemples de code figurant dans ce tutoriel à la méthode `Main()` du fichier `Program.cs` de votre application console.

Vous pouvez utiliser la bibliothèque de client Stockage Azure dans n’importe quel type d’application .NET, notamment un service cloud Azure ou une application web, ainsi que des applications de bureau et mobiles. Dans ce guide, nous utilisons une application console pour plus de simplicité.

## <a name="use-nuget-to-install-the-required-packages"></a>Utiliser NuGet pour installer les packages requis

Reportez-vous à ces packages dans votre projet pour suivre ce tutoriel :

* [Bibliothèque Microsoft Azure Storage Common pour .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
  
  ce package fournit un accès programmatique aux ressources communes de votre compte de stockage.
* [Bibliothèque Microsoft Azure Storage Blob pour .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)

  Ce package fournit un accès par programmation aux ressources d’objets blob de votre compte de stockage.
* [Bibliothèque Microsoft Azure Storage File pour .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)

  Ce package fournit un accès par programmation aux ressources de fichiers de votre compte de stockage.
* [Bibliothèque Microsoft Azure Configuration Manager pour .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)

  Ce package fournit une classe pour l’analyse d’une chaîne de connexion dans un fichier de configuration, quel que soit l’emplacement d’exécution de votre application.

Vous pouvez utiliser NuGet pour obtenir ces deux packages. Procédez comme suit :

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis choisissez **Gérer les packages NuGet**.
1. Dans le **Gestionnaire de package NuGet**, sélectionnez **Parcourir**. Ensuite, recherchez et choisissez **Microsoft.Azure.Storage.Blob**, puis sélectionnez **Installer**.

   Cette étape installe le package et ses dépendances.
1. Recherchez et installez ces packages :

   * **Microsoft.Azure.Storage.Common**
   * **Microsoft.Azure.Storage.File**
   * **Microsoft.Azure.ConfigurationManager**

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Enregistrer les informations d’identification de votre compte de stockage dans le fichier App.config

Enregistrez ensuite vos informations d’identification dans le fichier `App.config` de votre projet. Dans l’**Explorateur de solutions**, double-cliquez sur `App.config`, puis modifiez le fichier afin qu’il soit similaire à l’exemple suivant. Remplacez `myaccount` par le nom de votre compte de stockage et `mykey` par la clé de votre compte de stockage.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> La dernière version de l’émulateur de stockage Azure ne prend pas en charge Azure Files. Votre chaîne de connexion doit cibler un compte de stockage Azure dans le cloud pour fonctionner avec Azure Files.

## <a name="add-using-directives"></a>Ajouter des directives d’utilisation

Dans l’**Explorateur de solutions**, ouvrez le fichier `Program.cs` et ajoutez les directives using suivantes en haut du fichier.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>Accès au partage de fichiers par programmation

Ajoutez ensuite le contenu suivant à la méthode `Main()`, après le code indiqué ci-dessus, pour récupérer la chaîne de connexion. Ce code obtient une référence au fichier créé plus tôt et retourne son contenu.

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

Exécutez l’application console pour voir le résultat.

## <a name="set-the-maximum-size-for-a-file-share"></a>Définition de la taille maximale d’un partage de fichiers

À compter de la version 5.x de la bibliothèque de client Stockage Azure, vous pouvez définir le quota (ou la taille maximale) pour un partage de fichiers. Vous pouvez également vérifier la quantité de données actuellement stockée sur le partage.

Le fait de définir le quota pour un partage limite la taille totale des fichiers stockés sur ce partage. Si la taille totale des fichiers sur le partage dépasse le quota défini sur le partage, les clients ne peuvent pas augmenter la taille des fichiers existants. Les clients ne peuvent pas créer de nouveaux fichiers, sauf si ces fichiers sont vides.

L’exemple ci-dessous illustre comment vérifier l’utilisation actuelle pour un partage et comment définir le quota pour le partage.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Génération d’une signature d’accès partagé pour un fichier ou partage de fichiers

Depuis la version 5.x de la bibliothèque cliente Azure Storage, vous pouvez générer une signature d’accès partagé (SAP) pour un partage de fichiers ou un fichier individuel. Vous pouvez également créer une stratégie d’accès stockée sur un partage de fichiers pour gérer les signatures d’accès partagé. Nous vous recommandons de créer une stratégie d’accès stockée, car elle vous permet de révoquer la SAS si elle est compromise.

L’exemple suivant crée une stratégie d’accès stockée sur un partage. Il utilise cette stratégie pour fournir les contraintes pour une SAP sur un fichier du partage.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new stored access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the stored access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

Pour plus d’informations sur la création et l’utilisation de signatures d’accès partagé, consultez [Fonctionnement d’une signature d’accès partagé](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works).

## <a name="copy-files"></a>Copie des fichiers

Depuis la version 5.x de la bibliothèque cliente Azure Storage, vous pouvez copier un fichier dans un autre fichier, un fichier dans un objet blob ou un objet blob dans un fichier. Dans les sections suivantes, nous montrons comment effectuer ces opérations de copie par programmation.

Vous pouvez également utiliser AzCopy pour copier un fichier dans un autre ou pour copier un objet blob dans un fichier, ou inversement. Consultez [Bien démarrer avec AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Si vous copiez un objet blob dans un fichier ou un fichier dans un objet blob, vous devez utiliser une signature d’accès partagé (SAP) pour autoriser l’accès à l’objet source, même si vous effectuez la copie dans le même compte de stockage.
>

### <a name="copy-a-file-to-another-file"></a>Copier un fichier dans un autre

L’exemple suivant copie un fichier dans un autre fichier au sein du même partage. Étant donné que cette opération de copie a lieu entre des fichiers du même compte de stockage, vous pouvez utiliser l’authentification Clé partagée pour l’effectuer.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

### <a name="copy-a-file-to-a-blob"></a>Copier un fichier dans un objet blob

L’exemple ci-dessous crée un fichier et le copie dans un objet blob au sein du même compte de stockage. L’exemple crée une SAP pour le fichier source, que le service utilise pour autoriser l’accès au fichier source pendant l’opération de copie.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

Vous pouvez copier un objet blob dans un fichier de la même façon. Si l’objet source est un objet blob, créez une SAP pour authentifier l’accès à cet objet blob pendant l’opération de copie.

## <a name="share-snapshots"></a>Instantanés de partage

À compter de la version 8.5 de la bibliothèque cliente du Stockage Azure, vous pouvez créer un instantané de partage. Vous pouvez également répertorier ou parcourir des instantanés de partage et les supprimer. Les instantanés de partage sont en lecture seule. Aucune opération d’écriture n’est autorisée sur ceux-ci.

### <a name="create-share-snapshots"></a>Créer des instantanés de partage

L’exemple suivant permet de créer un instantané de partage de fichier.

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

### <a name="list-share-snapshots"></a>Répertorier les instantanés de partage

L’exemple suivant répertorie les instantanés de partage figurant sur un partage.

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

### <a name="browse-files-and-directories-within-share-snapshots"></a>Parcourir les fichiers et répertoires dans des instantanés de partage

L’exemple suivant permet de parcourir les fichiers et les répertoires au sein des instantanés de partage.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

### <a name="list-shares-and-share-snapshots-and-restore-file-shares-or-files-from-share-snapshots"></a>Lister des partages et des instantanés de partage, et restaurer des partages de fichiers ou des fichiers à partir d’instantanés de partage

Prendre un instantané d’un partage de fichiers vous permet de récupérer des fichiers ou l’ensemble du partage de fichiers ultérieurement.

Vous pouvez restaurer un fichier à partir d’un instantané de partage de fichiers en interrogeant les instantanés de partage d’un partage de fichiers. Vous pouvez ensuite récupérer un fichier qui appartient à un instantané de partage particulier. Utilisez cette version pour lire et comparer directement ou pour effectuer une restauration.

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();
var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();
var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
sasConstraints.Permissions = SharedAccessFilePermissions.Read;

//Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));
```

### <a name="delete-share-snapshots"></a>Supprimer des instantanés de partage

L’exemple suivant permet de supprimer un instantané de partage de fichier.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## <a name="troubleshoot-azure-files-by-using-metrics"></a>Résoudre les problèmes liés à Azure Files à l’aide de métriques<a name="troubleshooting-azure-files-using-metrics"></a>

Azure Storage Analytics prend à présent en charge les métriques pour Azure Files. Avec les données de métriques, vous pouvez suivre les demandes et diagnostiquer les problèmes.

Vous pouvez activer les métriques pour Azure Files par le biais du [Portail Azure](https://portal.azure.com). Vous pouvez également activer les métriques par programmation en appelant l’opération de définition des propriétés du service de fichiers avec l’API REST ou l’un de ses analogues dans la bibliothèque de client de stockage.

L’exemple de code suivant explique comment utiliser la bibliothèque cliente de stockage pour .NET afin d’activer les métriques pour Azure Files.

Commencez par ajouter les directives `using` suivantes à votre fichier `Program.cs`, en plus de celles que vous avez ajoutées ci-dessus :

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Bien qu’Azure Blobs, Azure Tables et Azure Queues utilisent le type `ServiceProperties` partagé dans l’espace de noms `Microsoft.Azure.Storage.Shared.Protocol`, Azure Files utilise son propre type (`FileServiceProperties`) dans l’espace de noms `Microsoft.Azure.Storage.File.Protocol`. Pour pouvoir compiler le code suivant, vous devez cependant référencer les deux espaces de noms à partir de votre code.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

Si vous rencontrez des problèmes, consultez [Résoudre les problèmes liés à Azure Files sous Windows](storage-troubleshoot-windows-file-connection-problems.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure Files, consultez les ressources suivantes :

### <a name="conceptual-articles-and-videos"></a>Vidéos et articles conceptuels

* [Azure Files : un système de fichiers SMB cloud transparent pour Windows et Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Utiliser Azure Files avec Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Outils pris en charge pour le stockage de fichiers

* [Bien démarrer avec AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Résoudre les problèmes liés à Azure Files sous Windows](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Informations de référence

* [API de stockage Azure pour .NET](/dotnet/api/overview/azure/storage)
* [API REST du service de fichiers](/rest/api/storageservices/File-Service-REST-API)

### <a name="blog-posts"></a>Billets de blog :

* [Stockage Fichier Azure est désormais mis à la disposition générale](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Stockage de fichiers dans Azure](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Présentation de Microsoft Azure Files Service](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Conservation des connexions vers les fichiers Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
