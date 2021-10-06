---
title: Autoriser l’accès aux tables avec Active Directory (préversion)
titleSuffix: Azure Storage
description: Autorisez l’accès aux tables Azure avec Azure Active Directory (Azure AD) (préversion). Attribuez des rôles Azure pour les droits d’accès. Accédez aux données avec un compte Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3f0c18d7f9130c4ef50b085a4f8c1ec5c22ed6d3
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360390"
---
# <a name="authorize-access-to-tables-using-azure-active-directory-preview"></a>Autoriser l’accès aux tables avec Azure Active Directory (préversion)

Le Stockage Azure prend en charge l’utilisation d’Azure Active Directory (Azure AD) pour autoriser les requêtes aux données de table (préversion). Avec Azure AD, vous pouvez utiliser le contrôle d’accès en fonction du rôle d’Azure (Azure RBAC) pour accorder des autorisations à un principal de sécurité, qui peut être un utilisateur, un groupe ou un principal de service d’application. Le principal de sécurité est authentifié par Azure AD pour retourner un jeton OAuth 2.0. Le jeton peut ensuite être utilisé pour autoriser une requête auprès du service Table.

Autoriser les demandes auprès du Stockage Azure avec Azure AD offre davantage de sécurité et de facilité d’utilisation sur l’autorisation de clé partagée. Microsoft recommande d’utiliser, dans la mesure du possible, l’autorisation Azure AD avec vos applications de table pour garantir l’accès avec les privilèges minimaux exigés.

Avec Azure AD, l’autorisation est disponible pour tous les comptes à usage général dans la totalité des régions publiques et clouds nationaux. Seuls les comptes de stockage créés avec le modèle de déploiement Azure Resource Manager prennent en charge l’autorisation Azure AD.

> [!IMPORTANT]
> Avec Azure AD, l’autorisation pour les tables est actuellement en **PRÉVERSION**. Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview-of-azure-ad-for-tables"></a>Vue d’ensemble d’Azure AD pour les tables

Quand un principal de sécurité (utilisateur, groupe ou application) tente d’accéder à une ressource de table, la requête doit être autorisée. Avec Azure AD, l’accès à une ressource est un processus en deux étapes. Pour commencer, l’identité du principal de sécurité est authentifiée, et un jeton OAuth 2.0 est renvoyé. Ensuite, ce jeton est passé dans le cadre d’une requête adressée au service Table et utilisé par le service pour autoriser l’accès à la ressource spécifiée.

L’étape d’authentification nécessite qu’une application demande un jeton d’accès OAuth 2.0 au moment de l’exécution. Si une application s’exécute à partir d’une entité Azure comme une machine virtuelle Azure, un groupe de machines virtuelles identiques ou une application Azure Functions, elle peut utiliser une [identité managée](../../active-directory/managed-identities-azure-resources/overview.md) pour accéder aux tables. Pour savoir comment autoriser des requêtes adressées par une identité managée, consultez [Autoriser l’accès aux tables avec Azure Active Directory et les identités managées pour les ressources Azure](../common/storage-auth-aad-msi.md).

L’étape d’autorisation exige qu’un ou plusieurs rôles Azure soient attribués au principal de sécurité. Le Stockage Azure fournit des rôles Azure qui englobent des ensembles communs d’autorisations pour les données de table. Les rôles qui sont attribués à un principal de sécurité déterminent les autorisations dont disposera le principal. Pour en savoir plus sur l’attribution de rôles Azure pour l’accès aux tables, consultez [Attribuer un rôle Azure pour l’accès aux données de table](assign-azure-role-data-access.md).

Les applications natives et les applications web qui adressent des requêtes au service Table Azure peuvent également autoriser l’accès avec Azure AD. Pour savoir comment demander un jeton d’accès et l’utiliser pour autoriser les requêtes, consultez [Autoriser l’accès au Stockage Azure avec Azure AD à partir d’une application de Stockage Azure](../common/storage-auth-aad-app.md).

## <a name="assign-azure-roles-for-access-rights"></a>Attribuer des rôles Azure pour les droits d’accès

Azure Active Directory (Azure AD) autorise les droits d’accès aux ressources sécurisées par le biais du [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../../role-based-access-control/overview.md). Le Stockage Azure définit un ensemble de rôles intégrés Azure qui englobent les jeux d’autorisations courants permettant d’accéder aux données de table. Vous pouvez également définir des rôles personnalisés pour l’accès aux données de table.

