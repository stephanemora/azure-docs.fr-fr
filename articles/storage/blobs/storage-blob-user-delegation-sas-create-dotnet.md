---
title: Créer une SAP de délégation d’utilisateur pour un conteneur ou un objet blob avec .NET (préversion) - Stockage Azure
description: Découvrez comment créer une SAP de délégation d’utilisateur à l’aide des informations d’identification Azure Active Directory dans stockage Azure à l’aide de la bibliothèque cliente .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: c75a13a20c1dbb222db69145e24838deb111fb66
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595214"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>Créer une SAP de délégation d’utilisateur pour un conteneur ou un objet blob avec .NET (préversion)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Cet article explique comment utiliser les informations d’identification Azure Active Directory (Azure AD) pour créer une SAP de délégation d’utilisateur pour un conteneur ou un blob avec la bibliothèque de client de Stockage Azure pour .NET.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="authenticate-with-the-azure-identity-library-preview"></a>S’authentifier avec la bibliothèque d’identité Azure (version préliminaire)

La bibliothèque de client d’identité Azure pour .NET (version préliminaire) authentifie un principal de sécurité. Lorsque votre code s’exécute dans Azure, le principal de sécurité est une identité managée pour les ressources Azure.

Lorsque votre code s’exécute dans l’environnement de développement, l’authentification peut être gérée automatiquement ou nécessiter une connexion du navigateur, selon les outils que vous utilisez. Microsoft Visual Studio prend en charge l’authentification unique (SSO), afin que le compte d’utilisateur actif Azure AD soit automatiquement utilisé pour l’authentification. Pour plus d’informations sur l’authentification unique, consultez [Authentification unique aux applications](../../active-directory/manage-apps/what-is-single-sign-on.md).

D’autres outils de développement peuvent vous inviter à vous connecter via un navigateur web. Vous pouvez également utiliser un principal de service pour vous authentifier à partir de l’environnement de développement. Pour plus d’informations, consultez [Créer une identité pour une application Azure dans le portail](../../active-directory/develop/howto-create-service-principal-portal.md).

Après l’authentification, la bibliothèque de client d’identité Azure obtient des informations d’identification de jeton. Ces informations d’identification de jeton sont ensuite encapsulées dans l’objet client de service que vous créez pour effectuer des opérations sur le stockage Azure. La bibliothèque la gère en toute transparence en obtenant les informations d’identification de jeton appropriées.

Pour plus d’informations sur la bibliothèque cliente Azure Identity, consultez [Bibliothèque cliente Azure Identity pour .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

## <a name="assign-rbac-roles-for-access-to-data"></a>Attribuer des rôles RBAC pour l’accès aux données

Lorsqu’un principal de sécurité Azure AD tente d’accéder aux données blob, ce principal de sécurité doit avoir des autorisations sur la ressource. Que le principal de sécurité soit une identité managée dans Azure ou un compte d’utilisateur Azure AD exécutant du code dans l’environnement de développement, un rôle RBAC qui accorde l’accès aux données blob dans le stockage Azure doit être affecté au principal de sécurité. Pour plus d’informations sur l’attribution d’autorisations via RBAC, consultez la section intitulée **Attribuer des rôles RBAC pour les droits d’accès** dans [Autoriser l’accès aux blobs et files d’attente Azure à l’aide d’Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

## <a name="install-the-preview-packages"></a>Installer le packages de la préversion

Les exemples de cet article utilisent la dernière préversion de la [bibliothèque de client du Stockage Azure pour le stockage blob](https://www.nuget.org/packages/Azure.Storage.Blobs). Pour installer le package de la préversion, exécutez la commande suivante dans la console du gestionnaire de package NuGet :

```powershell
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

Les exemples de cet article utilisent également la dernière préversion de la [bibliothèque cliente Azure Identity pour .NET](https://www.nuget.org/packages/Azure.Identity/) pour s’authentifier avec des informations d’identification Azure AD. Pour installer le package de la préversion, exécutez la commande suivante dans la console du gestionnaire de package NuGet :

```powershell
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="add-using-directives"></a>Ajouter des directives d’utilisation

Ajoutez les directives `using` suivantes à votre code pour utiliser les préversions des bibliothèques clientes Azure Identity et de Stockage Azure.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>Obtenir des informations d’identification d’un jeton authentifié

Pour obtenir les informations d’identification d’un jeton que votre code peut utiliser pour autoriser les requêtes au stockage Azure, créez une instance de la classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential).

L’extrait de code suivant montre comment obtenir les informations d’identification d’un jeton authentifié et comment les utiliser pour créer un client de service pour le stockage blob :

```csharp
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

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
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStart);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>Créer le jeton SAP

L’extrait de code suivant montre comment créer un [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) et spécifier les paramètres de la SAP de délégation d’utilisateur. L’extrait de code appelle ensuite [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) pour récupérer la chaîne de jeton SAP. Pour finir, le code génère l’URI complet, y compris l’adresse de ressource et le jeton SAP.

```csharp
BlobSasBuilder builder = new BlobSasBuilder()
{
    ContainerName = containerName,
    BlobName = blobName,
    Permissions = "r",
    Resource = "b",
    StartTime = DateTimeOffset.UtcNow,
    ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
};

string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>Exemple : Obtenir une SAP de délégation d’utilisateur

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
    // Use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                       DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStart);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
    Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);

    // Create a SAS token that's valid a short interval.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        ContainerName = containerName,
        BlobName = blobName,
        Permissions = "r",
        Resource = "b",
        StartTime = DateTimeOffset.UtcNow,
        ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
    };

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
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Exemple : Lire un objet blob avec une SAP de délégation d’utilisateur

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
    catch (StorageRequestFailedException e)
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

- [Obtenir une opération de clé de délégation d’utilisateur](/rest/api/storageservices/get-user-delegation-key)
- [Créer une SAP de délégation d’utilisateur (API REST)](/rest/api/storageservices/create-user-delegation-sas)
