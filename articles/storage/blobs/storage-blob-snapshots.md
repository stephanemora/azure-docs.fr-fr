---
title: Créer et gérer un instantané blob dans .NET - Stockage Azure
description: Découvrez comment créer un instantané d’un blob en lecture seule pour sauvegarder des données blob à un moment donné.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/06/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9bf5eea55002814f461d375b3db43a37fe4f7aa9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80474085"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Créer et gérer un instantané blob dans .NET

Un instantané est une version en lecture seule d'un objet blob capturé à un instant donné. Les captures instantanées sont utiles pour la sauvegarde des objets blob. Cet article explique comment créer ou gérer des instantanés blob à l’aide de la [bibliothèque de client de Stockage Azure pour .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-blob-snapshots"></a>À propos des instantanés blob

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Un instantané d’un objet blob est identique à l’objet blob de base, à la différence que l’URI de l’objet blob a une valeur **DateTime** à la fin qui indique l’heure à laquelle l’instantané a été pris. Par exemple, si l’URI de l’objet blob de pages est `http://storagesample.core.blob.windows.net/mydrives/myvhd`, l’URI de l’instantané est du type `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Tous les instantanés partagent l’URI de l’objet blob de base. La seule distinction entre l’objet blob de base et l’instantané est la valeur **DateTime** ajoutée à la fin.
>

Un objet blob peut avoir plusieurs instantanés. Les instantanés persistent jusqu’à ce qu’ils soient explicitement supprimés, ce qui signifie qu’un instantané ne peut pas survivre à son blob de base. Vous pouvez énumérer les instantanés associés à l’objet blob de base pour effectuer le suivi de vos instantanés actuels.

Lorsque vous créez un instantané d’un objet blob, les propriétés système de l’objet blob sont copiées dans l’instantané avec les mêmes valeurs. Les métadonnées de l’objet blob de base sont également copiées dans l’instantané, sauf si vous spécifiez des métadonnées distinctes pour l’instantané lorsque vous le créez. Une fois créé, un instantané peut être lu, copié ou supprimé, mais pas modifié.

Aucun bail associé à l’objet blob de base n’a d’incidence sur l’instantané. Vous ne pouvez pas acquérir de bail pour un instantané.

Un fichier de disque dur virtuel est utilisé pour stocker les informations et l’état actuels d’un disque de machine virtuelle. Vous pouvez détacher un disque de la machine virtuelle ou arrêter la machine virtuelle, puis prendre une capture instantanée de son fichier de disque dur virtuel. Vous pourrez par la suite utiliser ce fichier de capture instantanée pour récupérer le fichier de disque dur virtuel à ce moment précis et recréer la machine virtuelle.

## <a name="create-a-snapshot"></a>Créer un instantané

Pour créer un instantané d’un objet blob de blocs, utilisez l’une des méthodes suivantes :

- [CreateSnapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

L’exemple de code suivant montre comment créer un instantané. Cet exemple spécifie des métadonnées supplémentaires pour la capture instantanée lors de sa création.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="delete-snapshots"></a>Suppression d'instantanés

Pour supprimer un blob, vous devez d’abord supprimer tous les instantanés de ce blob. Vous pouvez supprimer un instantané individuellement, ou spécifier que tous les instantanés doivent être supprimés lors de la suppression de l’objet blob source. Si vous essayez de supprimer un objet blob auquel des instantanés sont encore associés, une erreur se produit.

Pour supprimer des instantanés de blob, utilisez l’une des méthodes de suppression de blob suivantes et incluez l’énumération [DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption).

- [Supprimer](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

L'exemple de code suivant montre comment supprimer un objet blob et ses captures instantanées dans .NET, où `blockBlob` est un objet de type [CloudBlockBlob][dotnet_CloudBlockBlob] :

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Renvoi de l'URI absolu d'un instantané

L’exemple de code suivant crée un instantané et écrit l’URI absolu de l’emplacement principal.

```csharp
//Create the blob service client object.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();

//Get a reference to a blob.
CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
blob.UploadText("This is a blob.");

