---
title: Gérer des propriétés et des métadonnées d’un blob avec .NET - Stockage Azure
description: Découvrez comment définir et récupérer des propriétés système et stocker des métadonnées personnalisées dans des blobs de votre compte de stockage Azure à l’aide de la bibliothèque de client .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 395210c582ba7f5e8170a96a46e2816336e33b2d
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624030"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Gérer les propriétés et les métadonnées blob avec .NET

En plus des données qu’ils contiennent, les blobs prennent en charge des propriétés système et des métadonnées définies par l’utilisateur. Cet article présente la gestion des propriétés système et des métadonnées définies par l’utilisateur avec la [bibliothèque cliente Stockage Azure pour .NET](/dotnet/api/overview/azure/storage/client).

## <a name="about-properties-and-metadata"></a>À propos des propriétés et des métadonnées

- **Propriétés système** : Propriétés système existant sur chaque ressource de stockage blob. Certaines d'entre elles peuvent être lues ou configurées, alors que d'autres sont en lecture seule. En arrière-plan, certaines propriétés système correspondent à certains en-têtes HTTP standard. La bibliothèque cliente de Stockage Azure pour .NET gère ces propriétés pour vous.

- **Métadonnées définies par l’utilisateur** : ces métadonnées se composent d’une ou plusieurs paires nom/valeur, que vous spécifiez pour une ressource de stockage d’objets blob. Vous pouvez les utiliser pour stocker des valeurs supplémentaires avec la ressource. Les valeurs de métadonnées sont destinées à votre usage personnel et n’affectent pas le comportement de la ressource.

La récupération des valeurs des propriétés et des métadonnées d’une ressource de stockage blob se déroule en deux étapes. Pour pouvoir lire ces valeurs, vous devez les extraire explicitement en appelant la méthode `FetchAttributes` ou `FetchAttributesAsync`. L’exception à cette règle est que les méthodes `Exists` et `ExistsAsync` appellent la méthode `FetchAttributes` appropriée en coulisses. Lorsque vous appelez une de ces méthodes, vous n’avez pas besoin d’appeler `FetchAttributes`.

> [!IMPORTANT]
> Si vous constatez que les valeurs des propriétés ou des métadonnées pour une ressource de stockage n’ont pas été alimentées, vérifiez que votre code appelle la méthode `FetchAttributes` ou `FetchAttributesAsync`.

## <a name="set-and-retrieve-properties"></a>Définir et récupérer des propriétés

L’exemple de code suivant définit les propriétés système `ContentType` et `ContentLanguage` sur un blob.

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Pour récupérer les propriétés blob, appelez la méthode `FetchAttributes` ou `FetchAttributesAsync` sur votre blob pour alimenter la propriété `Properties`. L’exemple de code suivant obtient les propriétés système d’un blob et affiche certaines des valeurs suivantes :

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

## <a name="set-and-retrieve-metadata"></a>Définir et récupérer des métadonnées

Vous pouvez indiquer des métadonnées sous la forme de paires nom-valeur sur une ressource d’objet blob ou de conteneur. Pour définir des métadonnées, ajoutez des paires nom-valeur à la collection `Metadata` sur la ressource. Appelez alors une des méthodes suivantes pour écrire les valeurs :

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

Les paires nom/valeur de métadonnées sont des en-têtes HTTP valides ; elles doivent donc respecter toutes les restrictions régissant les en-têtes HTTP. Les noms de métadonnées doivent être des noms d’en-tête HTTP et identificateurs C# valides, peuvent contenir uniquement des caractères ASCII, et doivent être considérés comme sensibles à la casse. Les valeurs de métadonnées contenant des caractères non-ASCII [codées en Base64](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) ou [codées en URL](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode).

Le nom de vos métadonnées doit respecter la convention d’affectation de noms pour les identificateurs C#. Les noms des métadonnées conservent la casse avec laquelle ils ont été créés, mais ils ne respectent pas la casse lorsqu’ils sont définis ou lus. Si deux en-têtes de métadonnées ou plus portant le même nom sont envoyés pour une ressource, le stockage blob retourne le code d’erreur HTTP 400 (requête incorrecte).

L’exemple de code suivant définit les métadonnées d’un blob. Une valeur est définie à l’aide de la méthode `Add` de la collection. L’autre valeur est définie à l’aide d’une syntaxe implicite clé/valeur.

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Pour récupérer des métadonnées, appelez la méthode `FetchAttributes` ou `FetchAttributesAsync` sur votre blob ou votre conteneur pour alimenter la collection `Metadata`, puis lisez les valeurs, comme indiqué dans l’exemple suivant.

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Voir aussi

- [Définir l’opération des propriétés blob](/rest/api/storageservices/set-blob-properties)
- [Obtenir l’opération des propriétés blob](/rest/api/storageservices/get-blob-properties)
- [Définir l’opération des métadonnées blob](/rest/api/storageservices/set-blob-metadata)
- [Obtenir l’opération des métadonnées blob](/rest/api/storageservices/get-blob-metadata)
