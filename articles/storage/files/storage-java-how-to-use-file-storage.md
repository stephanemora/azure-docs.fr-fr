---
title: Développer pour Azure Files avec Java | Microsoft Docs
description: Découvrez comment développer des services et applications Java qui utilisent Azure Files pour stocker les données de fichiers.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/19/2017
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2c80d741ca1cf0b4d66bc18488af8ef1da44fd14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89462596"
---
# <a name="develop-for-azure-files-with-java"></a>Développer pour Azure Files avec Java
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>À propos de ce didacticiel
Ce didacticiel décrit les principes fondamentaux de l’utilisation de Java pour développer des applications ou services qui utilisent Azure Files pour stocker les données de fichiers. Dans ce tutoriel, nous allons créer une application console et montrer comment effectuer des actions de base avec Java et Azure Files :

* Créer et supprimer des partages de fichiers Azure
* Créer et supprimer des répertoires
* Énumérer des fichiers et répertoires dans un partage de fichiers Azure
* Charger, télécharger et supprimer un fichier

> [!Note]  
> Comme Azure Files est accessible sur SMB, vous pouvez écrire des applications qui accèdent au partage de fichiers Azure à l’aide des classes d’E/S Java standard. Cet article indique comment écrire des applications qui utilisent le SDK Java de Stockage Azure, qui utilise l’[API REST Azure Files](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) pour communiquer avec Azure Files.

## <a name="create-a-java-application"></a>Création d’une application Java
Pour générer les exemples, vous avez besoin du SDK Java et du [SDK de Stockage Azure pour Java](https://github.com/Azure/azure-storage-java). Vous devez également avoir préalablement créé un compte de stockage Azure.

## <a name="set-up-your-application-to-use-azure-files"></a>Configurer votre application pour utiliser Azure Files
Pour utiliser les API de stockage Azure, ajoutez l’instruction suivante au début du fichier Java depuis lequel vous voulez accéder au service de stockage.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configuration d’une chaîne de connexion au stockage Azure
Pour utiliser Azure Files, vous devez vous connecter à votre compte de stockage Azure. La première étape consiste à configurer une chaîne de connexion, que nous allons utiliser pour nous connecter à votre compte de stockage. Pour cela, nous allons définir une variable statique.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Remplacez your_storage_account_name (nom de votre compte de stockage) et your_storage_account_key (clé de votre compte de stockage) par les valeurs réelles de votre compte de stockage.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Connexion à un compte de stockage Azure
Pour vous connecter à votre compte de stockage, vous devez utiliser l’objet **CloudStorageAccount**, en transmettant une chaîne de connexion à sa méthode **parse**.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** lève une exception InvalidKeyException. Vous devrez donc le placer dans un bloc try/catch.

## <a name="create-an-azure-file-share"></a>Crée un partage de fichiers Azure
Tous les fichiers et répertoires dans Azure Files se trouvent dans un conteneur appelé **Partage**. Votre compte de stockage peut avoir autant de partages que le permet la capacité de votre compte. Pour pouvoir accéder à un partage et à son contenu, vous devez utiliser un client Azure Files.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

À l’aide de ce dernier, vous pouvez ensuite obtenir une référence à un partage.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Pour créer le partage, utilisez la méthode **createIfNotExists** de l’objet CloudFileShare.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

À ce stade, le **partage** contient une référence à un partage nommé **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Supprimer un partage de fichiers Azure
La suppression d’un partage s’effectue en appelant la méthode **deleteIfExists** sur un objet CloudFileShare. Voici un exemple de code permettant d’effectuer cette opération.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

## <a name="create-a-directory"></a>Créer un répertoire
Vous pouvez également organiser le stockage en plaçant des fichiers dans des sous-répertoires, plutôt que de tous les mettre dans le répertoire racine. Azure Files vous permet de créer autant de répertoires que le permet votre compte. Le code ci-dessous crée un sous-répertoire nommé **sampledir** sous le répertoire racine.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

## <a name="delete-a-directory"></a>Supprimer un répertoire
La suppression d’un répertoire est une tâche simple. Notez, toutefois, que vous ne pouvez pas supprimer un répertoire s’il contient des fichiers ou d’autres répertoires.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Énumérer des fichiers et répertoires dans un partage de fichiers Azure
Il est facile d’obtenir la liste de fichiers et de répertoires d’un partage en appelant **listFilesAndDirectories** sur une référence CloudFileDirectory. La méthode renvoie une liste d’objets ListFileItem sur laquelle vous pouvez effectuer une itération. Par exemple, le code suivant répertorie les fichiers et répertoires du répertoire racine.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Charger un fichier
Cette section décrit comment télécharger un fichier du stockage local vers le répertoire racine d’un partage.

La première étape du téléchargement d’un fichier consiste à obtenir une référence au répertoire dans lequel ce fichier doit résider. Pour cela, vous devez appeler la méthode **getRootDirectoryReference** de l’objet de partage.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Maintenant que vous avez une référence au répertoire racine du partage, vous pouvez télécharger un fichier vers ce répertoire à l’aide du code ci-après.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Téléchargement d’un fichier
Le téléchargement de fichiers est l’une des opérations les plus fréquentes que vous effectuerez sur Azure Files. Dans l’exemple suivant, le code télécharge SampleFile.txt et affiche son contenu.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

## <a name="delete-a-file"></a>Supprimer un fichier
La suppression de fichiers est également une opération courante dans Azure Files. Le code suivant supprime un fichier nommé SampleFile.txt et stocké dans un répertoire nommé **sampledir**.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les autres API de stockage Azure, suivez ces liens.

* [Azure pour les développeurs Java](/java/azure)/)
* [Kit de développement logiciel (SDK) Azure Storage pour Java](https://github.com/azure/azure-storage-java)
* [Kit de développement logiciel (SDK) Azure Storage pour Android](https://github.com/azure/azure-storage-android)
* [Référence du Kit de développement logiciel (SDK) du client Azure Storage](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [API REST des services d’Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog de l'équipe Azure Storage](https://docs.microsoft.com/archive/blogs/windowsazurestorage/)
* [Transfert de données avec l’utilitaire de ligne de commande AzCopy](../common/storage-use-azcopy.md)
* [Résolution des problèmes liés à Azure Files - Windows](storage-troubleshoot-windows-file-connection-problems.md)
