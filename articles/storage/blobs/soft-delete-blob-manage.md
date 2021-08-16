---
title: Gérer et restaurer des objets blob supprimés de manière réversible
titleSuffix: Azure Storage
description: Gérez et restaurez des objets blob et des instantanés supprimés de manière réversible avec le portail Azure ou avec les bibliothèques de client de Stockage Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: bdd0c8a3ddacd3a99627bfc29c41e61a165350c6
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111812365"
---
# <a name="manage-and-restore-soft-deleted-blobs"></a>Gérer et restaurer des objets blob supprimés de manière réversible

La suppression réversible d’objets blob protège un objet blob et ses versions, instantané et métadonnées contre les suppressions ou les remplacements accidentels en conservant les données supprimées dans le système pendant un laps de temps spécifié. Pendant la période de rétention, vous pouvez restaurer l’objet blob à son état au moment de la suppression. Une fois la période de conservation expirée, l’objet est supprimé définitivement. Pour plus d’informations sur la suppression réversible d’objets blob, consultez [Suppression réversible pour les objets blob](soft-delete-blob-overview.md).

La suppression réversible d’objets blob fait partie d’une stratégie complète de protection des données pour les données blob. Pour en savoir plus sur les recommandations de Microsoft en matière de protection des données, consultez [Vue d’ensemble de la protection des données](data-protection-overview.md).

## <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>Gérer les objets blob supprimés de manière réversible avec le portail Azure

Vous pouvez utiliser le portail Azure pour afficher et restaurer des captures instantanées et des objets blob supprimés de manière réversible.

### <a name="view-deleted-blobs"></a>Afficher les objets blobs supprimés

Lorsque des objets blob sont supprimés de manière réversible, par défaut, ils sont invisibles dans le portail Azure. Pour afficher les objets blob supprimés de manière réversible, accédez à la page **Vue d’ensemble** du conteneur, et activez le paramètre **Afficher les objets blob supprimés**. Les objets blob supprimés de manière réversible sont affichés avec l’état **Supprimé**.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blobs-list-portal.png" alt-text="Capture d’écran montrant comment afficher la liste des objets blob supprimés de manière réversible dans le portail Azure":::

Ensuite, sélectionnez l’objet blob supprimé dans la liste des objets blob pour afficher ses propriétés. Sous l’onglet **Vue d’ensemble**, notez que l’état de l’objet blob est défini sur **Supprimé**. Le portail affiche également le nombre de jours jusqu’à la suppression définitive de l’objet blob.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-properties-portal.png" alt-text="Capture d’écran montrant les propriétés de l’objet blob supprimé de manière réversible dans le portail Azure":::

### <a name="view-deleted-snapshots"></a>Afficher les captures instantanées supprimées

La suppression d’un objet blob a également pour effet de supprimer tous les instantanés qui y sont associés. Si un objet blob supprimé de manière réversible contient des captures instantanées, vous pouvez également afficher les captures instantanées supprimées dans le portail. Affichez les propriétés de l’objet blob supprimé de manière réversible, accédez à l’onglet **Instantanés**, puis activez l’option **Afficher les instantanés supprimés**.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-snapshots-portal.png" alt-text="Capture d’écran montrant ":::

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Restaurer des objets supprimés de manière réversible quand le contrôle de version est désactivé

Pour restaurer un objet blob supprimé de manière réversible dans le portail Azure lorsque le contrôle de version des objets blob n’est pas activé, commencez par afficher les propriétés de l’objet blob, puis sélectionnez le bouton **Annuler la suppression** sous l’onglet **Vue d’ensemble**. La restauration d’un objet blob a pour effet de restaurer également toutes les clichés instantanés supprimées pendant la période de rétention de suppression réversible.

:::image type="content" source="media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal.png" alt-text="Capture d’écran montrant comment restaurer un objet blob supprimé de manière réversible dans le portail Azure":::

Pour promouvoir une cliché instantané supprimée de manière réversible vers l’objet blob de base, assurez-vous d’abord que les captures instantanées supprimées de manière réversible ont été restaurées. Sélectionnez le bouton **Annuler la suppression** pour restaurer les captures instantanées de manière réversible, même si l’objet blob de base lui-même n’a pas été supprimé de manière réversible. Ensuite, sélectionnez la capture instantanée à promouvoir, et utilisez le bouton **Promouvoir l’instantané** pour remplacer l’objet blob de base par le contenu de l’instantané.

:::image type="content" source="media/soft-delete-blob-manage/promote-snapshot.png" alt-text="Capture d’écran montrant comment promouvoir une capture instantanée sur l’objet blob de base":::

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Restaurer des objets blob supprimés de manière réversible lorsque le contrôle de version est activé

Pour restaurer un objet blob supprimé de manière réversible dans le portail Azure lorsque le contrôle de version est activé, sélectionnez l’objet blob supprimé de manière réversible pour afficher ses propriétés, puis sélectionnez l’onglet **Versions**. Sélectionnez la version que vous souhaitez promouvoir comme version actuelle, puis sélectionnez **Définir comme version actuelle**.  

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-promote-version-portal.png" alt-text="Capture d’écran montrant comment promouvoir une version pour restaurer un objet blob dans le portail Azure":::