//Create a snapshot of the blob and write out its primary URI.
CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);
```

## <a name="understand-how-snapshots-accrue-charges"></a>Présentation des frais liés aux instantanés

La création d’un instantané, c’est-à-dire d’une copie en lecture seule d’un objet blob, peut entraîner des frais de stockage de données supplémentaires pour votre compte. Lors de la conception de votre application, il est important de savoir comment ces frais peuvent s’accumuler pour pouvoir réduire les coûts.

### <a name="important-billing-considerations"></a>Considérations importantes relatives à la facturation

La liste suivante contient des points clés à prendre en compte lors de la création d’un instantané.

- Des frais s’appliquent à votre compte de stockage pour des pages ou des blocs uniques, qu’ils soient dans l’objet blob ou dans l’instantané. Aucuns frais supplémentaires ne sont imputés à votre compte pour les instantanés associés à un objet blob tant que vous n'avez pas mis à jour l’objet blob sur lequel ils sont basés. Une fois que vous avez mis à jour l’objet blob de base, il diffère de ses instantanés. Dans ce cas, vous êtes facturé pour les blocs ou pages uniques de chaque objet blob ou instantané.
- Quand vous remplacez un bloc au sein d’un objet blob de bloc, ce bloc est ensuite facturé comme un bloc unique. Cela est vrai même si le bloc a le même ID de bloc et les mêmes données que dans l'instantané. Une fois le bloc revalidé, il diffère de son homologue dans tous les instantanés et vous serez facturé pour ses données. Il en va de même pour une page dans un objet blob de pages qui est mise à jour avec des données identiques.
- Le remplacement d'un objet blob de blocs en appelant la méthode [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream] ou [UploadFromByteArray][dotnet_UploadFromByteArray] remplace tous les blocs de l'objet blob. Si vous avez une capture instantanée associée à cet objet blob, tous les blocs dans l’objet blob de base et la capture instantanée seront désormais différents et vous serez facturé pour tous les blocs des deux objets blob. Cela est vrai même si les données de l’objet blob de base et l’instantané restent identiques.
- Le service blob Azure ne dispose d'aucun moyen pour déterminer si deux blocs contiennent des données identiques. Chaque bloc qui est téléchargé et validé est traité comme étant unique, même s’il a les mêmes données et le même ID de bloc. Comme des frais sont applicables aux blocs uniques, il est important de savoir que la mise à jour d’un objet blob qui a une capture instantanée entraîne la création de blocs uniques supplémentaires et donc des frais supplémentaires.

### <a name="minimize-cost-with-snapshot-management"></a>Réduire les coûts grâce à la gestion des captures instantanées

Nous vous recommandons de gérer vos captures instantanées avec soin pour éviter des frais supplémentaires. Vous pouvez suivre ces meilleures pratiques pour réduire les coûts liés au stockage de vos captures instantanées :

- Supprimez et recréez les instantanés associés à un objet blob chaque fois que vous mettez à jour l'objet blob, même si vous le mettez à jour avec des données identiques, sauf si la conception de votre application exige de tenir à jour des instantanés. En supprimant et en recréant les captures instantanées de l’objet blob, vous pouvez vous assurer que l’objet blob et les captures instantanées ne diffèrent pas.
- Si vous tenez à jour les captures instantanées d'un objet blob, évitez d'appeler [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream] ou [UploadFromByteArray][dotnet_UploadFromByteArray] pour mettre à jour l'objet blob. Ces méthodes remplacent tous les blocs dans l’objet blob. Ainsi, votre objet blob de base et vos captures instantanées diffèrent de façon significative. Mettez plutôt à jour le moins de blocs possible en utilisant les méthodes [PutBlock][dotnet_PutBlock] et [PutBlockList][dotnet_PutBlockList].

### <a name="snapshot-billing-scenarios"></a>Scénarios de facturation d’instantanés

Les scénarios suivants illustrent l’accumulation des coûts pour un objet blob de blocs et ses instantanés.

#### <a name="scenario-1"></a>Scénario 1

Dans le scénario 1, l'objet blob de base n'a pas été mis à jour depuis la capture de l'instantané. Des frais sont donc applicables uniquement pour les blocs uniques 1, 2 et 3.

![Ressources Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scénario 2

Dans le scénario 2, l'objet blob de base a été mis à jour, mais pas l'instantané. Le bloc 3 a été mis à jour, et bien qu’il contienne les mêmes données et le même ID, il est différent du bloc 3 de l’instantané. Par conséquent, des frais pour quatre blocs sont facturés au compte.

![Ressources Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scénario 3

Dans le scénario 3, l'objet blob de base a été mis à jour, mais pas l'instantané. Le bloc 3 a été remplacé par le bloc 4 dans l’objet blob de base, mais l’instantané reflète toujours le bloc 3. Par conséquent, des frais pour quatre blocs sont facturés au compte.

![Ressources Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scénario 4

Dans le scénario 4, l'objet blob de base a été complètement mis à jour et ne contient aucun de ses blocs d'origine. Par conséquent, des frais pour les huit blocs uniques sont facturés au compte. Ce scénario peut se produire si vous utilisez une méthode de mise à jour telle que [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream] ou [UploadFromByteArray][dotnet_UploadFromByteArray], car ces méthodes remplacent tout le contenu d'un objet blob.

![Ressources Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Étapes suivantes

- Vous trouverez plus d’informations sur l’utilisation des captures instantanées de disque de machine virtuelle (VM) dans [Sauvegarder les disques de machines virtuelles Azure non gérés avec des captures instantanées incrémentielles](../../virtual-machines/windows/incremental-snapshots.md)

- Pour obtenir des exemples de code supplémentaires d’utilisation du Stockage Blob, consultez [Exemples de code Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Vous pouvez télécharger un exemple d’application et l’exécuter ou parcourir le code sur GitHub.

[dotnet_AccessCondition]: https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.accesscondition
[dotnet_CloudBlockBlob]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblockblob
[dotnet_CreateSnapshotAsync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.createsnapshotasync
[dotnet_HTTPStatusCode]: https://docs.microsoft.com/java/api/com.microsoft.store.partnercenter.network.httpstatuscode
[dotnet_PutBlockList]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblocklist
[dotnet_PutBlock]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblock
[dotnet_UploadFromByteArray]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblob.uploadfrombytearray
[dotnet_UploadFromFile]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblob.uploadfromfile
[dotnet_UploadFromStream]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadfromstream
[dotnet_UploadText]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadtext
