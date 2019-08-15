---
title: Autoriser l’accès aux objets blob et files d’attente avec Azure Active Directory et les identités managées pour les ressources Azure - Stockage Azure
description: Le stockage d’objets blob et de files d’attente Azure prend en charge l’autorisation d’accès aux ressources avec Azure Active Directory et les identités managées pour les ressources Azure. Vous pouvez utiliser des identités managées pour les ressources Azure, afin d’autoriser l’accès à des objets blob et à des files d’attente depuis des applications s’exécutant dans des machines virtuelles Azure, des applications de fonction, des groupes de machines virtuelles identiques, etc.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0f1c66126a1aa9a6ebf6f78ac6fb1ba37ba41829
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985423"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Autoriser l’accès aux objets blob et files d’attente avec Azure Active Directory et les identités managées pour les ressources Azure

Le stockage d’objets blob et de files d’attente Azure prend en charge l’authentification Azure Active Directory (Azure AD) avec des [identités managées pour ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md). Les identités managées pour ressources Azure autorisent l’accès à des objets blob et à des données de files d’attente en utilisant les informations d’identification Azure AD d’applications s’exécutant dans Machines virtuelles Azure, d’applications de fonction, de groupe de machines virtuelles identiques, et d’autres services. En utilisant des identités managées pour ressources Azure et Azure AD Authentication, vous pouvez éviter de stocker des informations d’identification avec les applications qui s’exécutent dans le cloud.  

Cet article montre comment autorise l’accès aux objets blob ou aux données de files d’attente avec une identité managée à partir d’une machine virtuelle Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Activer les identités managées sur une machine virtuelle

Avant de pouvoir utiliser les identités managées pour ressources Azure pour autoriser l’accès à des objets blob et à des files d’attente à partir de votre machine virtuelle, vous devez activer les identités managées pour ressources Azure sur la machine virtuelle. Pour savoir comment activer des identités managées pour ressources Azure, consultez un de ces articles :

- [Portail Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interface de ligne de commande Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modèle Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliothèques clientes Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>Accorder des autorisations à une identité managée Azure AD

Pour autoriser une requête auprès du service d’objets blob ou de file d’attente d’une identité managée à partir de votre application de Stockage Azure, commencez par configurer les paramètres du contrôle d’accès basé sur un rôle (RBAC) pour cette identité managée. Le Stockage Azure définit des rôles RBAC qui englobent les autorisations pour les données d’objets blob et de file d’attente. Lorsque le rôle RBAC est attribué à une identité managée, ces autorisations pour les données d’objets blob et de file d’attente sont accordées à cette identité managée selon l’étendue appropriée.

Pour plus d’informations sur l’attribution de rôles RBAC, consultez l’un des articles suivants :

- [Octroyer l’accès aux données d’objet blob et de file d’attente Azure avec RBAC dans le Portail Azure](storage-auth-aad-rbac-portal.md)
- [Octroyer l’accès aux données blob et de file d’attente Azure avec RBAC à l’aide d’Azure CLI](storage-auth-aad-rbac-cli.md)
- [Octroyer l’accès aux données blob et de file d’attente Azure avec RBAC à l’aide de PowerShell](storage-auth-aad-rbac-powershell.md)

## <a name="azure-storage-resource-id"></a>ID de ressource de stockage Azure

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Exemple de code .NET : Créer un objet blob de blocs

L’exemple de code montre comment obtenir un jeton OAuth 2.0 à partir d’Azure AD et l’utiliser pour autoriser une requête de création d’un objet blob de blocs. Pour que cet exemple fonctionne, suivez d’abord les étapes décrites dans les sections précédentes.

[!INCLUDE [storage-app-auth-lib-include](../../../includes/storage-app-auth-lib-include.md)]

### <a name="add-the-callback-method"></a>Ajouter la méthode de rappel

La méthode de rappel vérifie le délai d’expiration du jeton et renouvelle celui-ci en fonction des besoins :

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
    // Note that you can also specify the root URI for your storage account as the resource ID.
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

### <a name="get-a-token-and-create-a-block-blob"></a>Obtenir un jeton et créer un objet blob de blocs

La bibliothèque de client App Authentication fournit la classe **AzureServiceTokenProvider**. Une instance de cette classe peut être transmise à un rappel qui obtient un jeton, puis le renouvelle avant son expiration.

L’exemple suivant obtient un jeton et l’utilise pour créer un objet blob, puis utilise le même jeton pour lire l’objet blob.

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = await TokenRenewerAsync(azureServiceTokenProvider,CancellationToken.None);

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
await blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage.
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}",
                        DateTimeOffset.UtcNow,
                        await blob.DownloadTextAsync());

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

Pour plus d’informations sur la bibliothèque App Authentication, consultez [Authentification de service à service à Azure Key Vault avec .NET](../../key-vault/service-to-service-authentication.md).

Pour savoir comment acquérir un jeton d’accès d’identité managée, voir [Guide pratique de l’utilisation d’identités managées pour ressources Azure sur une machine virtuelle Azure afin d’acquérir un jeton d’accès](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

> [!NOTE]
> Pour autoriser les requêtes sur des données d’objets BLOB ou de file d’attente avec Azure AD, vous devez utiliser HTTPS pour ces requêtes.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les rôles RBAC relatifs au Stockage Azure, consultez [Gérer les droits d’accès aux données de stockage avec RBAC](storage-auth-aad-rbac.md).
- Pour apprendre à autoriser l’accès aux conteneurs et aux files d’attente à partir de vos applications de stockage, consultez [Utiliser Azure AD avec les applications de stockage](storage-auth-aad-app.md).
- Pour savoir comment exécuter des commandes Azure CLI et PowerShell avec les informations d’identification Azure AD, consultez [Exécuter des commandes Azure CLI ou PowerShell avec des informations d’identification Azure AD pour accéder aux données d’objet blob ou de file d’attente](storage-auth-aad-script.md).