Pour restaurer des versions ou des captures instantanées supprimées lorsque le contrôle de version est activé, affichez les propriétés de l’objet blob, puis sélectionnez le bouton **Annuler la suppression** sous l’onglet **Vue d’ensemble**.

> [!NOTE]
> Lorsque le contrôle de version est activé, le fait de sélectionner le bouton **Annuler la suppression** sur un objet blob supprimé a pour effet de restaurer toutes les versions ou captures instantanées supprimées de manière réversible, mais pas l’objet blob de base. Pour restaurer l’objet blob de base, vous devez promouvoir une version antérieure.

## <a name="manage-soft-deleted-blobs-with-code"></a>Gérer des objets blob supprimés de manière réversible avec du code

Vous pouvez utiliser les bibliothèques de client de Stockage Azure pour restaurer un objet blob ou une capture instantanée supprimés de manière réversible. Les exemples suivants montrent comment utiliser la bibliothèque de client .NET.

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Restaurer des objets supprimés de manière réversible quand le contrôle de version est désactivé

# <a name="net-v12-sdk"></a>[Kit de développement logiciel (SDK) .NET v12](#tab/dotnet)

Pour restaurer des objets blob supprimés lorsque le contrôle de version n’est pas activé, appelez l’opération [Annuler la suppression d’un objet blob](/rest/api/storageservices/undelete-blob) sur ces objets blob. L’opération **Annuler la suppression d’un objet blob** a pour effet de restaurer les objets blob supprimés de manière réversible et les captures instantanées associées à ceux-ci.

L’appel de l’opération **Annuler la suppression d’un objet blob** sur un objet blob qui n’a pas été supprimé est sans effet. L’exemple suivant appelle l’opération **Annuler la suppression d’un objet blob** sur tous les objets blob d’un conteneur, puis restaure les objets blob supprimés de manière réversible et leurs instantanés :

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Pour restaurer une capture instantanée supprimée de manière réversible, commencez par appeler l’opération **Annuler la suppression d’un objet blob** sur l’objet blob de base, puis copiez la capture instantanée souhaitée sur l’objet. Dans l’exemple suivant, un objet blob de blocs est restauré avec la dernière capture instantanée générée :

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobSnapshot":::

# <a name="net-v11-sdk"></a>[Kit de développement logiciel (SDK) .NET v11](#tab/dotnet11)

Pour restaurer des objets blob supprimés lorsque le contrôle de version n’est pas activé, appelez l’opération [Annuler la suppression d’un objet blob](/rest/api/storageservices/undelete-blob) sur ces objets blob. L’opération **Annuler la suppression d’un objet blob** a pour effet de restaurer les objets blob supprimés de manière réversible et les captures instantanées associées à ceux-ci.

L’appel de l’opération **Annuler la suppression d’un objet blob** sur un objet blob qui n’a pas été supprimé est sans effet. L’exemple suivant appelle l’opération **Annuler la suppression d’un objet blob** sur tous les objets blob d’un conteneur, puis restaure les objets blob supprimés de manière réversible et leurs instantanés :

```csharp
// Restore all blobs in a container.
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Pour restaurer une capture instantanée supprimée de manière réversible, commencez par appeler l’opération **Annuler la suppression d’un objet blob** sur l’objet blob de base, puis copiez la capture instantanée souhaitée sur l’objet. L’exemple suivant restaure un objet blob de blocs en restaurant sa capture instantanée générée le plus récemment :

```csharp
// Restore the block blob.
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container, prefixed by the blob name.
IEnumerable<IListBlobItem> allBlobSnapshots = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Copy the most recently generated snapshot to the base blob.
CloudBlockBlob copySource = allBlobSnapshots.First(snapshot => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)snapshot).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Restaurer des objets blob supprimés de manière réversible lorsque le contrôle de version est activé

Pour restaurer un objet blob supprimé de manière réversible lorsque le contrôle de version est activé, copiez une version antérieure sur l’objet blob de base avec une opération [Copier un objet blob](/rest/api/storageservices/copy-blob) ou [Copier un objet blob à partir d’une URL](/rest/api/storageservices/copy-blob-from-url).  

# <a name="net-v12-sdk"></a>[Kit de développement logiciel (SDK) .NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RestorePreviousVersion":::

# <a name="net-v11-sdk"></a>[Kit de développement logiciel (SDK) .NET v11](#tab/dotnet11)

Non applicable. Le contrôle de version des objets blob n’est pris en charge que dans les bibliothèques de client de Stockage Azure versions 12.x et ultérieures.

---

## <a name="next-steps"></a>Étapes suivantes

- [Suppression réversible pour le stockage de blobs](soft-delete-blob-overview.md)
- [Activer la suppression réversible pour les objets blob](soft-delete-blob-enable.md)
- [Contrôle de version des blobs](versioning-overview.md)
