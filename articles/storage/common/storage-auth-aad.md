---
title: Authentifier l’accès aux objets BLOB Azure et files d’attente à l’aide d’Azure Active Directory | Microsoft Docs
description: Authentifier l’accès aux objets BLOB Azure et files d’attente à l’aide d’Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 66051bd0f8be349f748c72218d538bba273be8f6
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147269"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Authentifier l’accès aux objets BLOB Azure et files d’attente à l’aide d’Azure Active Directory

Le Stockage Azure prend en charge l’authentification et l’autorisation avec Azure Active Directory (AD) pour les services de Blob et de File d’attente. Avec Azure AD, vous pouvez utiliser le contrôle d’accès basé sur un rôle (RBAC) pour accorder l’accès aux utilisateurs, groupes ou principaux de service d’application. 

L’authentification des utilisateurs et des applications à l’aide des informations d’identification Azure AD est plus sécurisée et plus facile à utiliser que les autres modes d’autorisation. Même si vous pouvez continuer à utiliser l’autorisation de clé partagée avec vos applications, avec Azure AD, vous n’avez plus besoin de stocker votre clé d’accès de compte avec votre code. Vous pouvez également continuer à utiliser des signatures d’accès partagé (SAP) pour accorder un accès affiné aux ressources de votre compte de stockage. Toutefois, Azure AD offre des fonctionnalités similaires sans nécessiter de gestion des jetons SAP, ni de révocation des SAP compromises. Dans la mesure du possible, Microsoft recommande d’utiliser l’authentification Azure AD pour les applications Stockage Azure.

Authentification et autorisation avec les informations d’identification Azure AD est disponible pour tous les à usage général et dans toutes les régions publiques et les clouds nationaux, les comptes de stockage d’objets Blob. Seuls les comptes de stockage créé avec la prise en charge du modèle de déploiement Azure Resource Manager d’Azure AD.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Vue d’ensemble d’Azure AD pour les objets BLOB et files d’attente

Lorsqu’un principal de sécurité (utilisateur, groupe ou application) tente d’accéder à une ressource d’objet blob ou file d’attente, la demande doit être autorisée, sauf s’il est un objet blob disponible pour l’accès anonyme. Avec Azure AD, accès à une ressource est un processus en deux étapes. Tout d’abord, identité de l’entité de sécurité est authentifiée et un jeton OAuth 2.0 est retourné. Ensuite, le jeton est passé en tant que partie d’une demande au service Blob ou file d’attente et utilisé par le service pour autoriser l’accès à la ressource spécifiée.

L’étape de l’authentification nécessite qu’une application demande un jeton d’accès OAuth 2.0 lors de l’exécution. Si une application s’exécute à partir d’une entité Azure comme une machine virtuelle Azure, un jeu de mise à l’échelle de machine virtuelle ou une application Azure Functions, il peut utiliser un [identité gérée](../../active-directory/managed-identities-azure-resources/overview.md) pour l’accès aux objets BLOB ou les files d’attente. Pour savoir comment autoriser des demandes effectuées par une identité gérée sur le service de file d’attente ou d’objets Blob Azure, consultez [authentifier l’accès aux objets BLOB et files d’attente avec Azure Active Directory et des identités gérées pour les ressources Azure](storage-auth-aad-msi.md).

L’étape d’autorisation exige qu’un ou plusieurs rôles RBAC soient attribués à l’entité de sécurité. Stockage Azure fournit des rôles RBAC qui englobent des ensembles communs d’autorisations pour les données d’objet blob et file d’attente. Les rôles sont attribués à un principal de sécurité déterminent les autorisations dont le principal sera. Pour en savoir plus sur l’attribution des rôles RBAC pour le stockage Azure, consultez [gérer les droits d’accès aux données de stockage avec RBAC](storage-auth-aad-rbac.md).

Applications natives et les applications web qui effectuent des demandes au service Blob Azure ou de la file d’attente peuvent également s’authentifier auprès d’Azure AD. Pour savoir comment demander un jeton d’accès et l’utiliser pour autoriser les demandes pour les données d’objet blob ou file d’attente, consultez [s’authentifier avec Azure AD à partir d’une application de stockage Azure](storage-auth-aad-app.md).

## <a name="assigning-rbac-roles-for-access-rights"></a>Attribution de rôles RBAC pour les droits d’accès

Azure Active Directory (Azure AD) autorise les droits d’accès aux ressources sécurisées via [RBAC (contrôle d’accès en fonction du rôle)](../../role-based-access-control/overview.md). Stockage Azure définit un ensemble de rôles RBAC intégrés qui englobent des ensembles communs d’autorisations permettant d’accéder aux données blob et file d’attente. Vous pouvez également définir des rôles personnalisés pour l’accès aux données blob et file d’attente.

