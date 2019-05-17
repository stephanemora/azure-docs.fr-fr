---
title: Authentifier l’accès aux objets BLOB et files d’attente avec des identités gérées pour les ressources Azure - stockage Azure | Microsoft Docs
description: Stockage d’objets Blob et file d’attente Azure prennent en charge l’authentification Azure Active Directory avec des identités gérées pour les ressources Azure. Vous pouvez utiliser des identités managées pour ressources Azure pour authentifier l’accès à des objets blob et à des files d’attente à partir d’applications s’exécutant dans Machines virtuelles Azure, des applications de fonction, des groupes de machines virtuelles identiques, etc.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f7525c3e125010bb4db9655bc214861e22dc8875
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787974"
---
# <a name="authenticate-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Authentifier l’accès aux objets BLOB et files d’attente avec Azure Active Directory et des identités gérées pour les ressources Azure

Le stockage d’objets blob et de files d’attente Azure prend en charge l’authentification Azure Active Directory (Azure AD) avec des [identités managées pour ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md). Géré les identités pour les ressources Azure peuvent autoriser l’accès aux objets blob et les données de file d’attente à l’aide des informations d’identification Azure AD à partir d’applications qui s’exécutent dans des machines virtuelles (VM) Azure, des applications de fonction, des machines virtuelles identiques et d’autres services. À l’aide des identités gérées pour les ressources Azure avec l’authentification Azure AD, vous pouvez éviter de stocker les informations d’identification avec vos applications qui s’exécutent dans le cloud.  

Cet article montre comment autoriser l’accès aux données d’objet blob ou file d’attente avec une identité gérée à partir d’une machine virtuelle Azure. 

## <a name="enable-managed-identities-on-a-vm"></a>Activer les identités managées sur une machine virtuelle

Avant de pouvoir utiliser des identités gérées pour les ressources Azure pour autoriser l’accès aux objets BLOB et files d’attente à partir de votre machine virtuelle, vous devez d’abord activer des identités gérées pour les ressources Azure sur la machine virtuelle. Pour savoir comment activer des identités managées pour ressources Azure, consultez un de ces articles :

- [Portail Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interface de ligne de commande Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modèle Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Kits Azure SDK](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>Accorder des autorisations à une identité Azure AD géré

Pour autoriser une demande au service Blob ou file d’attente à partir d’une identité gérée dans votre application Azure Storage, tout d’abord configurer les paramètres de contrôle (RBAC) d’accès en fonction du rôle pour cette identité gérée. Stockage Azure définit les rôles RBAC qui englobent les autorisations pour les données d’objet blob et file d’attente. Lorsque le rôle RBAC est attribué à une identité gérée, l’identité gérée est accordée ces autorisations aux données d’objet blob ou file d’attente à l’étendue appropriée. 

Pour plus d’informations sur l’affectation des rôles RBAC, consultez les articles suivants :

- [Accorder l’accès aux données blob et file d’attente Azure avec RBAC dans le portail Azure](storage-auth-aad-rbac-portal.md)
- [Accorder l’accès à des données d’objet blob et file d’attente Azure avec RBAC à l’aide d’Azure CLI](storage-auth-aad-rbac-cli.md)
- [Accorder l’accès à des données d’objet blob et file d’attente Azure avec RBAC à l’aide de PowerShell](storage-auth-aad-rbac-powershell.md)

## <a name="authorize-with-a-managed-identity-access-token"></a>Autoriser avec un jeton d’accès identité gérée

Pour autoriser les demandes auprès du stockage Blob et file d’attente avec une identité gérée, votre application ou le script doit acquérir un jeton OAuth. Le [Microsoft Azure App Authentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) bibliothèque cliente pour .NET (version préliminaire) simplifie le processus d’acquisition et de renouveler un jeton à partir de votre code.

La bibliothèque cliente de l’authentification d’application gère automatiquement l’authentification. La bibliothèque utilise les informations d’identification du développeur pour authentifier pendant le développement local. Il est plus sûr d’utiliser les informations d’identification du développeur pendant le développement local, car vous n’avez pas besoin de créer des informations d’identification Azure AD ou de partager des informations d’identification entre les développeurs. Lorsque la solution est déployée ultérieurement vers Azure, la bibliothèque bascule automatiquement à l’aide des informations d’identification de l’application.

Pour utiliser la bibliothèque d’authentification de l’application dans une application de stockage Azure, installez le dernier package de version préliminaire à partir de [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication), ainsi que la dernière version de la [bibliothèque client commune de stockage Azure pour .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) et le [bibliothèque cliente de stockage Blob Azure pour .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/). Ajoutez le code suivant **à l’aide de** instructions à votre code :

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```

La bibliothèque d’authentification de l’application fournit le **le paramètre AzureServiceTokenProvider** classe. Une instance de cette classe peut être passée à un rappel qui obtient un jeton, puis renouvelle le jeton avant son expiration.

L’exemple suivant obtient un jeton utilise pour créer un nouvel objet blob, puis utilise le même jeton à lire l’objet blob.

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = TokenRenewerAsync(azureServiceTokenProvider, 
                                            CancellationToken.None).GetAwaiter().GetResult();

// Create storage credentials using the initial token, and connect the callback function 
// to renew the token just before it expires
TokenCredential tokenCredential = new TokenCredential(tokenAndFrequency.Token, 
                                                        TokenRenewerAsync,
                                                        azureServiceTokenProvider, 
                                                        tokenAndFrequency.Frequency.Value);

StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a blob using the storage credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), 
                                            storageCredentials);

// Upload text to the blob.
blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage. 
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}", 
                        DateTimeOffset.UtcNow, 
                        blob.DownloadTextAsync().Result);

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

La méthode de rappel vérifie le délai d’expiration du jeton et renouvelle celui-ci en fonction des besoins :

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
    const string StorageResource = "https://storage.azure.com/";  

    // Use the same token provider to request a new token.
    var authResult = await ((AzureServiceTokenProvider)state).GetAuthenticationResultAsync(StorageResource);

    // Renew the token 5 minutes before it expires.
    var next = (authResult.ExpiresOn - DateTimeOffset.UtcNow) - TimeSpan.FromMinutes(5);
    if (next.Ticks < 0)
    {
        next = default(TimeSpan);
        Console.WriteLine("Renewing token...");
    }

    // Return the new token and the next refresh time.
    return new NewTokenAndFrequency(authResult.AccessToken, next);
}
```

Pour plus d’informations sur la bibliothèque d’authentification de l’application, consultez [l’authentification de Service à service dans Azure Key Vault à l’aide de .NET](../../key-vault/service-to-service-authentication.md). 

Pour en savoir plus sur comment acquérir un jeton d’accès, consultez [l’utilisation des identités gérées pour les ressources Azure sur une machine virtuelle Azure pour acquérir un jeton d’accès](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

> [!NOTE]
> Pour autoriser les requêtes sur des objets blob ou file d’attente des données avec Azure AD, vous devez utiliser HTTPS pour ces demandes.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les rôles RBAC pour le stockage Azure, consultez [gérer les droits d’accès aux données de stockage avec RBAC](storage-auth-aad-rbac.md).
- Pour apprendre à autoriser l’accès aux conteneurs et aux files d’attente à partir de vos applications de stockage, consultez [Utiliser Azure AD avec les applications de stockage](storage-auth-aad-app.md).
- Pour savoir comment exécuter des commandes Azure CLI et PowerShell avec les informations d’identification Azure AD, consultez [exécuter Azure CLI ou PowerShell des commandes avec informations d’identification Azure AD pour accéder aux données d’objet blob ou file d’attente](storage-auth-aad-script.md).