Lorsqu’un rôle Azure est attribué à un principal de sécurité Azure AD, Azure octroie l’accès à ces ressources pour ce principal de sécurité. Un principal de sécurité Azure AD peut correspondre à un utilisateur, à un groupe, à un principal de service d’application ou à une [identité managée pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="resource-scope"></a>Étendue des ressources

Avant d’attribuer un rôle RBAC Azure à un principal de sécurité, déterminez l’étendue de l’accès dont doit disposer le principal de sécurité. Selon les bonnes pratiques, il est toujours préférable d’accorder la plus petite étendue possible. Les rôles RBAC Azure définis au niveau d’une étendue plus large sont hérités par les ressources qui sont sous eux.

Vous pouvez étendre l’accès aux ressources de table Azure aux niveaux suivants, en commençant par l’étendue la plus restreinte :

- **Une table individuelle.** Dans cette étendue, une attribution de rôle s’applique à la table spécifiée.
- **Le compte de stockage.** Dans cette étendue, une attribution de rôle s’applique à toutes les tables dans le compte.
- **Le groupe de ressources.** Dans cette étendue, une attribution de rôle s’applique à toutes les tables dans tous les comptes de stockage du groupe de ressources.
- **L’abonnement.** Dans cette étendue, une attribution de rôle s’applique à toutes les tables dans tous les comptes de stockage de tous les groupes de ressources de l’abonnement.
- **Un groupe d’administration.** Dans cette étendue, une attribution de rôle s’applique à toutes les tables dans tous les comptes de stockage de tous les groupes de ressources de tous les abonnements du groupe d’administration.

Pour plus d’informations sur l’étendue des attributions de rôle RBAC Azure, consultez [Comprendre l’étendue de RBAC Azure](../../role-based-access-control/scope-overview.md).

### <a name="azure-built-in-roles-for-tables"></a>Rôles intégrés Azure pour les tables

Le contrôle d’accès en fonction du rôle (RBAC) Azure fournit des rôles intégrés pour autoriser l’accès aux données de table avec Azure AD et OAuth. Les rôles qui fournissent des autorisations aux tables dans le Stockage Azure sont notamment les suivants :

- [Contributeur aux données de la table de stockage](../../role-based-access-control/built-in-roles.md#storage-table-data-contributor) : permet d’accorder des autorisations en lecture/écriture/suppression sur les ressources de stockage Table.
- [Lecteur des données de la table de stockage](../../role-based-access-control/built-in-roles.md#storage-table-data-reader) : permet d’accorder des autorisations en lecture seule sur les ressources de stockage Table.

Pour savoir comment attribuer un rôle intégré Azure à un principal de sécurité, consultez [Attribuer un rôle Azure pour l’accès aux données de table](assign-azure-role-data-access.md). Pour apprendre à lister les rôles RBAC Azure et leurs autorisations, consultez [Lister les définitions de rôles Azure](../../role-based-access-control/role-definitions-list.md).

Pour plus d’informations sur la définition des rôles intégrés pour le Stockage Azure, consultez [Comprendre les définitions de rôles](../../role-based-access-control/role-definitions.md#control-and-data-actions). Pour plus d’informations sur la création de rôles personnalisés Azure, consultez [Rôles personnalisés Azure](../../role-based-access-control/custom-roles.md).

Seuls les rôles explicitement définis pour l’accès aux données permettent à un principal de sécurité d’accéder aux données de table. Des rôles intégrés comme **Propriétaire**, **Contributeur** et **Contributeur de comptes de stockage** permettent à un principal de sécurité de gérer un compte de stockage, mais n’accordent pas l’accès aux données de table dans ce compte par le biais d’Azure AD. Toutefois, si un rôle comprend **Microsoft.Storage/storageAccounts/listKeys/action**, un utilisateur auquel ce rôle est affecté peut accéder aux données du compte de stockage via l’autorisation de la clé partagée avec les clés d’accès au compte.

Pour des informations détaillées sur les rôles intégrés Azure pour le Stockage Azure pour les services de données et de gestion, consultez la section **Stockage** dans [Rôles intégrés Azure pour Azure RBAC](../../role-based-access-control/built-in-roles.md#storage). Pour plus d’informations sur les différents types de rôles qui fournissent des autorisations dans Azure, consultez [Rôles d’administrateur d’abonnement classique, rôles Azure et rôles Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> Les attributions de rôles Azure peuvent prendre jusqu’à 30 minutes pour se propager.

### <a name="access-permissions-for-data-operations"></a>Autorisations d’accès pour les opérations de données

Pour plus d’informations sur les autorisations nécessaires pour appeler des opérations propres au service Table, consultez [Autorisations pour appeler des opérations de données](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-data-operations).

## <a name="next-steps"></a>Étapes suivantes

- [Autoriser l’accès aux données dans le Stockage Azure](../common/authorize-data-access.md)
- [Attribuer un rôle Azure pour l’accès aux données de table](assign-azure-role-data-access.md)