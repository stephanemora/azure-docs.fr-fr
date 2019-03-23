---
title: Authentifier l’accès aux objets BLOB et files d’attente avec des identités Azure Active Directory géré pour les ressources Azure - stockage Azure | Microsoft Docs
description: Le stockage d’objets blob et de files d’attente Azure prend en charge l’authentification Azure Active Directory avec des identités managées pour les ressources Azure. Vous pouvez utiliser des identités managées pour ressources Azure pour authentifier l’accès à des objets blob et à des files d’attente à partir d’applications s’exécutant dans Machines virtuelles Azure, des applications de fonction, des groupes de machines virtuelles identiques, etc. En utilisant des identités managées pour ressources Azure et en tirant parti de la puissance d’Azure AD Authentication, vous pouvez éviter de stocker des informations d’identification avec les applications qui s’exécutent dans le cloud.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4372045590938df701dd00e58a111215f6e8e56d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369647"
---
# <a name="authenticate-access-to-blobs-and-queues-with-managed-identities-for-azure-resources"></a>Authentifier l’accès aux objets BLOB et files d’attente avec des identités gérées pour les ressources Azure

Le stockage d’objets blob et de files d’attente Azure prend en charge l’authentification Azure Active Directory (Azure AD) avec des [identités managées pour ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md). Les identités managées pour ressources Azure authentifient l’accès à des objets blob et à des files d’attente en utilisant les informations d’identification d’applications s’exécutant dans Machines virtuelles Azure, d’applications de fonction, de groupe de machines virtuelles identiques, etc. En utilisant des identités managées pour ressources Azure et en tirant parti de la puissance d’Azure AD Authentication, vous pouvez éviter de stocker des informations d’identification avec les applications qui s’exécutent dans le cloud.  

Pour accorder à une identité managée des autorisations sur un conteneur d’objets blob ou une file d’attente, vous attribuez un rôle de contrôle d’accès en fonction du rôle (RBAC) à l’identité managée qui englobe les autorisations de cette ressource à l’étendue appropriée. Pour plus d’informations sur les rôles RBAC dans le stockage, consultez [gérer les droits d’accès aux données de stockage avec RBAC](storage-auth-aad-rbac.md). 

Cet article montre comment s’authentifier auprès du stockage Azure d’objets blob et de files d’attente avec une identité managée à partir d’une machine virtuelle Azure.  

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="enable-managed-identities-on-a-vm"></a>Activer les identités managées sur une machine virtuelle

Avant de pouvoir utiliser les identités managées pour ressources Azure pour authentifier l’accès à des objets blob et à des files d’attente à partir de votre machine virtuelle, vous devez activer les identités managées pour ressources Azure sur la machine virtuelle. Pour savoir comment activer des identités managées pour ressources Azure, consultez un de ces articles :

- [Portail Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interface de ligne de commande Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modèle Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Kits Azure SDK](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="assign-an-rbac-role-to-an-azure-ad-managed-identity"></a>Attribuer un rôle RBAC à une identité managée Azure AD

Pour authentifier une identité managée à partir de votre application de Stockage Azure, commencez par configurer les paramètres du contrôle d’accès basé sur un rôle (RBAC) pour cette identité managée. Le Stockage Azure définit des rôles RBAC qui englobent les autorisations pour les conteneurs et les files d’attente. Lorsque le rôle RBAC est attribué à une identité managée, cette dernière est autorisée à accéder à cette ressource. Pour plus d’informations, consultez [gérer les droits d’accès aux données d’objets Blob Azure et de file d’attente avec RBAC](storage-auth-aad-rbac.md).

## <a name="get-a-managed-identity-access-token"></a>Obtenir un jeton d’accès d’identité managée

Pour s’authentifier avec une identité managée, votre application ou script doit acquérir un jeton d’accès d’identité managée. Pour savoir comment acquérir un jeton d'accès d’identité managée, voir [Guide pratique de l’utilisation d’identités managées pour ressources Azure sur une machine virtuelle Azure afin d’acquérir un jeton d’accès](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>Exemple de code .NET : Créer un objet blob de blocs

Pour l’exemple de code, vous êtes supposé avoir un jeton d’accès d’identité managée. Le jeton d’accès permet d’autoriser l’identité managée à créer un objet blob de blocs.

### <a name="add-references-and-using-statements"></a>Ajouter des références et des instructions using  

Dans Visual Studio, installez la bibliothèque cliente Azure Storage. Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet**, puis **Console du gestionnaire de package**. Tapez la commande suivante dans la console :

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage  
```

Ajoutez les instructions using suivantes à votre code :

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-managed-identity-access-token"></a>Créer des informations d’identification à partir du jeton d’accès d’identité managée

Pour créer l’objet blob de blocs, utilisez le **TokenCredentials** classe. Construisez une nouvelle instance de **TokenCredentials**, en passant le jeton d’accès d’identité managée que vous avez obtenu :

```dotnet
// Create storage credentials from your managed identity access token.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> L’intégration d’Azure AD au Stockage Azure nécessite l’utilisation du protocole HTTPS pour les opérations relatives au Stockage Azure.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les rôles RBAC pour le stockage Azure, consultez [gérer les droits d’accès aux données de stockage avec RBAC](storage-auth-aad-rbac.md).
- Pour apprendre à autoriser l’accès aux conteneurs et aux files d’attente à partir de vos applications de stockage, consultez [Utiliser Azure AD avec les applications de stockage](storage-auth-aad-app.md).
- Pour savoir comment se connecter à Azure CLI et PowerShell avec une identité Azure AD, consultez [utiliser une identité Azure AD pour accéder au stockage Azure avec PowerShell ou CLI](storage-auth-aad-script.md).
