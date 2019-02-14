---
title: Azure AD Identity Governance| Microsoft Docs
description: Azure AD Identity Governance vous permet de bénéficier de la visibilité et des processus appropriés pour répondre aux besoins de votre organisation en termes de sécurité et de productivité des employés.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 304399905e9dfd37557a342248c829c1f940452b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167576"
---
# <a name="what-is-azure-ad-identity-governance"></a>Qu’est-ce qu’Azure AD Identity Governance ?

Azure Active Directory (Azure AD) Identity Governance vous permet de bénéficier de la visibilité et des processus appropriés pour répondre aux besoins de votre organisation en termes de sécurité et de productivité des employés. Ce service vous offre les fonctionnalités requises pour vous assurer que les utilisateurs appropriés bénéficient d’un accès approprié aux ressources appropriées. Il vous permet également de protéger, surveiller et auditer l’accès aux ressources critiques, tout en garantissant la productivité des employés.  

Identity Governance offre aux organisations la possibilité d’effectuer les opérations suivantes vis-à-vis des employés, des fournisseurs et partenaires commerciaux et des services et applications :

- Gouverner le cycle de vie des identités
- Gouverner le cycle de vie des accès
- Sécuriser l’administration

Plus précisément, ce service vise à aider les organisations à répondre à ces quatre questions clés :

- Quels utilisateurs doivent pouvoir accéder à quelles ressources ?
- Que font les utilisateurs de cet accès ?
- Des contrôles organisationnels efficaces sont-ils disponibles pour gérer l’accès ?
- Des auditeurs peuvent-ils vérifier que les contrôles fonctionnent ?

## <a name="identity-lifecycle"></a>Cycle de vie des identités

Identity Governance aide les organisations à trouver un équilibre entre *productivité* (vitesse avec laquelle des personnes peuvent accéder aux ressources dont elles ont besoin, par exemple pour joindre mon organisation) et *sécurité* (comment leur accès doit évoluer au fil du temps, par exemple à la suite de modifications de leur statut professionnel).  La gestion du cycle de vie des identités constitue le fondement de la gouvernance des identités. Une gouvernance efficace à grande échelle implique la modernisation de l’infrastructure de gestion du cycle de vie des identités pour les applications.

Pour de nombreuses organisations, le cycle de vie des identités pour les employés est lié à la représentation de l’utilisateur concerné dans un système de GCH (gestion du capital humain).  Azure AD Premium gère automatiquement les identités des utilisateurs pour les personnes représentées dans Workday, et ce, dans Active Directory et Azure Active Directory, comme décrit dans le didacticiel [Workday inbound provisioning (preview)](../saas-apps/workday-inbound-tutorial.md) (provisionnement entrant Workday (préversion)).  Azure AD Premium inclut également [Microsoft Identity Manager](/microsoft-identity-manager/), qui peut importer des enregistrements à partir de systèmes de GCH locaux tels que SAP, Oracle eBusiness et Oracle PeopleSoft.

De plus en plus de scénarios impliquent une collaboration avec des personnes extérieures à votre organisation. La collaboration [Azure AD B2B](/azure/active-directory/b2b/) permet de partager en toute sécurité les applications et services de votre organisation avec des utilisateurs invités et des partenaires externes à partir de n’importe quelle organisation, tout en conservant le contrôle sur vos propres données d’entreprise.

## <a name="access-lifecycle"></a>Cycle de vie des accès

Les organisations ont besoin d’un processus pour gérer l’accès au-delà de ce qui a été initialement configuré pour un utilisateur lors de la création de son identité.  En outre, les entreprises doivent pouvoir procéder efficacement à une mise à l’échelle pour être en mesure de développer et d’appliquer des contrôles et une stratégie d’accès de manière continue.

En règle générale, le service informatique délègue aux décideurs d’entreprise la prise de décision en matière d’approbation d’accès.  De plus, le service informatique peut impliquer les utilisateurs eux-mêmes.  Par exemple, les utilisateurs accédant à des données client confidentielles dans l’application marketing d’une société en Europe doivent connaître les stratégies de l’entreprise. Les utilisateurs invités d’une organisation n’ont peut-être pas connaissance de ses exigences en matière de traitement des données.

