---
title: Utiliser un index d’objets blob pour gérer et rechercher des données sur le Stockage Blob Azure
description: Découvrez comment utiliser des balises d’index d’objets blob pour catégoriser, gérer et interroger afin de découvrir des objets blob.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: cc82b6578b06323d8cf9a09644d50043dba8e554
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774331"
---
# <a name="utilize-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>Utiliser des balises d’index d’objets blob (préversion) pour gérer et rechercher des données sur le Stockage Blob Azure

Les balises d’index d’objets blob catégorisent les données de votre compte de stockage à l’aide d’attributs de balise clé-valeur. Ces balises sont automatiquement indexées et exposées en tant qu’index multidimensionnel pouvant faire l’objet d’une requête pour trouver facilement des données. Cet article explique comment définir, obtenir et trouver des données à l’aide de balises d’index d’objets blob.

Pour en savoir plus sur l’index d’objets blob, consultez [Gérer et rechercher des données sur le stockage d’objets blob Azure avec un index d’objets blob (préversion)](storage-manage-find-blobs.md).

> [!NOTE]
> L’index d’objets blob, actuellement en préversion publique, est disponible dans les régions **France Centre** et **France Sud**. Pour en savoir plus sur cette fonctionnalité ainsi que sur les problèmes et limitations connus, consultez [Gérer et rechercher des données sur le stockage d’objets blob Azure avec un index d’objets blob (préversion)](storage-manage-find-blobs.md).

## <a name="prerequisites"></a>Prérequis
# <a name="portal"></a>[Portail](#tab/azure-portal)
- Abonnement inscrit et approuvé pour l’accès à la préversion de l’index d’objets blob
- Accès à [Portail Azure](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)
L’index d’objets blob étant en préversion publique, le package de stockage .NET est publié dans le flux NuGet de préversion. Cette bibliothèque est sujette à modification entre maintenant et le moment où elle deviendra officielle. 

1. Dans Visual Studio, ajoutez l’URL `https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json` à vos sources de package NuGet. 

   Pour en savoir plus, consultez [sources de package](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources).

2. Dans le gestionnaire de package NuGet, recherchez le package **Azure.Storage.Blobs** et installez la version **12.5.0-dev.20200422.2** pour votre projet. Vous pouvez également exécuter la commande ```Install-Package Azure.Storage.Blobs -Version 12.5.0-dev.20200422.2```.

   Pour savoir comment procéder, consultez [Rechercher et installer un package](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

3. Ajoutez les instructions using suivantes au début de votre fichier de code.
```csharp
using Azure;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
```
---

## <a name="upload-a-new-blob-with-index-tags"></a>Charger un nouvel objet blob avec des balises d’index
# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte de stockage. 

2. Sous **service BLOB**, accédez à l’option **Conteneurs**, puis sélectionnez votre conteneur.

3. Sélectionnez le bouton **Charger** pour ouvrir le panneau Charger et naviguez dans votre système de fichiers local pour rechercher un fichier à charger en tant qu’objet blob de blocs.

4. Développez la liste déroulante **Avancé**, puis accédez à la section **Balises d’index d’objets blob**.

5. Entrez les balises d’index d’objets blob clé/valeur que vous souhaitez appliquer à vos données.

6. Sélectionnez le bouton **Charger** pour charger l’objet blob.

![Charger des données avec des balises d’index d’objets blob](media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png)

# <a name="net"></a>[.NET](#tab/net)
L’exemple suivant montre comment créer un objet blob d’ajout avec des balises définies lors de la création.
```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");
         
          // Blob Index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>Obtenir, définir et mettre à jour des balises d’index d’objets blob
# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte de stockage. 

2. Sous **service BLOB**, accédez à l’option **Conteneurs**, puis sélectionnez votre conteneur.

3. Sélectionnez l’objet blob souhaité dans la liste des objets blob du conteneur sélectionné.

4. L’onglet Vue d’ensemble de l’objet blob affiche les propriétés de votre objet blob, dont les **balises d’index d’objets blob**.

5. Vous pouvez obtenir, définir, modifier ou supprimer n’importe quelle balise d’index clé/valeur pour votre objet blob.

6. Sélectionnez le bouton **Enregistrer** pour confirmer les mises à jour de votre objet blob.

![Obtenir, définir, mettre à jour et supprimer des balises d’index d’objets blob sur des objets](media/storage-blob-index-concepts/blob-index-get-set-tags.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or Update Blob Index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get Blob Index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List Blobs with all options returned including Blob Index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing Blob Index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>Filtrer et rechercher des données avec des balises d’index d’objets blob

# <a name="portal"></a>[Portail](#tab/azure-portal)

Dans le portail Azure, le filtre des balises d’index d’objets blob applique automatiquement le paramètre `@container` pour définir l’étendue de votre conteneur sélectionné. Si vous souhaitez filtrer et rechercher des données balisées dans votre compte de stockage, utilisez nos API REST, kits de développement logiciel (SDK) ou outils.

1. Dans le [Portail Azure](https://portal.azure.com/), sélectionnez votre compte de stockage. 

2. Sous **service BLOB**, accédez à l’option **Conteneurs**, puis sélectionnez votre conteneur.

3. Sélectionnez le bouton **Filtre des balises d’index d’objets blob** pour filtrer à l’intérieur du conteneur sélectionné.

4. Entrez une clé de balise d’index d’objets blob et une valeur de balise.

5. Sélectionnez le bouton **Filtre des balises d’index d’objets blob** pour ajouter des filtres de balises (jusqu’à 10).

![Filtrer et rechercher des objets balisés à l’aide de balises d’index d’objets blob](media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob Index queries and selection
      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob Index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<FilterBlobItem> blobs = new List<FilterBlobItem>();
          await foreach (FilterBlobItem filterBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(filterBlobItem);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>Gestion du cycle de vie avec des filtres de balises d’index d’objets blob

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Dans le [Portail Azure](https://portal.azure.com/), sélectionnez votre compte de stockage. 

2. Sous **Service blob**, accédez à l’option **Gestion du cycle de vie**.

3. Sélectionnez *Ajouter une règle*, puis complétez les champs du formulaire Ensemble d’actions.

4. Sélectionnez Jeu de filtres pour ajouter un filtre facultatif pour la correspondance des préfixes et des index d’objets blob. ![Ajouter des filtres de balises d’index d’objets blob pour la gestion du cycle de vie](media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png)

5. Sélectionnez **Vérifier + ajouter** pour examiner les paramètres de la règle. ![Exemple de règle de gestion du cycle de vie avec le filtre des balises d’index d’objets blob](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

6. Sélectionnez **Ajouter** pour appliquer la nouvelle règle à la stratégie de gestion du cycle de vie.

# <a name="net"></a>[.NET](#tab/net)
Des stratégies de [gestion du cycle de vie](storage-lifecycle-management-concepts.md) sont appliquées pour chaque compte de stockage au niveau du plan de contrôle. Pour .NET, installez la [Bibliothèque de stockage de Gestion Microsoft Azure version 16.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) ou version ultérieure pour tirer parti du filtre de correspondance d’index d’objets blob dans une règle de gestion du cycle de vie.

---

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur l’index d’objets blob. Consultez [Gérer et rechercher des données sur le stockage d’objets blob Azure avec un index d’objets blob (préversion)](storage-manage-find-blobs.md ).

Apprenez-en davantage sur la gestion du cycle de vie. Consultez [Gérer le cycle de vie du Stockage Blob Azure](storage-lifecycle-management-concepts.md)
