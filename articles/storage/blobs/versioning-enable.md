---
title: Activer et gérer le contrôle de version des objets blob (préversion)
titleSuffix: Azure Storage
description: Découvrez comment activer le contrôle de version des objets blob dans le Portail Azure ou à l’aide d’un modèle Azure Resource Manager.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/15/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 7ff0bb6d593a199bd249441cf484d1f0580774ac
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086045"
---
# <a name="enable-and-manage-blob-versioning"></a>Activer et gérer le contrôle de version des objets blob

Vous pouvez activer ou désactiver le contrôle de version des objets blob (préversion) pour le compte de stockage à tout moment à l’aide du Portail Azure ou d’un modèle Azure Resource Manager.

## <a name="enable-blob-versioning"></a>Activer la gestion des versions des objets blob

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour activer le contrôle de version des objets blob dans le Portail Azure :

1. Accédez à votre compte de stockage dans le portail.
1. Sous **Service BLOB**, choisissez **Protection des données**.
1. Dans la section **Gestion de version**, sélectionnez **Activée**.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Capture d’écran représentant l’activation du contrôle de version des objets blob dans le Portail Azure":::

# <a name="template"></a>[Modèle](#tab/template)

Pour activer le contrôle de version des objets blob avec un modèle, créez un modèle avec la propriété **IsVersioningEnabled** sur **true**. Les étapes suivantes montrent comment créer un modèle dans le Portail Azure.

1. Dans le Portail Azure, choisissez **Créer une ressource**.
1. Dans **Rechercher sur la Place de marché**, tapez **déploiement de modèle**, puis appuyez sur **Entrée**.
1. Choisissez **Template deployment**, **Créer**, puis **Créer votre propre modèle dans l’éditeur**.
1. Dans l’éditeur de modèle, collez le code JSON suivant. Remplacez la valeur d’espace réservé `<accountName>` par le nom de votre compte de stockage.
1. Enregistrez le modèle.
1. Spécifiez le groupe de ressources du compte, puis choisissez le bouton **Acheter** pour déployer le modèle et activer le contrôle de version des objets blob.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Pour plus d’informations sur le déploiement de ressources à l’aide de modèles dans le Portail Azure, consultez [Déployer des ressources avec le Portail Azure](../../azure-resource-manager/templates/deploy-portal.md).

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Modifier un objet blob pour déclencher une nouvelle version

L’exemple de code suivant montre comment déclencher la création d’une nouvelle version avec la bibliothèque de client Stockage Microsoft Azure pour .NET, version [12.5.0-preview.5](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.0-preview.5) ou ultérieure. Avant d’exécuter cet exemple, assurez-vous que vous avez activé le contrôle de version pour votre compte de stockage.

L’exemple crée un objet blob de blocs, puis met à jour les métadonnées de cet objet. La mise à jour des métadonnées de l’objet blob déclenche la création d’une nouvelle version. L’exemple récupère la version initiale et la version actuelle, et indique que seule la version actuelle contient les métadonnées.

```csharp
public static async Task UpdateVersionedBlobMetadata(string containerName, string blobName)
{
    // Create a new service client from the connection string.
    BlobServiceClient blobServiceClient = new BlobServiceClient(ConnectionString);

    // Create a new container client.
    BlobContainerClient containerClient = blobServiceClient.GetBlobContainerClient(containerName);

    try
    {
        // Create the container.
        await containerClient.CreateIfNotExistsAsync();

        // Upload a block blob.
        BlockBlobClient blockBlobClient = containerClient.GetBlockBlobClient(blobName);

        string blobContents = string.Format("Block blob created at {0}.", DateTime.Now);
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        string initalVersionId;
        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            Response<BlobContentInfo> uploadResponse = await blockBlobClient.UploadAsync(stream, null, default);

            // Get the version ID for the current version.
            initalVersionId = uploadResponse.Value.VersionId;
        }

        // Update the blob's metadata to trigger the creation of a new version.
        Dictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "key", "value" },
            { "key1", "value1" }
        };

        Response<BlobInfo> metadataResponse = await blockBlobClient.SetMetadataAsync(metadata);

        // Get the version ID for the new current version.
        string newVersionId = metadataResponse.Value.VersionId;

        // Request metadata on the previous version.
        BlockBlobClient initalVersionBlob = blockBlobClient.WithVersion(initalVersionId);
        Response<BlobProperties> propertiesResponse = await initalVersionBlob.GetPropertiesAsync();
        PrintMetadata(propertiesResponse);

        // Request metadata on the current version.
        BlockBlobClient newVersionBlob = blockBlobClient.WithVersion(newVersionId);
        Response<BlobProperties> newPropertiesResponse = await newVersionBlob.GetPropertiesAsync();
        PrintMetadata(newPropertiesResponse);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}

static void PrintMetadata(Response<BlobProperties> propertiesResponse)
{
    if (propertiesResponse.Value.Metadata.Count > 0)
    {
        Console.WriteLine("Metadata values for version {0}:", propertiesResponse.Value.VersionId);
        foreach (var item in propertiesResponse.Value.Metadata)
        {
            Console.WriteLine("Key:{0}  Value:{1}", item.Key, item.Value);
        }
    }
    else
    {
        Console.WriteLine("Version {0} has no metadata.", propertiesResponse.Value.VersionId);
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

- [Contrôle de version des objets blob (préversion)](versioning-overview.md)
- [Suppression réversible pour les objets blob de Stockage Azure](soft-delete-overview.md)
