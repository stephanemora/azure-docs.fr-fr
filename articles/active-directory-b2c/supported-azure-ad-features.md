---
title: Fonctionnalités Azure AD prises en charge
description: Découvrez les fonctionnalités Azure AD qui sont encore prises en charge dans Azure AD B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/08/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 99f6d58af1a8508b6e3582b4ec0139081a259046
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042994"
---
# <a name="supported-azure-ad-features"></a>Fonctionnalités Azure AD prises en charge

Un locataire Azure AD B2C n’est pas un locataire Azure Active Directory, dont vous disposez peut-être déjà, mais il s’appuie sur ce dernier. Les fonctionnalités Azure AD suivantes peuvent être utilisées dans votre locataire Azure AD B2C.

|Fonctionnalité  |Azure AD  | Azure AD B2C |
|---------|---------|---------|
| [Groupes](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) | Les groupes peuvent permettre de gérer les comptes d’utilisateur et d’administration.| Les groupes peuvent permettre de gérer les comptes d’administration. Des [comptes consommateur](user-overview.md#consumer-user) ne peuvent pas être membres d’un groupe. |
| [Invitation d’invités External Identities](../active-directory//external-identities/add-users-administrator.md)| Vous pouvez inviter des utilisateurs invités et configurer des fonctionnalités External Identities, telles que la fédération et la connexion avec des comptes Facebook et Google. | Vous ne pouvez inviter qu’un compte Microsoft ou un utilisateur Azure AD en tant qu’invité à votre locataire Azure AD pour accéder à des applications ou gérer des locataires. Pour les [comptes consommateur](user-overview.md#consumer-user), vous utilisez des flux d’utilisateurs et des stratégies personnalisées Azure AD B2C pour gérer des utilisateurs et vous inscrire ou vous connecter avec des fournisseurs d’identités externes, tels que Google ou Facebook. |
| [Rôles et administrateurs](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)| Entièrement pris en charge pour les comptes d’utilisateur et d’administration. | Les rôles ne sont pas pris en charge avec les [comptes consommateur](user-overview.md#consumer-user). Les comptes consommateur n’ont accès à aucune ressource Azure.|
| [Noms de domaine personnalisés](../active-directory/fundamentals/add-custom-domain.md) |  Vous pouvez utiliser des domaines personnalisés Azure AD pour les comptes d’administration uniquement. | Les [comptes consommateur](user-overview.md#consumer-user) peuvent se connecter à l’aide d’un nom d’utilisateur, d’un numéro de téléphone ou d’une adresse e-mail. Vous pouvez utiliser des [domaines personnalisés](custom-domain.md) dans vos URL de redirection.|
| [Accès conditionnel](../active-directory/conditional-access/overview.md) | Entièrement pris en charge pour les comptes d’utilisateur et d’administration. | Un sous-ensemble des fonctionnalités d’accès conditionnel Azure AD est pris en charge avec les [comptes consommateur](user-overview.md#consumer-user) Découvrez la procédure de configuration de l’[accès conditionnel](conditional-access-user-flow.md) Azure AD B2C.|
| [Premium P1](https://azure.microsoft.com/pricing/details/active-directory) | Entièrement pris en charge pour les fonctionnalités d’Azure AD Premium P1. Par exemple, [Protection par mot de passe](../active-directory/authentication/concept-password-ban-bad.md), [Identités hybrides](../active-directory/hybrid/whatis-hybrid-identity.md), [Accès conditionnel](../active-directory/roles/permissions-reference.md#), [Groupes dynamiques](../active-directory/enterprise-users/groups-create-rule.md), etc. | Un sous-ensemble de fonctionnalités d’accès conditionnel d’Azure AD sont prises en charge avec des [comptes consommateur](user-overview.md#consumer-user). Découvrez comment configurer un [accès conditionnel](conditional-access-user-flow.md) Azure AD B2C.|
| [Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) | Entièrement pris en charge pour les fonctionnalités d’Azure AD Premium P2. Par exemple, [Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) et [Identity Governance](../active-directory/governance/identity-governance-overview.md).  | Un sous-ensemble de fonctionnalités d’Azure AD Identity Protection sont prises en charge avec des [comptes de consommateurs](user-overview.md#consumer-user). Découvrez comment [Investiguer les risques avec Identity Protection](identity-protection-investigate-risk.md) et configurer un [accès conditionnel](conditional-access-user-flow.md) Azure AD B2C. |

> [!NOTE]
> **Autres ressources Azure dans votre locataire :** <br>Dans un locataire Azure AD B2C, vous ne pouvez pas approvisionner d’autres ressources Azure, telles que des machines virtuelles, des applications web Azure ou des fonctions Azure. Vous devez créer ces ressources dans votre locataire Azure AD.