Les organisations peuvent automatiser le processus de cycle de vie des accès à l’aide de technologies comme les [groupes dynamiques](../users-groups-roles/groups-dynamic-membership.md), moyennant l’approvisionnement des utilisateurs sur des [applications SaaS](../saas-apps/tutorial-list.md) ou des [applications intégrées à SCIM](../manage-apps/use-scim-to-provision-users-and-groups.md).  Les organisations peuvent également contrôler les [utilisateurs invités pouvant accéder aux applications locales](../b2b/hybrid-cloud-to-on-premises.md).  Ces droits d’accès peuvent ensuite être révisés régulièrement par le biais de [révisions d’accès Azure AD](access-reviews-overview.md) récurrentes.

Lorsqu’un utilisateur tente d’accéder à des applications, Azure AD applique des stratégies [d’accès conditionnel](/azure/active-directory/conditional-access/). Par exemple, les stratégies d’accès conditionnel peuvent inclure l’affichage de [conditions d’utilisation](active-directory-tou.md) et un processus [garantissant que l’utilisateur a accepté ces conditions](../conditional-access/require-tou.md) avant de lui permettre d’accéder à une application.

## <a name="privileged-access-lifecycle"></a>Cycle de vie des accès privilégiés

Les autres fournisseurs ont toujours décrit l’accès privilégié comme une fonctionnalité distincte de la gouvernance des identités. Toutefois, chez Microsoft, nous pensons que la gouvernance de l’accès privilégié est une composante essentielle de la gouvernance des identités, notamment en raison du risque d’utilisation inappropriée pesant sur les organisations, inhérent à ces droits d’administrateur. Les employés, fournisseurs et sous-traitants qui bénéficient de droits d’administration doivent être gouvernés.

Azure AD Privileged Identity Management (PIM) offre des contrôles supplémentaires conçus pour sécuriser les droits d’accès aux ressources dans Azure AD, Azure et d’autres services en ligne Microsoft.  L’accès juste-à-temps et les fonctionnalités d’alerte de changement de rôle fournis par Azure AD PIM, en plus de l’authentification multifacteur et de l’accès conditionnel, offrent un ensemble complet de contrôles de gouvernance contribuant à sécuriser les ressources de votre société (annuaire, Office 365 et rôles de ressources Azure). Comme avec d’autres formes d’accès, les organisations peuvent utiliser les révisions d’accès pour configurer une recertification récurrente des accès pour tous les utilisateurs bénéficiant de rôles d’administrateur.

## <a name="getting-started"></a>Prise en main

Il n’existe pas de solution ou recommandation parfaite pour chaque client. Cependant, vous pouvez vous appuyer sur les configurations suivantes afin d’appréhender les stratégies de base recommandées par Microsoft pour renforcer la sécurité et la productivité de la force de travail.

- [Configurations des identités et de l’accès aux appareils](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Sécurisation de l’accès privilégié](../users-groups-roles/directory-admin-roles-secure.md)


### <a name="access-reviews"></a>Révisions d’accès

- [Qu’est-ce qu’une révision d’accès ?](access-reviews-overview.md)
- [Gérer l’accès des utilisateurs avec les révisions d’accès](manage-user-access-with-access-reviews.md)
- [Gérer l’accès des invités avec les révisions d’accès](manage-guest-access-with-access-reviews.md)
- [Démarrer une révision d’accès des rôles d’annuaire Azure AD dans PIM](../privileged-identity-management/pim-how-to-start-security-review.md)

### <a name="terms-of-use"></a>Conditions d’utilisation

- [Que puis-je faire avec Conditions d’utilisation ?](active-directory-tou.md)

### <a name="privileged-identity-management"></a>Privileged Identity Management

- [Qu’est-ce qu’Azure AD B2C ?](../privileged-identity-management/pim-configure.md)
