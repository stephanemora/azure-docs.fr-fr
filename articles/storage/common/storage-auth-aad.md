---
title: Authentifier l’accès au Stockage Azure à l’aide d’Azure Active Directory (préversion) | Microsoft Docs
description: Authentifiez l’accès au Stockage Azure à l’aide d’Azure Active Directory (préversion).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/01/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 90868961475c2e9d0ac7d28c5d9a50c8eb281675
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525203"
---
# <a name="authenticate-access-to-azure-storage-using-azure-active-directory-preview"></a>Authentifier l’accès au Stockage Azure à l’aide d’Azure Active Directory (préversion)

Le Stockage Azure prend en charge l’authentification et l’autorisation avec Azure Active Directory (AD) pour les services de Blob et de File d’attente. Avec Azure AD, vous pouvez utiliser le contrôle d’accès basé sur un rôle (RBAC) pour accorder l’accès aux utilisateurs, groupes ou principaux de service d’application. 

En autorisant les applications qui accèdent au Stockage Azure à l’aide d’Azure AD, vous obtenez une meilleure sécurité et pouvez facilement utiliser les autres options d’autorisation. Même si vous pouvez continuer à utiliser l’autorisation de clé partagée avec vos applications, avec Azure AD, vous n’avez plus besoin de stocker votre clé d’accès de compte avec votre code. De même, vous pouvez continuer à utiliser des signatures d’accès partagé (SAP) pour accorder un accès affiné aux ressources dans votre compte de stockage, mais Azure AD offre des fonctionnalités similaires sans avoir à gérer les jetons SAP ou se soucier de révoquer une SAP compromise.

## <a name="about-the-preview"></a>À propos de la préversion

Gardez à l’esprit les points suivants concernant la préversion :

- L’intégration d’Azure AD est disponible pour les services de Blob et File d’attente uniquement dans la préversion.
- L’intégration d’Azure AD est disponible pour les comptes de stockage Blob, GPv2 et GPv1 dans toutes les régions publiques. 
- Seuls les comptes de stockage créés avec le modèle de déploiement Resource Manager sont pris en charge. 
- La prise en charge des informations d’identité de l’appelant dans la journalisation Azure Storage Analytics est bientôt disponible.
- L’autorisation Azure AD pour l’accès aux ressources dans les comptes de stockage standard est actuellement prise en charge. L’autorisation d’accès aux objets blob de pages dans les comptes de stockage Premium est bientôt prise en charge.
- Le Stockage Azure prend en charge les rôles RBAC intégrés et personnalisés. Vous pouvez attribuer des rôles limités au niveau de l’abonnement, du groupe de ressources, du compte de stockage ou d’un conteneur ou d’une file d’attente individuels.
- Les bibliothèques clientes de stockage Azure qui prennent en charge actuellement l’intégration d’Azure AD sont les suivantes :
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0)
    - [Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) (utilisez 7.1.x-Preview)
    - Python
        - [Objet blob](https://github.com/Azure/azure-storage-python/releases/tag/v1.2.0rc1-blob)
        - [File d'attente](https://github.com/Azure/azure-storage-python/releases/tag/v1.2.0rc1-queue)
    - [Node.JS](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs))

> [!IMPORTANT]
> Cette préversion est destinée à une utilisation hors production uniquement. Les contrats SLA (contrats de niveau de service) de production ne sont pas disponibles tant que l’intégration d’Azure AD pour le Stockage Azure n’est pas officiellement disponible de manière générale. Si l’intégration d’Azure AD n’est pas encore prise en charge pour votre scénario, continuez à utiliser l’autorisation de clé partagée ou les jetons SAP dans vos applications.
>
> Pendant la préversion, la propagation des attributions de rôles RBAC peut prendre jusqu’à cinq minutes.
>
> L’intégration d’Azure AD au Stockage Azure nécessite d’utiliser HTTPS pour les opérations de Stockage Azure.

## <a name="get-started-with-azure-ad-for-storage"></a>Bien démarrer avec Azure AD pour le stockage

La première étape pour utiliser l’intégration d’Azure AD avec le Stockage Azure est d’attribuer des rôles RBAC pour les données de stockage à votre principal de service (utilisateur, groupe ou principal de service d’application) ou à Managed Service Identity (MSI). Les rôles RBAC englobent des jeux d’autorisations communs pour les conteneurs et les files d’attente. Pour plus d’informations sur les rôles RBAC pour le Stockage Azure, consultez [Gérer les droits d’accès aux données de stockage avec RBAC (préversion)](storage-auth-aad-rbac.md).

Pour utiliser Azure AD afin d’autoriser l’accès aux ressources de stockage dans vos applications, vous devez demander un jeton d’accès OAuth 2.0 dans votre code. Pour savoir comment demander un jeton d’accès et l’utiliser pour autoriser les demandes adressées au Stockage Azure, consultez [Authentification avec Azure AD à partir d’une application de stockage Azure (préversion)](storage-auth-aad-app.md). Pour en savoir plus sur l’utilisation de Managed Service Identity avec le Stockage Azure, consultez [Authentification avec Azure AD à partir de Managed Service Identity sur une machine virtuelle Azure (préversion)](storage-auth-aad-msi.md).

Azure CLI et PowerShell prennent désormais en charge la connexion via une identité Azure AD. Quand vous vous connectez avec une identité Azure AD, votre session s’exécute sous cette identité. Pour en savoir plus, consultez [Utiliser une identité Azure AD pour accéder au Stockage Azure avec une interface CLI ou PowerShell (préversion)](storage-auth-aad-script.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’intégration d’Azure AD aux objets blob et files d’attente Azure, consultez le billet de blog de l’équipe Stockage Azure, [Annonce de la préversion d’Azure AD Authentication pour le Stockage Azure](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
