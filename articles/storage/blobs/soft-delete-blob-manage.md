---
title: Gérer et restaurer des objets blob supprimés de manière réversible
titleSuffix: Azure Storage
description: Gérez et restaurez des objets blob et des instantanés supprimés de manière réversible avec le portail Azure ou avec les bibliothèques de client de Stockage Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/23/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c66b970a99ad6dd69b9336d1638b2c156868324
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627935"
---
# <a name="manage-and-restore-soft-deleted-blobs"></a>Gérer et restaurer des objets blob supprimés de manière réversible

La suppression réversible d’objets blob protège un objet blob et ses versions, instantané et métadonnées contre les suppressions ou les remplacements accidentels en conservant les données supprimées dans le système pendant un laps de temps spécifié. Pendant la période de rétention, vous pouvez restaurer l’objet blob à son état au moment de la suppression. Une fois la période de conservation expirée, l’objet est supprimé définitivement. Pour plus d’informations sur la suppression réversible d’objets blob, consultez [Suppression réversible pour les objets blob](soft-delete-blob-overview.md).

La suppression réversible d’objets blob fait partie d’une stratégie complète de protection des données pour les données blob. Pour en savoir plus sur les recommandations de Microsoft en matière de protection des données, consultez [Vue d’ensemble de la protection des données](data-protection-overview.md).

## <a name="manage-soft-deleted-blobs"></a>Gérer les objets blob supprimés de manière réversible

### <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>Gérer les objets blob supprimés de manière réversible avec le portail Azure

Vous pouvez utiliser le portail Azure pour afficher et restaurer des captures instantanées et des objets blob supprimés de manière réversible.

#### <a name="view-deleted-blobs"></a>Afficher les objets blobs supprimés

Lorsque des objets blob sont supprimés de manière réversible, par défaut, ils sont invisibles dans le portail Azure. Pour afficher les objets blob supprimés de manière réversible, accédez à la page **Vue d’ensemble** du conteneur, et activez le paramètre **Afficher les objets blob supprimés**. Les objets blob supprimés de manière réversible sont affichés avec l’état **Supprimé**.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blobs-list-portal.png" alt-text="Capture d’écran montrant comment afficher la liste des objets blob supprimés de manière réversible dans le portail Azure":::

Ensuite, sélectionnez l’objet blob supprimé dans la liste des objets blob pour afficher ses propriétés. Sous l’onglet **Vue d’ensemble**, notez que l’état de l’objet blob est défini sur **Supprimé**. Le portail affiche également le nombre de jours jusqu’à la suppression définitive de l’objet blob.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-properties-portal.png" alt-text="Capture d’écran montrant les propriétés de l’objet blob supprimé de manière réversible dans le portail Azure":::

#### <a name="view-deleted-snapshots"></a>Afficher les captures instantanées supprimées

La suppression d’un objet blob a également pour effet de supprimer tous les instantanés qui y sont associés. Si un objet blob supprimé de manière réversible contient des captures instantanées, vous pouvez également afficher les captures instantanées supprimées dans le portail. Affichez les propriétés de l’objet blob supprimé de manière réversible, accédez à l’onglet **Instantanés**, puis activez l’option **Afficher les instantanés supprimés**.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-snapshots-portal.png" alt-text="Capture d’écran montrant ":::

#### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Restaurer des objets supprimés de manière réversible quand le contrôle de version est désactivé

Pour restaurer un objet blob supprimé de manière réversible dans le portail Azure lorsque le contrôle de version des objets blob n’est pas activé, commencez par afficher les propriétés de l’objet blob, puis sélectionnez le bouton **Annuler la suppression** sous l’onglet **Vue d’ensemble**. La restauration d’un objet blob a pour effet de restaurer également toutes les clichés instantanés supprimées pendant la période de rétention de suppression réversible.

:::image type="content" source="media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal.png" alt-text="Capture d’écran montrant comment restaurer un objet blob supprimé de manière réversible dans le portail Azure":::

Pour promouvoir une cliché instantané supprimée de manière réversible vers l’objet blob de base, assurez-vous d’abord que les captures instantanées supprimées de manière réversible ont été restaurées. Sélectionnez le bouton **Annuler la suppression** pour restaurer les captures instantanées de manière réversible, même si l’objet blob de base lui-même n’a pas été supprimé de manière réversible. Ensuite, sélectionnez la capture instantanée à promouvoir, et utilisez le bouton **Promouvoir l’instantané** pour remplacer l’objet blob de base par le contenu de l’instantané.

:::image type="content" source="media/soft-delete-blob-manage/promote-snapshot.png" alt-text="Capture d’écran montrant comment promouvoir une capture instantanée sur l’objet blob de base":::

#### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Restaurer des objets blob supprimés de manière réversible lorsque le contrôle de version est activé

Pour restaurer un objet blob supprimé de manière réversible dans le portail Azure lorsque le contrôle de version est activé, sélectionnez l’objet blob supprimé de manière réversible pour afficher ses propriétés, puis sélectionnez l’onglet **Versions**. Sélectionnez la version que vous souhaitez promouvoir comme version actuelle, puis sélectionnez **Définir comme version actuelle**.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-promote-version-portal.png" alt-text="Capture d’écran montrant comment promouvoir une version pour restaurer un objet blob dans le portail Azure":::

Pour restaurer des versions ou des captures instantanées supprimées lorsque le contrôle de version est activé, affichez les propriétés de l’objet blob, puis sélectionnez le bouton **Annuler la suppression** sous l’onglet **Vue d’ensemble**.

> [!NOTE]
> Lorsque le contrôle de version est activé, le fait de sélectionner le bouton **Annuler la suppression** sur un objet blob supprimé a pour effet de restaurer toutes les versions ou captures instantanées supprimées de manière réversible, mais pas l’objet blob de base. Pour restaurer l’objet blob de base, vous devez promouvoir une version antérieure.

### <a name="manage-soft-deleted-blobs-with-code"></a>Gérer des objets blob supprimés de manière réversible avec du code

Vous pouvez utiliser les bibliothèques de client de Stockage Azure pour restaurer un objet blob ou une capture instantanée supprimés de manière réversible. Les exemples suivants montrent comment utiliser la bibliothèque de client .NET.

#### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Restaurer des objets supprimés de manière réversible quand le contrôle de version est désactivé

##### <a name="net-v12-sdk"></a>[Kit de développement logiciel (SDK) .NET v12](#tab/dotnet)

Pour restaurer des objets blob supprimés lorsque le contrôle de version n’est pas activé, appelez l’opération [Annuler la suppression d’un objet blob](/rest/api/storageservices/undelete-blob) sur ces objets blob. L’opération **Annuler la suppression d’un objet blob** a pour effet de restaurer les objets blob supprimés de manière réversible et les captures instantanées associées à ceux-ci.

L’appel de l’opération **Annuler la suppression d’un objet blob** sur un objet blob qui n’a pas été supprimé est sans effet. L’exemple suivant appelle l’opération **Annuler la suppression d’un objet blob** sur tous les objets blob d’un conteneur, puis restaure les objets blob supprimés de manière réversible et leurs instantanés :

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Pour restaurer une capture instantanée supprimée de manière réversible, commencez par appeler l’opération **Annuler la suppression d’un objet blob** sur l’objet blob de base, puis copiez la capture instantanée souhaitée sur l’objet. Dans l’exemple suivant, un objet blob de blocs est restauré avec la dernière capture instantanée générée :

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobSnapshot":::

##### <a name="net-v11-sdk"></a>[Kit de développement logiciel (SDK) .NET v11](#tab/dotnet11)

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

#### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Restaurer des objets blob supprimés de manière réversible lorsque le contrôle de version est activé

Pour restaurer un objet blob supprimé de manière réversible lorsque le contrôle de version est activé, copiez une version antérieure sur l’objet blob de base avec une opération [Copier un objet blob](/rest/api/storageservices/copy-blob) ou [Copier un objet blob à partir d’une URL](/rest/api/storageservices/copy-blob-from-url).

##### <a name="net-v12-sdk"></a>[Kit de développement logiciel (SDK) .NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RestorePreviousVersion":::

##### <a name="net-v11-sdk"></a>[Kit de développement logiciel (SDK) .NET v11](#tab/dotnet11)

Non applicable. Le contrôle de version des objets blob n’est pris en charge que dans les bibliothèques de client de Stockage Azure versions 12.x et ultérieures.

---

## <a name="manage-soft-deleted-blobs-and-directories-hierarchical-namespace"></a>Gérer des objets blob et les répertoires supprimés de manière (espace de noms hiérarchique) réversible

Vous pouvez restaurer des répertoires et des objets blob supprimés de manière réversible dans des comptes qui ont un espace de noms hiérarchique.

> [!IMPORTANT]
> La suppression réversible dans les comptes pour lesquels la fonctionnalité d’espace de noms hiérarchique est activée est actuellement en VERSION PRÉLIMINAIRE et est disponible globalement dans toutes les régions Azure.
> Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
>
> Pour vous inscrire à la préversion, voir [ce formulaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pUOVRVOUpDRUtHVUtDUUtMVTZUR0tUMjZWNy4u).

