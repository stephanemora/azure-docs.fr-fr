---
title: Authentifier l’accès aux objets blob et aux files d’attente avec des identités managées Azure Active Directory pour ressources Azure (préversion) – Stockage Azure | Microsoft Docs
description: Le stockage d’objets blob et de files d’attente Azure prend en charge l’authentification Azure Active Directory avec des identités managées pour les ressources Azure. Vous pouvez utiliser des identités managées pour ressources Azure pour authentifier l’accès à des objets blob et à des files d’attente à partir d’applications s’exécutant dans Machines virtuelles Azure, des applications de fonction, des groupes de machines virtuelles identiques, etc. En utilisant des identités managées pour ressources Azure et en tirant parti de la puissance d’Azure AD Authentication, vous pouvez éviter de stocker des informations d’identification avec les applications qui s’exécutent dans le cloud.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/05/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 67e0731c1f10bb635baa4e0d1a26dce0a336b555
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44090353"
---
# <a name="authenticate-access-to-blobs-and-queues-with-azure-managed-identities-for-azure-resources-preview"></a>Authentifier l’accès aux objets blob et aux files d’attente avec des identités managées Azure pour ressources Azure (préversion)

Le stockage d’objets blob et de files d’attente Azure prend en charge l’authentification Azure Active Directory (Azure AD) avec des [identités managées pour ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md). Vous pouvez utiliser des identités managées pour ressources Azure pour authentifier l’accès à des objets blob et à des files d’attente à partir d’applications s’exécutant dans Machines virtuelles Azure, des applications de fonction, des groupes de machines virtuelles identiques, etc. En utilisant des identités managées pour ressources Azure et en tirant parti de la puissance d’Azure AD Authentication, vous pouvez éviter de stocker des informations d’identification avec les applications qui s’exécutent dans le cloud.  

Pour accorder à une identité managée des autorisations sur un conteneur d’objets blob ou une file d’attente, vous attribuez un rôle de contrôle d’accès en fonction du rôle (RBAC) à l’identité managée qui englobe les autorisations de cette ressource à l’étendue appropriée. Pour plus d’informations sur les rôles RBAC relatifs au stockage, consultez [Gérer les droits d’accès aux données de stockage avec RBAC (préversion)](storage-auth-aad-rbac.md). 

Cet article montre comment s’authentifier auprès du stockage Azure d’objets blob et de files d’attente avec une identité managée à partir d’une machine virtuelle Azure.  

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="enable-managed-identities-on-a-vm"></a>Activer les identités managées sur une machine virtuelle

Avant de pouvoir utiliser les identités managées pour ressources Azure pour authentifier l’accès à des objets blob et à des files d’attente à partir de votre machine virtuelle, vous devez activer les identités managées pour ressources Azure sur la machine virtuelle. Pour savoir comment activer des identités managées pour ressources Azure, consultez un de ces articles :

- [Portail Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [interface de ligne de commande Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modèle Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Kits Azure SDK](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="get-a-managed-identity-access-token"></a>Obtenir un jeton d’accès d’identité managée

Pour s’authentifier avec une identité managée, votre application ou script doit acquérir un jeton d’accès d’identité managée. Pour savoir comment acquérir un jeton d'accès d’identité managée, voir [Guide pratique de l’utilisation d’identités managées pour ressources Azure sur une machine virtuelle Azure afin d’acquérir un jeton d’accès](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>Exemple de code .NET : créer un objet blob de blocs

Pour l’exemple de code, vous êtes supposé avoir un jeton d’accès d’identité managée. Le jeton d’accès permet d’autoriser l’identité managée à créer un objet blob de blocs.

### <a name="add-references-and-using-statements"></a>Ajouter des références et des instructions using  

Dans Visual Studio, installez la préversion de la bibliothèque de client du Stockage Azure. Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet**, puis **Console du gestionnaire de package**. Tapez la commande suivante dans la console :

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage  
```

Ajoutez les instructions using suivantes à votre code :

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-managed-identity-access-token"></a>Créer des informations d’identification à partir du jeton d’accès d’identité managée

Pour créer l’objet blob de blocs, utilisez la classe **TokenCredentials** fournie par le package de préversion. Construisez une nouvelle instance de **TokenCredentials**, en passant le jeton d’accès d’identité managée que vous avez obtenu :

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

- Pour en savoir plus sur les rôles RBAC relatifs au stockage Azure, consultez [Gérer les droits d’accès aux données de stockage avec RBAC (préversion)](storage-auth-aad-rbac.md).
- Pour apprendre à autoriser l’accès aux conteneurs et aux files d’attente à partir de vos applications de stockage, consultez [Utiliser Azure AD avec les applications de stockage](storage-auth-aad-app.md).
- Pour apprendre à vous connecter à Azure CLI et PowerShell avec une identité Azure AD, consultez [Utiliser une identité Azure AD pour accéder au Stockage Azure avec CLI ou PowerShell (préversion)](storage-auth-aad-script.md).
- Pour plus d’informations sur l’intégration d’Azure AD aux objets blob et files d’attente Azure, consultez le billet de blog de l’équipe Stockage Azure, [Annonce de la préversion d’Azure AD Authentication pour le Stockage Azure](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
