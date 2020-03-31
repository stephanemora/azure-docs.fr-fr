---
title: Utiliser .NET pour créer une SAS de délégation d’utilisateur pour un conteneur ou un objet blob
titleSuffix: Azure Storage
description: Découvrez comment créer une SAS de délégation d’utilisateur avec des informations d’identification Azure Active Directory à l’aide de la bibliothèque de client .NET pour Stockage Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 385d2c3b88bc2e4d653dae2dc9670cb9e9388faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371834"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>Créer une SAP de délégation d’utilisateur pour un conteneur ou un objet blob avec .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Cet article explique comment utiliser les informations d’identification Azure Active Directory (Azure AD) pour créer une SAP de délégation d’utilisateur pour un conteneur ou un blob avec la bibliothèque de client de Stockage Azure pour .NET.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-rbac-roles-for-access-to-data"></a>Attribuer des rôles RBAC pour l’accès aux données

Lorsqu’un principal de sécurité Azure AD tente d’accéder aux données blob, ce principal de sécurité doit avoir des autorisations sur la ressource. Que le principal de sécurité soit une identité managée dans Azure ou un compte d’utilisateur Azure AD exécutant du code dans l’environnement de développement, un rôle RBAC qui accorde l’accès aux données blob dans le stockage Azure doit être affecté au principal de sécurité. Pour plus d’informations sur l’attribution d’autorisations via RBAC, consultez la section intitulée **Attribuer des rôles RBAC pour les droits d’accès** dans [Autoriser l’accès aux blobs et files d’attente Azure à l’aide d’Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Pour en savoir plus sur la façon de s’authentifier avec de la bibliothèque cliente Azure Identity à partir du Stockage Azure, consultez la section intitulée **S’authentifier avec la bibliothèque d’identité Azure** dans [Autoriser l’accès aux objets blob et files d’attente avec Azure Active Directory et les identités managées pour les ressources Azure](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).

## <a name="add-using-directives"></a>Ajouter des directives d’utilisation

Ajoutez les directives `using` suivantes à votre code pour utiliser les bibliothèques de client Azure Identity et Stockage Azure.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure;
using Azure.Identity;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>Obtenir des informations d’identification d’un jeton authentifié

Pour obtenir les informations d’identification d’un jeton que votre code peut utiliser pour autoriser les requêtes au stockage Azure, créez une instance de la classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential).

L’extrait de code suivant montre comment obtenir les informations d’identification d’un jeton authentifié et comment les utiliser pour créer un client de service pour le stockage blob :

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Obtenir la clé de délégation d’utilisateur

Chaque SAP est signée avec une clé. Pour créer une SAP de délégation d’utilisateur, vous devez d’abord demander une clé de délégation d’utilisateur, qui est ensuite utilisée pour signer la SAP. La clé de délégation d’utilisateur est analogue à la clé de compte utilisée pour signer une SAP de service ou une SAP de compte, à ceci près qu’elle s’appuie sur vos informations d’identification Azure AD. Lorsqu’un client demande une clé de délégation d’utilisateur à l’aide d’un jeton OAuth 2.0, le Stockage Azure retourne la clé de délégation utilisateur pour le compte de l’utilisateur.

Une fois que vous disposez de la clé de délégation d’utilisateur, vous pouvez utiliser cette clé pour créer un nombre quelconque de signatures d’accès partagé de délégation d’utilisateur tout au long de la durée de vie de la clé. La clé de délégation d’utilisateur est indépendante du jeton OAuth 2.0 utilisé pour l’acquérir. Il n’est donc pas nécessaire de renouveler le jeton tant que la clé est toujours valide. Vous pouvez spécifier que la clé est valide pendant une période allant jusqu’à 7 jours.

Utilisez l’une des méthodes suivantes pour demander la clé de délégation d’utilisateur :

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

L’extrait de code suivant permet d’obtenir la clé de délégation d’utilisateur et d’écrire ses propriétés :

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>Créer le jeton SAP

L’extrait de code suivant montre comment créer un [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) et spécifier les paramètres de la SAP de délégation d’utilisateur. L’extrait de code appelle ensuite [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) pour récupérer la chaîne de jeton SAP. Pour finir, le code génère l’URI complet, y compris l’adresse de ressource et le jeton SAP.

```csharp
// Create a SAS token that's valid for one hour.
BlobSasBuilder sasBuilder = new BlobSasBuilder()
{
    BlobContainerName = containerName,
    BlobName = blobName,
    Resource = "b",
    StartsOn = DateTimeOffset.UtcNow,
    ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
};

// Specify read permissions for the SAS.
sasBuilder.SetPermissions(BlobSasPermissions.Read);

// Use the key to get the SAS token.
string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

// Construct the full URI, including the SAS token.
UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>Exemple : Obtenir une SAP de délégation d’utilisateur

L’exemple de méthode suivant montre le code complet pour l’authentification du principal de sécurité et la création de la SAP de délégation d’utilisateur :

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // You can use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow, 
                                                                        DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
    Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);
    Console.WriteLine();

    // Create a SAS token that's valid for one hour.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        BlobContainerName = containerName,
        BlobName = blobName,
        Resource = "b",
        StartsOn = DateTimeOffset.UtcNow,
        ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
    };

    // Specify read permissions for the SAS.
    sasBuilder.SetPermissions(BlobSasPermissions.Read);

    // Use the key to get the SAS token.
    string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

    // Construct the full URI, including the SAS token.
    UriBuilder fullUri = new UriBuilder()
    {
        Scheme = "https",
        Host = string.Format("{0}.blob.core.windows.net", accountName),
        Path = string.Format("{0}/{1}", containerName, blobName),
        Query = sasToken
    };

    Console.WriteLine("User delegation SAS URI: {0}", fullUri);
    Console.WriteLine();
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Exemple : Lire un objet blob avec une SAP de délégation d’utilisateur

L’exemple suivant teste la SAP de délégation d’utilisateur créée dans l’exemple précédent à partir d’une application cliente simulée. Si la SAP est valide, l’application cliente est en mesure de lire le contenu de l’objet blob. Si la SAP n’est pas valide, par exemple si elle a expiré, le Stockage Azure retourne le code d’erreur 403 (interdit).

```csharp
private static async Task ReadBlobWithSasAsync(Uri sasUri)
{
    // Try performing blob operations using the SAS provided.

    // Create a blob client object for blob operations.
    BlobClient blobClient = new BlobClient(sasUri, null);

    // Download and read the contents of the blob.
    try
    {
        // Download blob contents to a stream and read the stream.
        BlobDownloadInfo blobDownloadInfo = await blobClient.DownloadAsync();
        using (StreamReader reader = new StreamReader(blobDownloadInfo.Content, true))
        {
            string line;
            while ((line = reader.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }

        Console.WriteLine();
        Console.WriteLine("Read operation succeeded for SAS {0}", sasUri);
        Console.WriteLine();
    }
    catch (RequestFailedException e)
    {
        // Check for a 403 (Forbidden) error. If the SAS is invalid,
        // Azure Storage returns this error.
        if (e.Status == 403)
        {
            Console.WriteLine("Read operation failed for SAS {0}", sasUri);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Voir aussi

- [Accorder un accès limité aux ressources du Stockage Azure à l’aide des signatures d’accès partagé (SAP)](../common/storage-sas-overview.md)
- [Obtenir une opération de clé de délégation d’utilisateur](/rest/api/storageservices/get-user-delegation-key)
- [Créer une SAP de délégation d’utilisateur (API REST)](/rest/api/storageservices/create-user-delegation-sas)
