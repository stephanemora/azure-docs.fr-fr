---
title: Spécifier une clé fournie par le client sur une demande au stockage d’objets blob avec .NET - Stockage Azure
description: Découvrez comment spécifier une clé fournie par le client sur une demande au stockage d’objets blob avec .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: c3096da8b3c83dbfe8cfdd6a5fa4d177241334de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693513"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Spécifier une clé fournie par le client sur une demande au stockage d’objets blob avec .NET

Les clients qui effectuent des requêtes auprès du Stockage Blob Azure ont la possibilité de fournir une clé de chiffrement AES-256 sur une requête individuelle. L’inclusion de la clé de chiffrement dans la requête offre un contrôle précis des paramètres de chiffrement pour les opérations de stockage d’objets blob. Les clés fournies par le client peuvent être stockées dans Azure Key Vault ou dans un autre magasin de clés.

Cet article explique comment spécifier une clé fournie par le client sur une demande avec .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Pour savoir comment s’authentifier avec la bibliothèque de client Azure Identity, consultez [Bibliothèque de client Azure Identity pour .NET](/dotnet/api/overview/azure/identity-readme).

## <a name="use-a-customer-provided-key-to-write-to-a-blob"></a>Utilisation d’une clé fournie par le client pour écrire dans un objet blob

Dans l’exemple suivant est fournie une clé AES-256 lors du chargement d’un objet blob avec la bibliothèque de client v12 pour le Stockage Blob. Ici, c’est l’objet [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) qui est utilisé pour autoriser la demande d’écriture auprès d’Azure AD, mais il est également possible de se servir à cet effet des informations d’identification de la clé partagée.

```csharp
async static Task UploadBlobWithClientKey(Uri blobUri,
                                          Stream data,
                                          byte[] key,
                                          string keySha256)
{
    // Create a new customer-provided key.
    // Key must be AES-256.
    var cpk = new CustomerProvidedKey(key);

    // Check the key's encryption hash.
    if (cpk.EncryptionKeyHash != keySha256)
    {
        throw new InvalidOperationException("The encryption key is corrupted.");
    }

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = cpk
    };

    // Create the client object with options specified.
    BlobClient blobClient = new BlobClient(
        blobUri,
        new DefaultAzureCredential(),
        options);

    // If the container may not exist yet,
    // create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient =
        blobClient.GetParentBlobContainerClient();

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

- [Fournir une clé de chiffrement lors d’une requête au stockage d’objets blob](encryption-customer-provided-keys.md)
- [Chiffrement du stockage Azure pour les données au repos](../common/storage-service-encryption.md)
