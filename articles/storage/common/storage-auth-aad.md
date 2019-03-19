---
title: Authentifier l’accès aux objets blob et aux files d’attente Azure à l’aide d’Azure Active Directory (préversion) | Microsoft Docs
description: Authentifiez l’accès aux objets blob et aux files d’attente Azure à l’aide d’Azure Active Directory (préversion).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/13/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: dcf7d237c8cfbf52a804e428d84fff0bb328c7c8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012108"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory-preview"></a>Authentifier l’accès aux objets blob et aux files d’attente Azure à l’aide d’Azure Active Directory (préversion)

Le Stockage Azure prend en charge l’authentification et l’autorisation avec Azure Active Directory (AD) pour les services de Blob et de File d’attente. Avec Azure AD, vous pouvez utiliser le contrôle d’accès basé sur un rôle (RBAC) pour accorder l’accès aux utilisateurs, groupes ou principaux de service d’application. 

L’authentification des utilisateurs et des applications à l’aide des informations d’identification Azure AD est plus sécurisée et plus facile à utiliser que les autres modes d’autorisation. Même si vous pouvez continuer à utiliser l’autorisation de clé partagée avec vos applications, avec Azure AD, vous n’avez plus besoin de stocker votre clé d’accès de compte avec votre code. Vous pouvez également continuer à utiliser des signatures d’accès partagé (SAP) pour accorder un accès affiné aux ressources de votre compte de stockage. Toutefois, Azure AD offre des fonctionnalités similaires sans nécessiter de gestion des jetons SAP, ni de révocation des SAP compromises. Dans la mesure du possible, Microsoft recommande d’utiliser l’authentification Azure AD pour les applications Stockage Azure.

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

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
    - [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)
    - Python
        - [Blob, File d’attente et Fichiers](https://github.com/Azure/azure-storage-python)
    - [Node.JS](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs)

## <a name="overview-of-azure-ad-for-storage"></a>Vue d’ensemble d’Azure AD pour le stockage

La première étape pour utiliser l’intégration d’Azure AD avec Stockage Azure est d’attribuer des rôles RBAC pour les données de stockage à votre principal de service (utilisateur, groupe ou principal de service d’application) ou des identités managées pour les ressources Azure. Les rôles RBAC englobent des jeux d’autorisations communs pour les conteneurs et les files d’attente. Pour en savoir plus sur l’attribution des rôles RBAC pour le stockage Azure, consultez [gérer les droits d’accès aux données de stockage avec RBAC (version préliminaire)](storage-auth-aad-rbac.md).

Pour utiliser Azure AD afin d’autoriser l’accès aux ressources de stockage dans vos applications, vous devez demander un jeton d’accès OAuth 2.0 dans votre code. Pour savoir comment demander un jeton d’accès et l’utiliser pour autoriser les demandes adressées au Stockage Azure, consultez [Authentification avec Azure AD à partir d’une application de stockage Azure (préversion)](storage-auth-aad-app.md). Si vous utilisez une identité managée, consultez [Authentifier l’accès aux objets blob et aux files d’attente avec des identités managées Azure pour ressources Azure (préversion)](storage-auth-aad-msi.md).

Azure CLI et PowerShell prennent désormais en charge la connexion via une identité Azure AD. Quand vous vous connectez avec une identité Azure AD, votre session s’exécute sous cette identité. Pour en savoir plus, consultez [Utiliser une identité Azure AD pour accéder au Stockage Azure avec une interface CLI ou PowerShell (préversion)](storage-auth-aad-script.md).

## <a name="rbac-roles-for-blobs-and-queues"></a>Rôles RBAC pour objets blob et files d’attente

Azure Active Directory (Azure AD) autorise les droits d’accès aux ressources sécurisées via [RBAC (contrôle d’accès en fonction du rôle)](../../role-based-access-control/overview.md). Le Stockage Azure définit un ensemble de rôles RBAC intégrés qui englobent les ensembles communs d’autorisations permettant d’accéder aux conteneurs ou aux files d’attente. 

Lorsqu’un rôle RBAC est attribué à un principal de sécurité Azure AD, Azure accorde l’accès à ces ressources pour cette entité de sécurité. L’accès peut être limité au niveau de l’abonnement, du groupe de ressources, du compte de stockage ou d’un conteneur ou d’une file d’attente individuelle. Un principal de sécurité Azure AD peut être un utilisateur, un groupe, un principal de service d’application, ou un [identité managée pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md). 

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Pour savoir comment affecter un rôle intégré dans le portail Azure, consultez [accorder l’accès aux conteneurs Azure et de files d’attente avec RBAC dans le portail Azure (aperçu)](storage-auth-aad-rbac.md).

### <a name="access-permissions-granted-by-rbac-roles"></a>Autorisations d’accès accordées par les rôles RBAC 

Le tableau suivant récapitule les droits d’accès accordées par les rôles intégrés pour les différents niveaux de portée :

|Étendue|Propriétaire des données Blob|Contributeur aux données blob|Lecteur de données blob|Contributeur aux données en file d’attente|Lecteur de données en file d’attente|
|---|---|---|---|---|---|
|Niveau de l’abonnement|Accès en lecture/écriture et gestion du contrôle d’accès POSIX à tous les conteneurs et objets blob de l’abonnement|Accès en lecture/écriture à tous les conteneurs et objets blob de l’abonnement| Accès en lecture à tous les conteneurs et objets blob de l’abonnement|Accès en lecture/écriture à toutes les files d’attente de l’abonnement|Accès en lecture à toutes les files d’attente de l’abonnement|
|Niveau du groupe de ressources|Accès en lecture/écriture et gestion du contrôle d’accès POSIX à tous les conteneurs et objets blob dans le groupe de ressources|Accès en lecture/écriture à tous les conteneurs et objets blob du groupe de ressources|Accès en lecture à tous les conteneurs et objets blob du groupe de ressources|Accès en lecture/écriture à toutes les files d’attente du groupe de ressources|Accès en lecture à toutes les files d’attente du groupe de ressources|
|Niveau de compte de stockage|Accès en lecture/écriture et gestion du contrôle d’accès POSIX à tous les conteneurs et objets blob dans le compte de stockage|Accès en lecture/écriture à tous les conteneurs et objets blob du compte de stockage|Accès en lecture à tous les conteneurs et objets blob du compte de stockage|Accès en lecture/écriture à toutes les files d’attente du compte de stockage|Accès en lecture à toutes les files d’attente du compte de stockage|
|Niveau conteneur/file d’attente|Accès en lecture/écriture et gestion du contrôle d’accès POSIX pour le conteneur spécifié et ses objets blob.|Accès en lecture/écriture au conteneur spécifié et à ses objets blob|Accès en lecture au conteneur spécifié et à ses objets blob|Accès en lecture/écriture à la file d’attente spécifiée|Accès en lecture à la file d’attente spécifiée|

Pour plus d’informations sur les autorisations nécessaires pour appeler des opérations relatives au Stockage Azure, consultez [Autorisations d’appel d’opérations REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’intégration d’Azure AD aux objets blob et files d’attente Azure, consultez le billet de blog de l’équipe Stockage Azure, [Annonce de la préversion d’Azure AD Authentication pour le Stockage Azure](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
