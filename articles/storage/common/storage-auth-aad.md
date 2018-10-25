---
title: Authentifier l’accès aux objets blob et aux files d’attente Azure à l’aide d’Azure Active Directory (préversion) | Microsoft Docs
description: Authentifiez l’accès aux objets blob et aux files d’attente Azure à l’aide d’Azure Active Directory (préversion).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/09/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 81c4990f79f0255a1ae2b3c4020dbfdebda73f6f
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116621"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory-preview"></a>Authentifier l’accès aux objets blob et aux files d’attente Azure à l’aide d’Azure Active Directory (préversion)

Le Stockage Azure prend en charge l’authentification et l’autorisation avec Azure Active Directory (AD) pour les services de Blob et de File d’attente. Avec Azure AD, vous pouvez utiliser le contrôle d’accès basé sur un rôle (RBAC) pour accorder l’accès aux utilisateurs, groupes ou principaux de service d’application. 

L’authentification des utilisateurs et des applications à l’aide des informations d’identification Azure AD est plus sécurisée et plus facile à utiliser que les autres modes d’autorisation. Même si vous pouvez continuer à utiliser l’autorisation de clé partagée avec vos applications, avec Azure AD, vous n’avez plus besoin de stocker votre clé d’accès de compte avec votre code. Vous pouvez également continuer à utiliser des signatures d’accès partagé (SAP) pour accorder un accès affiné aux ressources de votre compte de stockage. Toutefois, Azure AD offre des fonctionnalités similaires sans nécessiter de gestion des jetons SAP, ni de révocation des SAP compromises. Dans la mesure du possible, Microsoft recommande d’utiliser l’authentification Azure AD pour les applications Stockage Azure.

## <a name="about-the-preview"></a>À propos de la préversion

Gardez à l’esprit les points suivants concernant la préversion :

- L’intégration d’Azure AD est disponible pour les services de Blob et File d’attente uniquement dans la préversion.
- L’intégration d’Azure AD est disponible pour les comptes de stockage Blob, GPv2 et GPv1 dans toutes les régions publiques. 
- Seuls les comptes de stockage créés avec le modèle de déploiement Resource Manager sont pris en charge. 
- La prise en charge des informations d’identité de l’appelant dans la journalisation Azure Storage Analytics est bientôt disponible.
- L’autorisation Azure AD pour l’accès aux ressources dans les comptes de stockage standard est actuellement prise en charge. L’autorisation d’accès aux objets blob de pages dans les comptes de stockage Premium est bientôt prise en charge.
- Le Stockage Azure prend en charge les rôles RBAC intégrés et personnalisés. Vous pouvez attribuer des rôles limités au niveau de l’abonnement, du groupe de ressources, du compte de stockage ou d’un conteneur ou d’une file d’attente individuels.
- Les bibliothèques clientes de stockage Azure qui prennent en charge actuellement l’intégration d’Azure AD sont les suivantes :
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage)
    - [Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)
    - Python
        - [Blob, File d’attente et Fichiers](https://github.com/Azure/azure-storage-python)
    - [Node.JS](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs)

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="get-started-with-azure-ad-for-storage"></a>Bien démarrer avec Azure AD pour le stockage

La première étape pour utiliser l’intégration d’Azure AD avec Stockage Azure est d’attribuer des rôles RBAC pour les données de stockage à votre principal de service (utilisateur, groupe ou principal de service d’application) ou des identités managées pour les ressources Azure. Les rôles RBAC englobent des jeux d’autorisations communs pour les conteneurs et les files d’attente. Pour plus d’informations sur les rôles RBAC pour le Stockage Azure, consultez [Gérer les droits d’accès aux données de stockage avec RBAC (préversion)](storage-auth-aad-rbac.md).

Pour utiliser Azure AD afin d’autoriser l’accès aux ressources de stockage dans vos applications, vous devez demander un jeton d’accès OAuth 2.0 dans votre code. Pour savoir comment demander un jeton d’accès et l’utiliser pour autoriser les demandes adressées au Stockage Azure, consultez [Authentification avec Azure AD à partir d’une application de stockage Azure (préversion)](storage-auth-aad-app.md). Si vous utilisez une identité managée, consultez [Authentifier l’accès aux objets blob et aux files d’attente avec des identités managées Azure pour ressources Azure (préversion)](storage-auth-aad-msi.md).

Azure CLI et PowerShell prennent désormais en charge la connexion via une identité Azure AD. Quand vous vous connectez avec une identité Azure AD, votre session s’exécute sous cette identité. Pour en savoir plus, consultez [Utiliser une identité Azure AD pour accéder au Stockage Azure avec une interface CLI ou PowerShell (préversion)](storage-auth-aad-script.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’intégration d’Azure AD aux objets blob et files d’attente Azure, consultez le billet de blog de l’équipe Stockage Azure, [Annonce de la préversion d’Azure AD Authentication pour le Stockage Azure](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
