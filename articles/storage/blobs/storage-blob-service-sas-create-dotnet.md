---
title: Créer une SAP de service pour un conteneur ou un objet blob avec .NET
titleSuffix: Azure Storage
description: Découvrez comment créer une signature d’accès partagé (SAP) de service pour un conteneur ou un objet blob à l’aide de la bibliothèque cliente .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9674c7f892c31bd65ec651baf2d032de0256ac6c
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92218202"
---
# <a name="create-a-service-sas-for-a-container-or-blob-with-net"></a>Créer une SAP de service pour un conteneur ou un objet blob avec .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Cet article explique comment utiliser la clé du compte de stockage pour créer une SAP de service pour un conteneur ou un objet blob avec la [bibliothèque cliente Stockage Azure pour .NET](/dotnet/api/overview/azure/storage).

## <a name="create-a-service-sas-for-a-blob-container"></a>Créer une SAP de service pour un conteneur d’objets blob

L’exemple de code suivant crée une SAP pour un conteneur. Si le nom d’une stratégie d’accès stockée existante est fourni, cette stratégie est associée à la SAP. Dans le cas contraire, le code crée une SAP ad hoc sur le conteneur.

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Une SAP de service est signée avec la clé d’accès au compte. Utilisez la classe [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) afin de créer les informations d’identification utilisées pour signer le jeton SAS. Créez ensuite un objet [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) et appelez [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) pour obtenir la chaîne de jeton SAS.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetContainerSasUri":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Pour créer une SAP de service pour un conteneur, appelez la méthode [CloudBlobContainer.GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature).

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, 
                                         string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define
        // the parameters of an ad hoc SAS, and to construct a shared access policy
        // that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed
            // to be the time when the storage service receives the request. Omitting
            // the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container,
        // setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case,
        // all of the constraints for the shared access signature are specified
        // on the stored access policy, which is provided by name. It is also possible
        // to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

---

## <a name="create-a-service-sas-for-a-blob"></a>Créer une SAP de service pour un objet blob

L’exemple de code suivant crée une SAP sur un objet blob. Si le nom d’une stratégie d’accès stockée existante est fourni, cette stratégie est associée à la SAP. Dans le cas contraire, le code crée une SAP ad hoc sur l’objet blob.

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Une SAP de service est signée avec la clé d’accès au compte. Utilisez la classe [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) afin de créer les informations d’identification utilisées pour signer le jeton SAS. Créez ensuite un objet [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) et appelez [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) pour obtenir la chaîne de jeton SAS.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetBlobSasUri":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Pour créer une SAP de service pour un objet blob, appelez la méthode [CloudBlob.GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature).

```csharp
private static string GetBlobSasUri(CloudBlobContainer container,
                                    string blobName,
                                    string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters
        // of an ad hoc SAS, and to construct a shared access policy that is saved to
        // the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be
            // the time when the storage service receives the request. Omitting the start time
            // for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read |
                          SharedAccessBlobPermissions.Write |
                          SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob,
        // setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints
        // for the SAS are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Accorder un accès limité aux ressources du Stockage Azure à l’aide des signatures d’accès partagé (SAP)](../common/storage-sas-overview.md)
- [Créer une SAP de service](/rest/api/storageservices/create-service-sas)
