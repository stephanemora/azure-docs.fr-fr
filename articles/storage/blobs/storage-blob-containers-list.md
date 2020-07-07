---
title: Répertorier les conteneurs d’objets BLOB avec .NET - Stockage Azure
description: Découvrez comment répertorier les conteneurs d’objets BLOB dans votre compte de stockage Azure à l’aide de la bibliothèque de client .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: da0c5bf6bc371bc512d9264afeab52b9908396fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84463556"
---
# <a name="list-blob-containers-with-net"></a>Répertoriez les conteneurs d’objets BLOB avec .NET

Lorsque vous répertoriez les conteneurs dans un compte de stockage Azure à partir de votre code, vous pouvez spécifier un certain nombre d’options pour gérer la façon dont les résultats sont retournés à partir du stockage Azure. Cet article explique comment répertorier les conteneurs à l’aide de la [bibliothèque de client de stockage Azure pour .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).  

## <a name="understand-container-listing-options"></a>Comprenez les options de la liste des conteneurs

Pour répertorier les conteneurs dans votre compte de stockage, appelez l’une des méthodes suivantes :

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

Les surcharges de ces méthodes fournissent des options supplémentaires pour gérer la façon dont les conteneurs sont retournés par l’opération de dressage de liste. Les étapes de cette procédure sont décrites dans les sections suivantes.

### <a name="manage-how-many-results-are-returned"></a>Gérez le nombre de résultats retournés

Par défaut, une opération de dressage de liste renvoie jusqu’à 5 000 résultats à la fois. Pour retourner un ensemble de résultats plus petit, fournissez une valeur différente de zéro pour le paramètre `maxresults` lors de l’appel de l’une des méthodes **ListContainerSegmented**.

Si votre compte de stockage contient plus de 5 000 conteneurs, ou si vous avez spécifié une valeur pour `maxresults` telle que l’opération de dressage de liste retourne un sous-ensemble de conteneurs dans le compte de stockage, le stockage Azure retourne un *jeton de continuation* avec la liste des conteneurs. Un jeton de continuation est une valeur opaque que vous pouvez utiliser pour récupérer le jeu de résultats suivant à partir du stockage Azure.

Dans votre code, vérifiez la valeur du jeton de continuation pour déterminer s’il s’agit d’une valeur Null. Lorsque le jeton de continuation a la valeur Null, l’ensemble de résultats est complété. Si le jeton de continuation n’a pas la valeur Null, appelez à nouveau **ListContainersSegmented** ou **ListContainersSegmentedAsync**, en passant le jeton de continuation pour récupérer l’ensemble de résultats suivant, jusqu’à ce que le jeton de continuation possède la valeur Null.

### <a name="filter-results-with-a-prefix"></a>Filtrez les résultats avec un préfixe

Pour filtrer la liste des conteneurs, spécifiez une chaîne pour le paramètre `prefix`. La chaîne de préfixe peut inclure un ou plusieurs caractères. Le stockage Azure retourne alors uniquement les conteneurs dont les noms commencent par ce préfixe.

### <a name="return-metadata"></a>Retourner les métadonnées

Pour retourner les métadonnées de conteneur avec les résultats, spécifiez la valeur de **Metadata** pour l’énumération [ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails). Le stockage Azure comprend des métadonnées avec chaque conteneur retourné. vous n’avez donc pas besoin d’appeler également l’une des méthodes **FetchAttributes** pour récupérer les métadonnées du conteneur.

## <a name="example-list-containers"></a>Exemple : Répertorier les conteneurs

L’exemple suivant répertorie de manière asynchrone les conteneurs dans un compte de stockage qui commencent par un préfixe spécifié. L’exemple énumère les conteneurs par incréments de 5 résultats à la fois, et utilise le jeton de continuation pour obtenir le segment suivant des résultats. L’exemple retourne également les métadonnées de conteneur avec les résultats.

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix)
{
    Console.WriteLine("List all containers beginning with prefix {0}, plus container metadata:", prefix);

    try
    {
        ContainerResultSegment resultSegment = null;
        BlobContinuationToken continuationToken = null;

        do
        {
            // List containers beginning with the specified prefix, returning segments of 5 results each.
            // Passing null for the maxResults parameter returns the max number of results (up to 5000).
            // Requesting the container's metadata with the listing operation populates the metadata,
            // so it's not necessary to also call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, 5, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token. If not null, get the next segment.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0} : {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Voir aussi

[Lister les conteneurs](/rest/api/storageservices/list-containers2)
[Énumération des ressources d’objets BLOB](/rest/api/storageservices/enumerating-blob-resources)
