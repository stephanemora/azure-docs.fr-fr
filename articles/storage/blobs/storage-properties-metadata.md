---
title: Définir et récupérer des propriétés et métadonnées d’objet dans Stockage Azure | Documents Microsoft
description: Stockez des métadonnées personnalisées sur des objets dans Azure Storage, et définissez et récupérez les propriétés système.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/16/2018
ms.author: tamram
ms.openlocfilehash: 2641e1653e14a7c101d95b02b8a5af71ceb9fdc6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398172"
---
# <a name="set-and-retrieve-properties-and-metadata"></a>Définir et récupérer des propriétés et métadonnées d’objet

Les objets dans Stockage Azure prennent en charge des propriétés système et des métadonnées définies par l’utilisateur, en plus des données qu’ils contiennent. Cet article décrit la gestion des propriétés système et des métadonnées définies par l’utilisateur avec la [Bibliothèque cliente Stockage Azure pour .NET](https://www.nuget.org/packages/WindowsAzure.Storage/).

* **Propriétés système** : des propriétés système s’appliquent à chaque ressource de stockage. Certaines d'entre elles peuvent être lues ou configurées, alors que d'autres sont en lecture seule. En arrière-plan, certaines propriétés système correspondent à certains en-têtes HTTP standard. Les bibliothèques clientes de Stockage Azure conservent ces propriétés pour vous.

* **Métadonnées définies par l’utilisateur** : ces métadonnées se composent d’une ou plusieurs paires nom/valeur que vous spécifiez pour une ressource de Stockage Azure. Vous pouvez les utiliser pour stocker des valeurs supplémentaires avec une ressource. Les valeurs de métadonnées sont destinées à votre usage personnel et n’affectent pas le comportement de la ressource.

La récupération des valeurs des propriétés et des métadonnées d’une ressource se déroule en deux étapes. Pour pouvoir lire ces valeurs, vous devez les récupérer explicitement en appelant l’une des méthodes **FetchAttributesAsync** ou **FetchAttributesAsync**. L’exception est si vous appelez l’une des méthodes **Exists** ou **ExistsAsync** sur une ressource. Lorsque vous appelez l’une de ces méthodes, le Stockage Azure appelle la méthode **FetchAttributes** appropriée en arrière-plan dans le cadre de l’appel de la méthode **Exists**.

> [!IMPORTANT]
> Si vous constatez que les valeurs de propriété ou de métadonnées pour une ressource de stockage n’ont pas été complétées, vérifiez que votre code appelle l’une des méthodes **FetchAttributes** ou **FetchAttributesAsync**.
>
> Les paires nom/valeur de métadonnées ne peuvent contenir que des caractères ASCII. Les paires nom/valeur de métadonnées sont des en-têtes HTTP valides, et doivent donc respecter toutes les restrictions régissant les en-têtes HTTP. Il est recommandé d’utiliser l’encodage URL ou l’encodage Base64 pour les noms et valeurs contenant des caractères non-ASCII.
>

## <a name="setting-and-retrieving-properties"></a>Définition et récupération de propriétés
Pour récupérer des valeurs de propriétés, appelez la méthode **FetchAttributesAsync** sur votre blob ou votre conteneur pour renseigner les propriétés, puis lisez les valeurs.

Pour configurer les propriétés d’un objet blob, indiquez la valeur de propriété, puis appelez la méthode **SetProperties** .

L’exemple de code suivant crée un conteneur, puis écrit certaines de ses valeurs de propriété dans une fenêtre de console.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

//Create the service client object for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it does not already exist.
container.CreateIfNotExists();

// Fetch container properties and write out their values.
await container.FetchAttributesAsync();
Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
Console.WriteLine("ETag: {0}", container.Properties.ETag);
Console.WriteLine();
```

## <a name="setting-and-retrieving-metadata"></a>Définition et récupération de métadonnées
Vous pouvez indiquer des métadonnées sous la forme de paires nom-valeur sur une ressource d’objet blob ou de conteneur. Pour configurer des métadonnées, ajoutez des paires nom/valeur à la collection **Metadata** de la ressource, puis appelez l’un des méthodes **SetMetadata** ou **SetMetadataAsync** pour enregistrer les valeurs sur le service.

> [!NOTE]
> Le nom de vos métadonnées doit respecter la convention d’affectation de noms pour les identificateurs C#.
>
>

L’exemple de code suivant définit les métadonnées d’un conteneur. Une valeur est définie à l’aide de la méthode **Add** de la collection. L’autre valeur est définie à l’aide d’une syntaxe implicite clé/valeur. Les deux sont valides.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    // Add some metadata to the container.
    container.Metadata.Add("docType", "textDocuments");
    container.Metadata["category"] = "guidance";

    // Set the container's metadata.
    await container.SetMetadataAsync();
}
```

Pour récupérer des métadonnées, appelez l’une des méthodes **FetchAttributes** ou **SetMetadataAsync** sur votre objet blob ou votre conteneur pour remplir la collection **Metadata**, puis lisez les valeurs, comme indiqué dans l’exemple suivant.

```csharp
public static async Task ListContainerMetadataAsync(CloudBlobContainer container)
{
    // Fetch container attributes in order to populate the container's properties and metadata.
    await container.FetchAttributesAsync();

    // Enumerate the container's metadata.
    Console.WriteLine("Container metadata:");
    foreach (var metadataItem in container.Metadata)
    {
        Console.WriteLine("\tKey: {0}", metadataItem.Key);
        Console.WriteLine("\tValue: {0}", metadataItem.Value);
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
* [Référence de la bibliothèque cliente Stockage Azure pour .NET](/dotnet/api/?term=Microsoft.WindowsAzure.Storage)
* [Package NuGet de la bibliothèque cliente Stockage Azure pour .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