### <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>Gérer les objets blob supprimés de manière réversible avec le portail Azure

Vous pouvez utiliser le portail Azure pour afficher et restaurer des répertoires et des objets blob supprimés de manière réversible.

#### <a name="view-deleted-blobs-and-directories"></a>Afficher les blobs et les répertoires supprimés

Lorsque des objets blob ou des répertoires sont supprimés de manière réversible, par défaut, ils sont invisibles dans le portail Azure. Pour afficher les objets blob et les répertoires supprimés de manière réversible, accédez à la page **Vue d’ensemble** du conteneur, et activez le paramètre **Afficher les objets blob supprimés**. Les objets blob et les répertoires supprimés de manière réversible sont affichés avec l’état **Supprimé**. L’image suivante montre un répertoire supprimé de manière réversible.

> [!div class="mx-imgBorder"]
> ![Capture d’écran montrant comment répertorier les objets blob supprimés de manière réversible dans le portail Azure (comptes avec espace de noms hiérarchique activé).](media/soft-delete-blob-manage/soft-deleted-blobs-list-portal-hns.png)

> [!NOTE]
> Si vous renommez un répertoire qui contient des éléments supprimés de manière réversible (sous-répertoires et blobs), ces éléments sont déconnectés du répertoire et n’apparaissent donc pas dans le portail Azure lorsque vous activez le paramètre **Afficher les objets blob supprimés**. Si vous souhaitez les afficher dans le portail Azure, vous devez rétablir le nom d’origine du répertoire ou créer un répertoire distinct qui utilise le nom de répertoire d’origine.

Ensuite, sélectionnez le répertoire ou l’objet blob supprimé dans la liste pour afficher ses propriétés. Sous l’onglet **Vue d’ensemble**, notez que l’état est défini sur **Supprimé**. Le portail affiche également le nombre de jours jusqu’à la suppression définitive de l’objet blob.

> [!div class="mx-imgBorder"]
> ![Capture d’écran montrant les propriétés d’un objet blob supprimé de manière réversible dans le portail Azure (comptes avec espace de noms hiérarchique activé).](media/soft-delete-blob-manage/soft-deleted-blob-properties-portal-hns.png)

#### <a name="restore-soft-delete-blobs-and-directories"></a>Restaurer des objets blob et des répertoires supprimés de manière réversible

Pour restaurer un objet blob ou un répertoire supprimé de manière réversible dans le portail Azure, commencez par afficher les propriétés de l’objet blob ou du répertoire, puis sélectionnez le bouton **Annuler la suppression** dans l’onglet **Vue d’ensemble**. L’image suivante montre le bouton Annuler la suppression d’un répertoire supprimé de manière réversible.

> [!div class="mx-imgBorder"]
> ![Capture d’écran montrant comment restaurer un objet blob supprimé de manière réversible dans le portail Azure (comptes avec espace de noms hiérarchique activé).](media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal-hns.png)

### <a name="restore-soft-deleted-blobs-and-directories-by-using-powershell"></a>Restaurer des objets blob et des répertoires supprimés de manière réversible à l’aide de PowerShell

> [!IMPORTANT]
> Cette section s’applique uniquement aux comptes qui ont un espace de noms hiérarchique.

