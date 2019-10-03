---
title: Créer une SAP de délégation d’utilisateur pour un conteneur ou un objet blob avec .NET (préversion) - Stockage Azure
description: Découvrez comment créer une SAP de délégation d’utilisateur à l’aide des informations d’identification Azure Active Directory dans stockage Azure à l’aide de la bibliothèque cliente .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 59de768e75a88d7cfa5b68fa306d0e83f1aa0ba3
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671330"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>Créer une SAP de délégation d’utilisateur pour un conteneur ou un objet blob avec .NET (préversion)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Cet article explique comment utiliser les informations d’identification Azure Active Directory (Azure AD) pour créer une SAP de délégation d’utilisateur pour un conteneur ou un objet blob avec la [bibliothèque cliente de Stockage Azure pour .NET](https://www.nuget.org/packages/Azure.Storage.Blobs).

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-packages"></a>Installer le packages de la préversion

Les exemples de cet article utilisent la dernière préversion de la bibliothèque cliente de Stockage Azure pour le stockage d’objets blob. Pour installer le package de la préversion, exécutez la commande suivante dans la console du gestionnaire de package NuGet :

```
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

Les exemples de cet article utilisent également la dernière préversion de la [bibliothèque cliente Azure Identity pour .NET](https://www.nuget.org/packages/Azure.Identity/) pour s’authentifier avec des informations d’identification Azure AD. La bibliothèque cliente Azure Identity authentifie un principal de sécurité. Le principal de sécurité authentifié peut ensuite créer la SAP de délégation d’utilisateur. Pour plus d’informations sur la bibliothèque cliente Azure Identity, consultez [Bibliothèque cliente Azure Identity pour .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

```
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="create-a-service-principal"></a>Créer un principal du service

Pour vous authentifier avec des informations d’identification Azure AD via la bibliothèque cliente Azure Identity, utilisez un principal du service ou une identité managée comme principal de sécurité, selon l’emplacement d’exécution de votre code. Si votre code s’exécute dans un environnement de développement, utilisez un principal du service à des fins de test. Si votre code s’exécute dans Azure, utilisez une identité managée. Cet article suppose que vous exécutez du code à partir de l’environnement de développement, et montre comment utiliser un principal du service pour créer la SAP de délégation d’utilisateur.

Pour créer un principal du service avec Azure CLI et attribuer un rôle RBAC, appelez la commande [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac). Fournissez un rôle d’accès aux données de Stockage Azure à attribuer au nouveau principal du service. Le rôle doit inclure l’action **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey**. Pour plus d’informations sur les rôles intégrés fournis pour le Stockage Azure, consultez [Rôles intégrés pour les ressources Azure](../../role-based-access-control/built-in-roles.md).

Indiquez également l’étendue de l’attribution de rôle. Le principal du service crée la clé de délégation d’utilisateur, qui est une opération effectuée au niveau du compte de stockage, de sorte que l’attribution de rôle soit limitée au niveau du compte de stockage, du groupe de ressources ou de l’abonnement. Pour plus d’informations sur les autorisations RBAC pour la création d’une SAP de délégation d’utilisateur, consultez la section **Affecter des autorisations avec RBAC** dans [Créer une SAP de délégation d’utilisateur (API REST)](/rest/api/storageservices/create-user-delegation-sas).

Si vous ne disposez pas des autorisations suffisantes pour attribuer un rôle au principal du service, vous devrez peut-être demander au propriétaire du compte ou à l’administrateur d’effectuer l’attribution de rôle.

L’exemple suivant utilise Azure CLI pour créer un principal du service et lui attribuer le rôle de **lecteur de données d’objets blob de stockage** avec une étendue de compte

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

La commande `az ad sp create-for-rbac` retourne une liste de propriétés de principal du service au format JSON. Copiez ces valeurs pour pouvoir les utiliser afin de créer les variables d’environnement nécessaires à l’étape suivante.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> La propagation des attributions de rôles RBAC peut prendre plusieurs minutes.

## <a name="set-environment-variables"></a>Définition des variables d'environnement

La bibliothèque cliente Azure Identity lit les valeurs de trois variables d’environnement au moment de l’exécution pour authentifier le principal du service. Le tableau suivant indique la valeur à définir pour chaque variable d’environnement.

|Variable d’environnement|Valeur
|-|-
|`AZURE_CLIENT_ID`|ID de l’application pour le principal du service
|`AZURE_TENANT_ID`|ID de locataire Azure AD du principal du service
|`AZURE_CLIENT_SECRET`|Mot de passe généré pour le principal du service

> [!IMPORTANT]
> Après avoir défini les variables d’environnement, fermez et rouvrez votre fenêtre de console. Si vous utilisez Visual Studio ou un autre environnement de développement, vous devrez peut-être redémarrer l’environnement de développement afin qu’il enregistre les nouvelles variables d’environnement.

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

## <a name="authenticate-the-service-principal"></a>Authentifier le principal du service

Pour authentifier le principal du service, créez une instance de la classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Le constructeur `DefaultAzureCredential` lit les variables d’environnement que vous avez créées précédemment.

L’extrait de code suivant montre comment obtenir les informations d’identification authentifiées et comment les utiliser pour créer un client de service pour le Stockage d’objets blob

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