Lorsqu’un rôle RBAC est attribué à un principal de sécurité Azure AD, Azure accorde l’accès à ces ressources pour cette entité de sécurité. L’accès peut être limité au niveau de l’abonnement, du groupe de ressources, du compte de stockage ou d’un conteneur ou d’une file d’attente individuelle. Un principal de sécurité Azure AD peut être un utilisateur, un groupe, un principal de service d’application, ou un [identité managée pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Rôles RBAC intégrés pour les objets BLOB et files d’attente

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Pour savoir comment affecter un rôle RBAC intégré à un principal de sécurité, consultez les articles suivants :

- [Accorder l’accès aux données blob et file d’attente Azure avec RBAC dans le portail Azure](storage-auth-aad-rbac-portal.md)
- [Accorder l’accès à des données d’objet blob et file d’attente Azure avec RBAC à l’aide d’Azure CLI](storage-auth-aad-rbac-cli.md)
- [Accorder l’accès à des données d’objet blob et file d’attente Azure avec RBAC à l’aide de PowerShell](storage-auth-aad-rbac-powershell.md)

Pour plus d’informations sur la définition des rôles intégrés pour le Stockage Azure, consultez [Comprendre les définitions de rôles](../../role-based-access-control/role-definitions.md#management-and-data-operations-preview). Pour plus d’informations sur la création des rôles RBAC personnalisés, consultez [créer des rôles personnalisés pour le contrôle d’accès](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Autorisations d’accès pour les opérations de données

Pour plus d’informations sur les autorisations requises pour appeler des opérations de service Blob ou file d’attente spécifiques, consultez [autorisations pour appeler des opérations de données d’objets blob et file d’attente](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Portée de la ressource

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Accéder aux données avec un compte Azure AD

Accès aux données d’objet blob ou file d’attente via le portail Azure, PowerShell, ou Azure CLI peuvent être autorisé à l’aide de compte Azure AD de l’utilisateur ou en utilisant les touches d’accès de compte (authentification par clé partagée).

### <a name="data-access-from-the-azure-portal"></a>Accès aux données à partir du portail Azure

Le portail Azure permettre utiliser votre compte Azure AD ou les clés d’accès de compte pour accéder aux données blob et file d’attente dans un compte de stockage Azure. Quel schéma d’autorisation utilise le portail Azure varie selon les rôles RBAC qui vous sont assignés.

Lorsque vous tentez d’accéder aux données blob ou file d’attente, le portail Azure vérifie d’abord si vous avez été affecté un rôle RBAC avec **Microsoft.Storage/storageAccounts/listkeys/action**. Si vous avez été affecté à un rôle avec cette action, le portail Azure utilise la clé de compte pour accéder aux données blob et file d’attente par le biais de l’autorisation de clé partagée. Si vous n’avez pas été affecté un rôle avec cette action, le portail Azure tente d’accéder aux données à l’aide de votre compte Azure AD.

Pour accéder aux objets blob ou file d’attente des données à partir du portail Azure à l’aide de votre compte Azure AD, vous avez besoin des autorisations pour accéder aux données blob et file d’attente, et vous devez également des autorisations pour parcourir les ressources de compte de stockage dans le portail Azure. Les rôles intégrés fournis par le stockage Azure accordent l’accès aux ressources d’objets blob et file d’attente, mais ils n’accorder des autorisations aux ressources de compte de stockage. Pour cette raison, l’accès au portail requiert également l’attribution d’un rôle de gestionnaire de ressources Azure telles que la [lecteur](../../role-based-access-control/built-in-roles.md#reader) rôle, étendu au niveau du compte de stockage ou une version ultérieure. Le **lecteur** rôle accorde les autorisations plus limitées, mais un autre rôle d’Azure Resource Manager qui accorde l’accès aux ressources de gestion de compte de stockage est également acceptable. Pour en savoir plus sur la façon d’affecter des autorisations aux utilisateurs pour accéder aux données dans le portail Azure avec un compte Azure AD, consultez [accorder l’accès aux données blob et file d’attente Azure avec RBAC dans le portail Azure](storage-auth-aad-rbac-portal.md).

Le portail Azure indique quel schéma d’autorisation est en cours d’utilisation lorsque vous accédez à un conteneur ou d’une file d’attente. Pour plus d’informations sur l’accès aux données dans le portail, consultez [utiliser le portail Azure pour accéder aux données blob ou file d’attente](storage-access-blobs-queues-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Accès aux données à partir de PowerShell ou Azure CLI

Azure CLI et PowerShell prend en charge la connexion avec informations d’identification Azure AD. Après que vous être connecté, votre session s’exécute sous ces informations d’identification. Pour plus d’informations, consultez [exécuter Azure CLI ou PowerShell des commandes avec informations d’identification Azure AD pour accéder aux données d’objet blob ou file d’attente](storage-auth-aad-script.md).

## <a name="azure-ad-authentication-over-smb-for-azure-files"></a>Authentification Azure AD sur SMB pour Azure Files

Azure Files prend en charge l’authentification avec Azure AD sur SMB uniquement pour les machines virtuelles jointes au domaine (préversion). Pour en savoir plus sur l’utilisation d’Azure AD sur SMB pour Azure Files, consultez [authentification de la vue d’ensemble d’Azure Active Directory sur SMB pour les fichiers Azure (aperçu)](../files/storage-files-active-directory-overview.md).

## <a name="next-steps"></a>Étapes suivantes

- [Authentifier l’accès aux objets BLOB et files d’attente avec Azure Active Directory et des identités gérées pour les ressources Azure](storage-auth-aad-msi.md)
- [S’authentifier auprès d’Azure Active Directory à partir d’une application pour accéder aux objets blob et aux files d’attente](storage-auth-aad-app.md)
- [Support de stockage Azure pour Azure Active Directory en fonction de contrôle d’accès à la disposition générale](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
