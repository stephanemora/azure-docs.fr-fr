---
title: S’authentifier auprès d’Azure AD à partir d’une identité Managed Service Identity de machine virtuelle Azure (préversion) | Microsoft Docs
description: Authentifiez-vous auprès d’Azure AD à partir d’une identité Managed Service Identity de machine virtuelle Azure (préversion).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.openlocfilehash: 080cb3ee536227e5ddce3fac856de79b2b061dcf
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970766"
---
# <a name="authenticate-with-azure-ad-from-an-azure-managed-service-identity-preview"></a>S’authentifier auprès d’Azure AD à partir d’une identité Managed Service Identity Azure (préversion)

Stockage Azure prend en charge l’authentification Azure AD (Azure Active Directory) avec [Managed Service Identity](../../active-directory/managed-service-identity/overview.md). MSI (Managed Service Identity) fournit une identité administrée automatiquement dans Azure AD (Azure Active Directory). Vous pouvez utiliser MSI pour vous authentifier auprès du Stockage Azure à partir d’applications s’exécutant dans Machines virtuelles Azure, des applications de fonction, des groupes de machines virtuelles identiques, etc. En utilisant MSI et en tirant parti de la puissance d’Azure AD Authentication, vous pouvez éviter de stocker des informations d’identification avec les applications qui s’exécutent dans le cloud.  

Pour accorder des autorisations à une identité Managed Service Identity pour des conteneurs ou des files d’attente de stockage, vous devez attribuer un rôle RBAC englobant des autorisations de stockage à l’identité MSI. Pour plus d’informations sur les rôles RBAC relatifs au stockage, consultez [Gérer les droits d’accès aux données de stockage avec RBAC (préversion)](storage-auth-aad-rbac.md). 

> [!IMPORTANT]
> Cette préversion est destinée uniquement à une utilisation hors production. Les contrats SLA (contrats de niveau de service) de production ne sont pas disponibles tant que l’intégration d’Azure AD pour le Stockage Azure n’est pas officiellement disponible de manière générale. Si l’intégration d’Azure AD n’est pas encore prise en charge pour votre scénario, continuez à utiliser l’autorisation basée sur une clé partagée ou les jetons SAP dans vos applications. Pour plus d’informations sur la préversion, consultez [Authentifier l’accès au Stockage Azure à l’aide d’Azure Active Directory (préversion)](storage-auth-aad.md).
>
> Durant la préversion, les attributions de rôles RBAC peuvent prendre jusqu’à cinq minutes pour se propager.

Cet article montre comment s’authentifier auprès du Stockage Azure avec MSI à partir d’une machine virtuelle Azure.  

## <a name="enable-msi-on-the-vm"></a>Activer MSI sur la machine virtuelle

Pour pouvoir utiliser MSI et vous authentifier auprès du Stockage Azure à partir de votre machine virtuelle, vous devez d’abord activer MSI sur la machine virtuelle. Pour apprendre à activer MSI, consultez l’un des articles suivants :

- [Portail Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-service-identity/qs-configure-powershell-windows-vm.md)
- [interface de ligne de commande Azure](../../active-directory/managed-service-identity/qs-configure-cli-windows-vm.md)
- [Modèle Azure Resource Manager](../../active-directory/managed-service-identity/qs-configure-template-windows-vm.md)
- [Kits Azure SDK](../../active-directory/managed-service-identity/qs-configure-sdk-windows-vm.md)

## <a name="get-an-msi-access-token"></a>Obtenir un jeton d’accès MSI

Pour s’authentifier avec MSI, votre application ou votre script doit obtenir un jeton d’accès MSI. Pour apprendre à obtenir un jeton d’accès, consultez [Guide pratique pour utiliser une identité MSI (Managed Service Identity) de machine virtuelle Azure et obtenir des jetons](../../active-directory/managed-service-identity/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>Exemple de code .NET : créer un objet blob de blocs

Pour l’exemple de code, vous êtes supposé avoir un jeton d’accès MSI. Le jeton d’accès permet d’autoriser l’identité Managed Service Identity à créer un objet blob de blocs.

### <a name="add-references-and-using-statements"></a>Ajouter des références et des instructions using  

Dans Visual Studio, installez la préversion de la bibliothèque de client du Stockage Azure. Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet**, puis **Console du gestionnaire de package**. Tapez la commande suivante dans la console :

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

Ajoutez les instructions using suivantes à votre code :

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-msi-access-token"></a>Créer des informations d’identification à partir du jeton d’accès MSI

Pour créer l’objet blob de blocs, utilisez la classe **TokenCredentials** fournie par le package de préversion. Construisez une nouvelle instance de **TokenCredentials**, en passant le jeton d’accès MSI que vous avez obtenu :

```dotnet
// Create storage credentials from your MSI access token.
TokenCredential tokenCredential = new TokenCredential(msiAccessToken);
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
