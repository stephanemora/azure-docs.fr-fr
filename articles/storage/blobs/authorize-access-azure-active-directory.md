---
title: Autoriser l’accès aux blobs avec Active Directory
titleSuffix: Azure Storage
description: Autorisez l’accès aux blobs Azure à l’aide d’Azure Active Directory (Azure AD). Attribuez des rôles Azure pour les droits d’accès. Accédez aux données avec un compte Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: fe6397b79ff37074342b40880aa5284e020cd8a1
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859185"
---
# <a name="authorize-access-to-blobs-using-azure-active-directory"></a>Autoriser l’accès aux blobs avec Azure Active Directory

Le Stockage Azure prend en charge l’utilisation d’Azure Active Directory (Azure AD) pour autoriser les requêtes au blob de données. Avec Azure AD, vous pouvez utiliser le contrôle d’accès en fonction du rôle d’Azure (Azure RBAC) pour accorder des autorisations à un principal de sécurité, qui peut être un utilisateur, un groupe ou un principal de service d’application. Le principal de sécurité est authentifié par Azure AD pour retourner un jeton OAuth 2.0. Le jeton peut ensuite être utilisé pour autoriser une requête auprès du service BLOB.

Autoriser les demandes auprès du Stockage Azure avec Azure AD offre davantage de sécurité et de facilité d’utilisation sur l’autorisation de clé partagée. Microsoft recommande d’utiliser l’autorisation Azure AD avec vos applications blob dans la mesure du possible pour garantir l’accès avec les privilèges minimaux requis.

L’autorisation avec Azure AD est disponible pour tous les comptes de stockage universels et comptes de stockage Blob, dans la totalité des régions publiques et clouds nationaux. Seuls les comptes de stockage créés avec le modèle de déploiement Azure Resource Manager prennent en charge l’autorisation Azure AD.

Le stockage Blob prend également en charge la création de signatures d’accès partagé (SAS) signées avec des informations d’identification Azure AD. Pour plus d’informations, consultez [Accorder un accès limité aux données avec des signatures d’accès partagé](../common/storage-sas-overview.md).

## <a name="overview-of-azure-ad-for-blobs"></a>Présentation d’Azure AD pour les blobs

Lorsqu’un principal de sécurité (utilisateur, groupe ou application) tente d’accéder à une ressource blob, la requête doit être autorisée, sauf s’il s’agit d’un blob disponible pour l’accès anonyme. Avec Azure AD, l’accès à une ressource est un processus en deux étapes. Pour commencer, l’identité du principal de sécurité est authentifiée, et un jeton OAuth 2.0 est renvoyé. Ensuite, ce jeton est transmis dans le cadre d’une requête adressée au service BLOB et utilisé par le service pour autoriser l’accès à la ressource spécifiée.

L’étape d’authentification nécessite qu’une application demande un jeton d’accès OAuth 2.0 au moment de l’exécution. Si une application s’exécute à partir d’une entité Azure telle qu’une machine virtuelle Azure, un groupe de machines virtuelles identiques ou une application Azure Functions, elle peut utiliser une [identité managée](../../active-directory/managed-identities-azure-resources/overview.md) pour accéder aux données blob. Pour savoir comment autoriser des requêtes adressées au service Azure Blob par une identité managée, consultez [Autoriser l’accès aux données de blob avec des identités managées pour les ressources Azure](authorize-managed-identity.md).

L’étape d’autorisation exige qu’un ou plusieurs rôles Azure soient attribués au principal de sécurité. Le Stockage Azure fournit des rôles Azure qui englobent des ensembles communs d’autorisations pour les données blob. Les rôles qui sont attribués à un principal de sécurité déterminent les autorisations dont disposera le principal. Pour en savoir plus sur l’attribution de rôles Azure pour l’accès au blob, consultez [Attribuer un rôle Azure pour l’accès aux données blob](../blobs/assign-azure-role-data-access.md).

Les applications natives et les applications web qui adressent des requêtes au service Azure Blob peuvent également autoriser l’accès avec Azure AD. Pour savoir comment demander un jeton d’accès et l’utiliser pour autoriser les requêtes pour les données blob, consultez [Autoriser l’accès au stockage Azure avec Azure AD à partir d’une application de stockage Azure](../common/storage-auth-aad-app.md).

## <a name="assign-azure-roles-for-access-rights"></a>Attribuer des rôles Azure pour les droits d’accès

Azure Active Directory (Azure AD) autorise les droits d’accès aux ressources sécurisées par le biais du [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../../role-based-access-control/overview.md). Le stockage Azure définit un ensemble de rôles intégrés Azure qui englobent les ensembles communs d’autorisations permettant d’accéder aux données blob. Vous pouvez également définir des rôles personnalisés pour l’accès aux données blob.

Lorsqu’un rôle Azure est attribué à un principal de sécurité Azure AD, Azure octroie l’accès à ces ressources pour ce principal de sécurité. Un principal de sécurité Azure AD peut correspondre à un utilisateur, à un groupe, à un principal de service d’application ou à une [identité managée pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="resource-scope"></a>Étendue des ressources

Avant d’attribuer un rôle RBAC Azure à un principal de sécurité, déterminez l’étendue de l’accès dont doit disposer le principal de sécurité. Selon les bonnes pratiques, il est toujours préférable d’accorder la plus petite étendue possible. Les rôles RBAC Azure définis au niveau d’une étendue plus large sont hérités par les ressources qui sont sous eux.

Vous pouvez étendre l’accès aux ressources blob Azure aux niveaux suivants, en commençant par l’étendue la plus étroite :

- **Un conteneur individuel.** Dans cette étendue, une attribution de rôle s’applique à tous les objets blob dans le conteneur, ainsi qu’aux propriétés et aux métadonnées du conteneur.
- **Le compte de stockage.** Dans cette étendue, une attribution de rôle s’applique à tous les conteneurs et à leurs blobs.
- **Le groupe de ressources.** Dans cette étendue, une attribution de rôle s’applique à tous les conteneurs dans tous les comptes de stockage du groupe de ressources.
- **L’abonnement.** Dans cette étendue, une attribution de rôle s’applique à tous les conteneurs dans tous les comptes de stockage de tous les groupes de ressources de l’abonnement.
- **Un groupe d’administration.** Dans cette étendue, une attribution de rôle s’applique à tous les conteneurs dans tous les comptes de stockage de tous les groupes de ressources de tous les abonnements du groupe d’administration.

Pour plus d’informations sur l’étendue des attributions de rôle RBAC Azure, consultez [Comprendre l’étendue de RBAC Azure](../../role-based-access-control/scope-overview.md).

### <a name="azure-built-in-roles-for-blobs"></a>Rôles intégrés Azure pour les blobs

RBAC Azure fournit un certain nombre de rôles intégrés pour autoriser l’accès aux données de blob avec Azure AD et OAuth. Voici quelques exemples de rôles qui fournissent des autorisations aux ressources de données dans Stockage Azure :

- [Propriétaire des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) : permet de définir la propriété et de gérer le contrôle d’accès POSIX pour Azure Data Lake Storage Gen2. Pour plus d’informations, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md).
- [Contributeur aux données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) : permet d’accorder des autorisations en lecture/écriture/suppression aux ressources de stockage d’objets blob.
- [Lecteur des données blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) : permet d’accorder des autorisations en lecture seule aux ressources de stockage d’objets blob.
- [Délégation du Stockage Blob](../../role-based-access-control/built-in-roles.md#storage-blob-delegator) : Obtenez une clé de délégation d’utilisateur pour créer une signature d’accès partagé qui est signée avec les informations d’identification Azure AD pour un conteneur ou un objet blob.

Pour savoir comment attribuer un rôle intégré Azure à un principal de sécurité, consultez [Attribuer un rôle Azure pour l’accès aux données d’objet blob](../blobs/assign-azure-role-data-access.md). Pour apprendre à lister les rôles RBAC Azure et leurs autorisations, consultez [Lister les définitions de rôles Azure](../../role-based-access-control/role-definitions-list.md).

Pour plus d’informations sur la définition des rôles intégrés pour le Stockage Azure, consultez [Comprendre les définitions de rôles](../../role-based-access-control/role-definitions.md#control-and-data-actions). Pour plus d’informations sur la création de rôles personnalisés Azure, consultez [Rôles personnalisés Azure](../../role-based-access-control/custom-roles.md).

Seuls les rôles explicitement définis pour l’accès aux données permettent à un principal de sécurité d’accéder aux données de blob. Des rôles intégrés tels que **Propriétaire**, **Contributeur** et **Contributeur de comptes de stockage** permettent à un principal de sécurité de gérer un compte de stockage, mais n’accordent pas l’accès aux données de blob dans ce compte via Azure AD. Toutefois, si un rôle comprend **Microsoft.Storage/storageAccounts/listKeys/action**, un utilisateur auquel ce rôle est affecté peut accéder aux données du compte de stockage via l’autorisation de la clé partagée avec les clés d’accès au compte. Pour plus d’informations, consultez [Choisir comment autoriser l’accès à des données blobs dans le portail Azure](../../storage/blobs/authorize-data-operations-portal.md).

Pour des informations détaillées sur les rôles intégrés Azure pour le Stockage Azure pour les services de données et de gestion, consultez la section **Stockage** dans [Rôles intégrés Azure pour Azure RBAC](../../role-based-access-control/built-in-roles.md#storage). Pour plus d’informations sur les différents types de rôles qui fournissent des autorisations dans Azure, consultez [Rôles d’administrateur d’abonnement classique, rôles Azure et rôles Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> Les attributions de rôles Azure peuvent prendre jusqu’à 30 minutes pour se propager.

### <a name="access-permissions-for-data-operations"></a>Autorisations d’accès pour les opérations de données

Pour plus d’informations sur les autorisations requises pour l’appel d’opérations propres aux services BLOB, consultez [Autorisations pour l’appel d’opérations de données](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-data-operations).

## <a name="access-data-with-an-azure-ad-account"></a>Accéder aux données avec un compte Azure AD

L’accès aux données blob par le biais du Portail Azure, de PowerShell ou d’Azure CLI peut être autorisé à l’aide du compte Azure AD de l’utilisateur ou au moyen des clés d’accès au compte (autorisation de clé partagée).

### <a name="data-access-from-the-azure-portal"></a>Accès aux données à partir du Portail Azure

Le Portail Azure peut utiliser soit votre compte Azure AD, soit les clés d’accès au compte pour accéder aux données blob dans un compte de stockage Azure. Le schéma d’autorisation utilisé par le portail Azure dépend des rôles Azure qui vous sont attribués.

Lorsque vous tentez d’accéder aux données blob, le Portail Azure commence par vérifier si vous avez reçu un rôle Azure avec **Microsoft.Storage/storageAccounts/listkeys/action**. Si vous avez été doté d’un rôle avec cette action, le Portail Azure utilise la clé de compte pour l’accès aux données blob par le biais de l’autorisation par clé partagée. Si vous n’avez pas reçu un rôle avec cette action, le Portail Azure tente d’accéder aux données à l’aide de votre compte Azure AD.

Pour accéder aux données blob à partir du Portail Azure à l’aide de votre compte Azure AD, vous devez disposer d’autorisations pour accéder aux données blob, ainsi que pour parcourir les ressources de compte de stockage dans le Portail Azure. Les rôles intégrés fournis par le Stockage Azure octroient l’accès aux ressources blob, mais n’accordent pas d’autorisations pour les ressources de compte de stockage. C’est la raison pour laquelle l’accès au portail requiert également l’attribution d’un rôle Azure Resource Manager, tel que le rôle [Lecteur](../../role-based-access-control/built-in-roles.md#reader), étendu au niveau du compte de stockage ou à un niveau supérieur. Le rôle **Lecteur** octroie les autorisations les plus restreintes, mais l’utilisation d’un autre rôle Azure Resource Manager accordant l’accès aux ressources de gestion de compte de stockage est également acceptable. Pour en savoir plus sur l’attribution d’autorisations à des utilisateurs pour l’accès aux données dans le Portail Azure avec un compte Azure AD, consultez [Attribuer un rôle Azure pour l’accès aux données Blob](../blobs/assign-azure-role-data-access.md).

Le Portail Azure indique le schéma d’autorisation en cours d’utilisation lorsque vous accédez à un conteneur. Pour plus d’informations sur l’accès aux données dans le portail, consultez [Choisir comment autoriser l’accès à des données blobs dans le Portail Azure](../blobs/authorize-data-operations-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Accès aux données à partir de PowerShell ou d’Azure CLI

Azure CLI et PowerShell prennent en charge la connexion avec des informations d’identification Azure AD. Une fois que vous vous êtes connecté, votre session s’exécute sous ces informations d’identification. Pour plus d’informations, consultez l’un des articles suivants :

- [Choisir comment autoriser l’accès à des données blob avec Azure CLI](authorize-data-operations-cli.md)
- [Exécuter des commandes PowerShell avec des informations d’identification Azure AD pour accéder aux données d’objet blob](authorize-data-operations-powershell.md)

## <a name="feature-support"></a>Prise en charge des fonctionnalités

Ce tableau montre comment cette fonctionnalité est prise en charge dans votre compte ainsi que l’impact sur la prise en charge lorsque vous activez certaines fonctionnalités.

| Type de compte de stockage                | Stockage Blob (prise en charge par défaut)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| Usage général v2 Standard | ![Oui](../media/icons/yes-icon.png) |![Oui](../media/icons/yes-icon.png)              | ![Non](../media/icons/no-icon.png) |
| Objets blob de blocs Premium          | ![Oui](../media/icons/yes-icon.png) | ![Oui](../media/icons/yes-icon.png)| ![Non](../media/icons/no-icon.png) |

<sup>1</sup>    Data Lake Storage Gen2 et le protocole NFS (Network File System) 3.0 requièrent tous deux un compte de stockage avec un espace de noms hiérarchique activé.

## <a name="next-steps"></a>Étapes suivantes

- [Autoriser l’accès aux données dans le stockage Azure](../common/authorize-data-access.md)
- [Attribuer un rôle Azure pour l’accès aux données d’objet blob](assign-azure-role-data-access.md)
