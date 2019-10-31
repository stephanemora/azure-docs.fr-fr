---
title: Autoriser l’accès aux objets blob et files d’attente avec Azure Active Directory et les identités managées pour les ressources Azure - Stockage Azure
description: Le stockage d’objets blob et de files d’attente Azure prend en charge l’autorisation d’accès aux ressources avec Azure Active Directory et les identités managées pour les ressources Azure. Vous pouvez utiliser des identités managées pour les ressources Azure, afin d’autoriser l’accès à des objets blob et à des files d’attente depuis des applications s’exécutant dans des machines virtuelles Azure, des applications de fonction, des groupes de machines virtuelles identiques, etc.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 833aa7dcce5c429b3005a378e93e2177df1eb0d4
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595181"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Autoriser l’accès aux objets blob et files d’attente avec Azure Active Directory et les identités managées pour les ressources Azure

Le stockage d’objets blob et de files d’attente Azure prend en charge l’authentification Azure Active Directory (Azure AD) avec des [identités managées pour ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md). Les identités managées pour ressources Azure autorisent l’accès à des objets blob et à des données de files d’attente en utilisant les informations d’identification Azure AD d’applications s’exécutant dans Machines virtuelles Azure, d’applications de fonction, de groupe de machines virtuelles identiques, et d’autres services. En utilisant des identités managées pour ressources Azure et Azure AD Authentication, vous pouvez éviter de stocker des informations d’identification avec les applications qui s’exécutent dans le cloud.  

Cet article montre comment autoriser l’accès au blob ou aux données de files d’attente avec une machine virtuelle Azure utilisant des identités managées pour des ressources Azure. Il explique également comment tester votre code dans l’environnement de développement.

## <a name="enable-managed-identities-on-a-vm"></a>Activer les identités managées sur une machine virtuelle

Avant de pouvoir utiliser les identités managées pour ressources Azure pour autoriser l’accès à des objets blob et à des files d’attente à partir de votre machine virtuelle, vous devez activer les identités managées pour ressources Azure sur la machine virtuelle. Pour savoir comment activer des identités managées pour ressources Azure, consultez un de ces articles :

- [Portail Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interface de ligne de commande Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modèle Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliothèques clientes Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Pour plus d’informations sur les identités managées, consultez [Identités managées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authenticate-with-the-azure-identity-library-preview"></a>S’authentifier avec la bibliothèque d’identité Azure (version préliminaire)

La bibliothèque de client d’identité Azure pour .NET (version préliminaire) authentifie un principal de sécurité. Lorsque votre code s’exécute dans Azure, le principal de sécurité est une identité managée pour les ressources Azure.

Lorsque votre code s’exécute dans l’environnement de développement, l’authentification peut être gérée automatiquement ou nécessiter une connexion du navigateur, selon les outils que vous utilisez. Microsoft Visual Studio prend en charge l’authentification unique (SSO), afin que le compte d’utilisateur actif Azure AD soit automatiquement utilisé pour l’authentification. Pour plus d’informations sur l’authentification unique, consultez [Authentification unique aux applications](../../active-directory/manage-apps/what-is-single-sign-on.md).

D’autres outils de développement peuvent vous inviter à vous connecter via un navigateur web. Vous pouvez également utiliser un principal de service pour vous authentifier à partir de l’environnement de développement. Pour plus d’informations, consultez [Créer une identité pour une application Azure dans le portail](../../active-directory/develop/howto-create-service-principal-portal.md).

Après l’authentification, la bibliothèque de client d’identité Azure obtient des informations d’identification de jeton. Ces informations d’identification de jeton sont ensuite encapsulées dans l’objet client de service que vous créez pour effectuer des opérations sur le stockage Azure. La bibliothèque la gère en toute transparence en obtenant les informations d’identification de jeton appropriées.

Pour plus d’informations sur la bibliothèque cliente Azure Identity, consultez [Bibliothèque cliente Azure Identity pour .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

## <a name="assign-rbac-roles-for-access-to-data"></a>Attribuer des rôles RBAC pour l’accès aux données

Lorsqu’un principal de sécurité Azure AD tente d’accéder aux données blob ou de file d’attente, ce principal de sécurité doit avoir des autorisations sur la ressource. Que le principal de sécurité soit une identité managée dans Azure ou un compte d’utilisateur Azure AD exécutant du code dans l’environnement de développement, le principale de sécurité doit se voir attribuer un rôle RBAC qui accorde l’accès aux données blob ou de file d’attente dans le stockage Azure. Pour plus d’informations sur l’attribution d’autorisations via RBAC, consultez la section intitulée **Attribuer des rôles RBAC pour les droits d’accès** dans [Autoriser l’accès aux blobs et files d’attente Azure à l’aide d’Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

## <a name="install-the-preview-packages"></a>Installer le packages de la préversion

Les exemples de cet article utilisent la dernière préversion de la bibliothèque cliente de Stockage Azure pour le stockage d’objets blob. Pour installer le package de la préversion, exécutez la commande suivante dans la console du gestionnaire de package NuGet :

```powershell
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

Les exemples de cet article utilisent également la dernière préversion de la [bibliothèque cliente Azure Identity pour .NET](https://www.nuget.org/packages/Azure.Identity/) pour s’authentifier avec des informations d’identification Azure AD. Pour installer le package de la préversion, exécutez la commande suivante dans la console du gestionnaire de package NuGet :

```powershell
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="net-code-example-create-a-block-blob"></a>Exemple de code .NET : Créer un objet blob de blocs

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

Pour obtenir les informations d’identification d’un jeton que votre code peut utiliser pour autoriser les requêtes au stockage Azure, créez une instance de la classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). L’exemple de code suivant montre comment obtenir des informations d’identification du jeton authentifié et comment l’utiliser pour créer un objet client de service, puis utiliser le client de service pour charger un nouveau blob :

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (StorageRequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Pour autoriser les requêtes sur des données d’objets BLOB ou de file d’attente avec Azure AD, vous devez utiliser HTTPS pour ces requêtes.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les rôles RBAC relatifs au Stockage Azure, consultez [Gérer les droits d’accès aux données de stockage avec RBAC](storage-auth-aad-rbac.md).
- Pour apprendre à autoriser l’accès aux conteneurs et aux files d’attente à partir de vos applications de stockage, consultez [Utiliser Azure AD avec les applications de stockage](storage-auth-aad-app.md).
- Pour savoir comment exécuter des commandes Azure CLI et PowerShell avec les informations d’identification Azure AD, consultez [Exécuter des commandes Azure CLI ou PowerShell avec des informations d’identification Azure AD pour accéder aux données d’objet blob ou de file d’attente](storage-auth-aad-script.md).