1. Assurez-vous d’avoir installé le module **Az.Storage** en préversion. Pour plus d’informations, consultez [Activer la suppression réversible via PowerShell](soft-delete-blob-enable.md?tabs=azure-powershell#enable-blob-soft-delete-hierarchical-namespace).

2. Obtenez l’autorisation de compte de stockage à l’aide d’une clé de compte de stockage, d’une chaîne de connexion ou d’Azure Active Directory (Azure AD). Pour plus d’informations, consultez [Se connecter au compte](data-lake-storage-directory-file-acl-powershell.md#connect-to-the-account).

   L’exemple suivant obtient l’autorisation à l’aide d’une clé de compte de stockage.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
   ```

3. Pour restaurer un élément supprimé de manière réversible, utilisez la commande `Restore-AzDataLakeGen2DeletedItem`.

   ```powershell
   $filesystemName = "my-file-system"
   $dirName="my-directory"
   $deletedItems = Get-AzDataLakeGen2DeletedItem -Context $ctx -FileSystem $filesystemName -Path $dirName
   $deletedItems | Restore-AzDataLakeGen2DeletedItem
   ```

   Si vous renommez le répertoire qui contient les éléments supprimés de manière réversible, ces éléments sont déconnectés du répertoire. Si vous souhaitez restaurer ces éléments, vous devez rétablir le nom d’origine du répertoire ou créer un répertoire distinct qui utilise le nom de répertoire d’origine. Dans le cas contraire, vous recevrez une erreur lorsque vous tenterez de restaurer ces éléments supprimés de manière réversible.

### <a name="restore-soft-deleted-blobs-and-directories-by-using-azure-cli"></a>Restaurer des objets blob et des répertoires supprimés de manière réversible à l’aide d’Azure CLI

> [!IMPORTANT]
> Cette section s’applique uniquement aux comptes qui ont un espace de noms hiérarchique.

1. Assurez-vous d’avoir installé l’extension `storage-preview`. Pour plus d’informations, consultez [activer la suppression réversible d’objets BLOB à l’aide de PowerShell](soft-delete-blob-enable.md?tabs=azure-CLI#enable-blob-soft-delete-hierarchical-namespace).

2. Obtenez la liste des éléments supprimés.

   ```azurecli
   $filesystemName = "my-file-system"
   az storage fs list-deleted-path -f $filesystemName --auth-mode login
   ```

3. Pour restaurer un élément, utilisez la commande `az storage fs undelete-path`.

   ```azurecli
   $dirName="my-directory"
   az storage fs undelete-path -f $filesystemName --deleted-path-name $dirName -deletion-id "<deletionId>" --auth-mode login
   ```

   Si vous renommez le répertoire qui contient les éléments supprimés de manière réversible, ces éléments sont déconnectés du répertoire. Si vous souhaitez restaurer ces éléments, vous devez rétablir le nom d’origine du répertoire ou créer un répertoire distinct qui utilise le nom de répertoire d’origine. Dans le cas contraire, vous recevrez une erreur lorsque vous tenterez de restaurer ces éléments supprimés de manière réversible.

### <a name="restore-soft-deleted-blobs-and-directories-by-using-net"></a>Restaurer des objets blob et des répertoires supprimés de manière réversible à l’aide de .NET

> [!IMPORTANT]
> Cette section s’applique uniquement aux comptes qui ont un espace de noms hiérarchique.

1. Ouvrez une invite de commandes et changez de répertoire (`cd`) dans votre dossier de projet. Par exemple :

   ```console
   cd myProject
   ```

2. Installez la version `Azure.Storage.Files.DataLake -v 12.7.0` du package NuGet [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) à l’aide de la commande `dotnet add package`.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v -v 12.7.0 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. Ensuite, ajoutez les instructions using suivantes au début de votre fichier de code.

    ```csharp
    using Azure;
    using Azure.Storage;
    using Azure.Storage.Files.DataLake;
    using Azure.Storage.Files.DataLake.Models;
    using NUnit.Framework;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    ```

4. Le code suivant permet de supprimer un répertoire, puis de restaurer un répertoire supprimé de manière réversible.

   Cette méthode suppose que vous avez créé une instance [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient). Pour savoir comment créer une instance [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient), consultez [Se connecter au compte](data-lake-storage-directory-file-acl-dotnet.md#connect-to-the-account).

   ```csharp
      public void RestoreDirectory(DataLakeServiceClient serviceClient)
      {
          DataLakeFileSystemClient fileSystemClient =
             serviceClient.GetFileSystemClient("my-container");

          DataLakeDirectoryClient directory =
              fileSystem.GetDirectoryClient("my-directory");

          // Delete the Directory
          await directory.DeleteAsync();

          // List Deleted Paths
          List<PathHierarchyDeletedItem> deletedItems = new List<PathHierarchyDeletedItem>();
          await foreach (PathHierarchyDeletedItem deletedItem in fileSystemClient.GetDeletedPathsAsync())
          {
            deletedItems.Add(deletedItem);
          }

          Assert.AreEqual(1, deletedItems.Count);
          Assert.AreEqual("my-directory", deletedItems[0].Path.Name);
          Assert.IsTrue(deletedItems[0].IsPath);

          // Restore deleted directory.
          Response<DataLakePathClient> restoreResponse = await fileSystemClient.RestorePathAsync(
          deletedItems[0].Path.Name,
          deletedItems[0].Path.DeletionId);

      }

   ```

   Si vous renommez le répertoire qui contient les éléments supprimés de manière réversible, ces éléments sont déconnectés du répertoire. Si vous souhaitez restaurer ces éléments, vous devez rétablir le nom d’origine du répertoire ou créer un répertoire distinct qui utilise le nom de répertoire d’origine. Dans le cas contraire, vous recevrez une erreur lorsque vous tenterez de restaurer ces éléments supprimés de manière réversible.

### <a name="restore-soft-deleted-blobs-and-directories-by-using-java"></a>Restaurer des objets blob et des répertoires supprimés de manière réversible à l’aide de Java

> [!IMPORTANT]
> Cette section s’applique uniquement aux comptes qui ont un espace de noms hiérarchique.

1. Pour commencer, ouvrez le fichier *pom.xml* dans votre éditeur de texte. Ajoutez l’élément dépendance suivant au groupe de dépendances.

   ```xml
   <dependency>
     <groupId>com.azure</groupId>
     <artifactId>azure-storage-file-datalake</artifactId>
     <version>12.6.0</version>
   </dependency>
   ```

2. Ajoutez ensuite ces instructions Imports à votre fichier de code.

   ```java
   Put imports here
   ```

3. L’extrait de code suivant permet de restaurer un fichier supprimé de manière réversible nommé `my-file`.

   Cette méthode suppose que vous avez créé une instance **DataLakeServiceClient**. Pour savoir comment créer une instance **DataLakeServiceClient**, consultez [Se connecter au compte](data-lake-storage-directory-file-acl-java.md#connect-to-the-account).

   ```java

   public void RestoreFile(DataLakeServiceClient serviceClient){

       DataLakeFileSystemClient fileSystemClient =
           serviceClient.getFileSystemClient("my-container");

       DataLakeFileClient fileClient =
           fileSystemClient.getFileClient("my-file");

       String deletionId = null;

       for (PathDeletedItem item : fileSystemClient.listDeletedPaths()) {

           if (item.getName().equals(fileClient.getFilePath())) {
              deletionId = item.getDeletionId();
           }
       }

       fileSystemClient.restorePath(fileClient.getFilePath(), deletionId);
    }

   ```

   Si vous renommez le répertoire qui contient les éléments supprimés de manière réversible, ces éléments sont déconnectés du répertoire. Si vous souhaitez restaurer ces éléments, vous devez rétablir le nom d’origine du répertoire ou créer un répertoire distinct qui utilise le nom de répertoire d’origine. Dans le cas contraire, vous recevrez une erreur lorsque vous tenterez de restaurer ces éléments supprimés de manière réversible.

### <a name="restore-soft-deleted-blobs-and-directories-by-using-python"></a>Restaurer des objets blob et des répertoires supprimés de manière réversible à l’aide de Python

> [!IMPORTANT]
> Cette section s’applique uniquement aux comptes qui ont un espace de noms hiérarchique.

1. Installez la version `12.4.0` ou une version ultérieure de la bibliothèque cliente Azure Data Lake Storage pour Python à l’aide de [pip](https://pypi.org/project/pip/). Cette commande permet d’installer la dernière version de la bibliothèque cliente Azure Data Lake Storage pour Python.

   ```
   pip install azure-storage-file-datalake
   ```

2. Ajoutez les instructions d’importation suivantes au début de votre fichier de code.

   ```python
   import os, uuid, sys
   from azure.storage.filedatalake import DataLakeServiceClient
   from azure.storage.filedatalake import FileSystemClient
   ```

3. Le code suivant permet de supprimer un répertoire, puis de restaurer un répertoire supprimé de manière réversible.

   L’exemple de code ci-dessous contient un objet nommé `service_client` du type **DataLakeServiceClient**. Pour voir des exemples de création d’une instance **DataLakeServiceClient**, consultez [Se connecter au compte](data-lake-storage-directory-file-acl-python.md#connect-to-the-account).

    ```python
    def restoreDirectory():

        try:
            global file_system_client

            file_system_client = service_client.create_file_system(file_system="my-file-system")

            directory_path = 'my-directory'
            directory_client = file_system_client.create_directory(directory_path)
            resp = directory_client.delete_directory()

            restored_directory_client = file_system_client.undelete_path(directory_client, resp['deletion_id'])
            props = restored_directory_client.get_directory_properties()

            print(props)

        except Exception as e:
            print(e)

    ```

   Si vous renommez le répertoire qui contient les éléments supprimés de manière réversible, ces éléments sont déconnectés du répertoire. Si vous souhaitez restaurer ces éléments, vous devez rétablir le nom d’origine du répertoire ou créer un répertoire distinct qui utilise le nom de répertoire d’origine. Dans le cas contraire, vous recevrez une erreur lorsque vous tenterez de restaurer ces éléments supprimés de manière réversible.

## <a name="next-steps"></a>Étapes suivantes

- [Suppression réversible pour le stockage de blobs](soft-delete-blob-overview.md)
- [Activer la suppression réversible pour les objets blob](soft-delete-blob-enable.md)
- [Contrôle de version des blobs](versioning-overview.md)
