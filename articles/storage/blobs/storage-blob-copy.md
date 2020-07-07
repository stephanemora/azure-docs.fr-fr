---
title: Copier un objet blob avec .NET – Stockage Azure
description: Découvrez comment copier un objet blob dans votre compte de stockage Azure à l’aide de la bibliothèque cliente .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: f4e6e2f2732d1c90e8fe669788d82692c8016fd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84463448"
---
# <a name="copy-a-blob-with-net"></a>Copier un objet blob avec .NET

Cet article explique comment copier un objet blob avec un compte de stockage Azure. Il montre également comment abandonner une opération de copie asynchrone. L’exemple de code utilise la [bibliothèque cliente de stockage Azure pour .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-copying-blobs"></a>À propos de la copie d’objets blob

Lorsque vous copiez un objet blob dans le même compte de stockage, il s’agit d’une opération synchrone. Lorsque vous effectuez la copie d’un compte à un autre, il s’agit d’une opération asynchrone. Les méthodes [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) et [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) retournent une valeur d’ID de copie utilisée pour vérifier l’état ou abandonner l’opération de copie.

L’objet blob source d’une opération de copie peut être un objet blob de blocs, un objet blob d’ajout, un objet blob de pages ou un instantané. Si l’objet blob de destination existe déjà, il doit être du même type d’objet blob que l’objet blob source. Tout objet blob de destination existant sera remplacé. 

L’objet blob de destination ne peut pas être modifié pendant une opération de copie. Un objet blob de destination ne peut avoir qu’une seule opération de copie d’objet blob en attente. En d’autres termes, un objet blob ne peut pas être la destination de plusieurs opérations de copie en attente.

L’intégralité du fichier ou de l’objet blob source est toujours copiée. La copie d’une plage d’octets ou d’un ensemble de blocs n’est pas prise en charge.

Lorsqu’un objet blob est copié, ses propriétés système sont copiées dans l’objet blob de destination avec les mêmes valeurs.

Pour tous les types d’objet blob, vous pouvez vérifier la propriété [CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) dans l’objet blob de destination pour obtenir l’état de l’opération de copie. L’objet blob final est validé à la fin de la copie.

Une opération de copie peut prendre l’une des formes suivantes :

  - Vous pouvez copier un objet blob source vers un objet blob de destination avec un nom différent. L’objet blob de destination peut être un objet blob existant du même type d’objet blob (bloc, ajout ou page), ou il peut s’agir d’un nouvel objet blob créé par l’opération de copie.
  - Vous pouvez copier un objet blob source vers un objet blob de destination portant le même nom, ce qui a pour effet de remplacer l’objet blob de destination. Cette opération supprime tous les blocs non validés et remplace les métadonnées de l’objet blob de destination.
  - Vous pouvez copier un fichier source figurant dans le service Azure File vers un objet blob de destination. L’objet blob de destination peut être un objet blob existant ou un nouvel objet blob de blocs créé par l’opération de copie. La copie de fichiers vers des objets blob de pages ou d’ajout n’est pas prise en charge.
  - Vous pouvez copier un instantané sur son objet blob de base. En plaçant un instantané à la place d'un objet blob de base, vous pouvez restaurer une version antérieure de l’objet blob.
  - Vous pouvez copier un instantané sur un objet blob de destination avec un nom différent. L’objet blob de destination obtenu est un objet blob modifiable et non pas un instantané.

## <a name="copy-a-blob"></a>Copier un objet blob

Pour copier un objet blob, appelez l’une des méthodes suivantes :

 - [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

L’exemple de code suivant obtient une référence à un objet blob créé précédemment et copie ce dernier dans un nouvel objet blob dans le même conteneur :

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
            Console.WriteLine();
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

## <a name="abort-a-blob-copy-operation"></a>Abandonner une opération de copie d’objet blob

L’abandon d’une opération de copie entraîne un objet blob de destination de longueur nulle pour des objets blob de blocs, d’ajout et de pages. Toutefois, les métadonnées de l’objet blob de destination comporteront les nouvelles valeurs copiées à partir de l’objet blob source ou définies explicitement dans l’appel [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) ou [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet). Pour conserver les métadonnées d’origine antérieures à la copie, créez un instantané de l’objet blob de destination avant d’appeler `StartCopy` ou `StartCopyAsync`.

Lorsque vous abandonnez une opération de copie d’objet blob en cours, la propriété [CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) de l’objet blob de destination est définie sur [CopyStatus.Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet).

Les méthodes [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) et [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) annulent une opération de copie d’objet blob en cours et laissent un objet blob de destination avec une longueur nulle et des métadonnées complètes.

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Les rubriques suivantes contiennent des informations sur la copie des objets blob et sur l’abandon des opérations de copie en cours à l’aide des API REST Azure.

- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Abort Copy Blob](/rest/api/storageservices/abort-copy-blob)
