---
title: Identity Governance - Azure Active Directory | Microsoft Docs
description: Azure Active Directory Identity Governance vous permet de bénéficier de la visibilité et des processus appropriés pour répondre aux besoins de votre organisation en termes de sécurité et de productivité des employés.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcb7ab566d861e0c4256acda8f8facd1e94d4ad6
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87796775"
---
# <a name="what-is-azure-ad-identity-governance"></a>Qu’est-ce qu’Azure AD Identity Governance ?

Azure Active Directory (Azure AD) Identity Governance vous permet de bénéficier de la visibilité et des processus appropriés pour répondre aux besoins de votre organisation en termes de sécurité et de productivité des employés. Il offre des fonctionnalités permettant de faire en sorte que chacun dispose de droits d’accès aux ressources adaptés. Ces fonctionnalités et celles d’Azure AD et d’Enterprise Mobility + Security permettent de limiter les risques d’accès en protégeant, en surveillant et en auditant l’accès aux ressources critiques, tout en garantissant la productivité des employés et des partenaires.  

Identity Governance offre aux organisations la possibilité d’effectuer les tâches suivantes vis-à-vis des employés, des partenaires et des fournisseurs, pour différents services et applications en local comme dans le cloud :

- Gouverner le cycle de vie des identités
- Gouverner le cycle de vie des accès
- Sécuriser l’accès privilégié pour l’administration

Plus précisément, ce service vise à aider les organisations à répondre à ces quatre questions clés :

- Quels utilisateurs doivent pouvoir accéder à quelles ressources ?
- Que font les utilisateurs de cet accès ?
- Des contrôles organisationnels efficaces sont-ils disponibles pour gérer l’accès ?
- Des auditeurs peuvent-ils vérifier que les contrôles fonctionnent ?

## <a name="identity-lifecycle"></a>Cycle de vie des identités

Identity Governance aide les organisations à trouver un équilibre entre *productivité* (vitesse à laquelle des personnes peuvent accéder aux ressources dont elles ont besoin, par exemple pour joindre mon organisation) et *sécurité* (comment leur accès doit évoluer au fil du temps, par exemple à la suite de modifications de leur statut professionnel).  La gestion du cycle de vie des identités constitue le fondement d’Identity Governance. Une gouvernance efficace à grande échelle implique la modernisation de l’infrastructure de gestion du cycle de vie des identités pour les applications.

![Cycle de vie des identités](./media/identity-governance-overview/identity-lifecycle.png)

Pour de nombreuses organisations, le cycle de vie des identités pour les employés est lié à la représentation de l’utilisateur concerné dans un système de GCH (gestion du capital humain).  Azure AD Premium gère automatiquement l’identité des utilisateurs représentés dans Workday, à la fois dans Active Directory et dans Azure Active Directory, comme le décrit le tutoriel [Provisionnement entrant Workday](../saas-apps/workday-inbound-tutorial.md).  Azure AD Premium inclut également [Microsoft Identity Manager](/microsoft-identity-manager/), qui peut importer des enregistrements à partir de systèmes de GCH locaux tels que SAP, Oracle eBusiness et Oracle PeopleSoft.

De plus en plus de scénarios impliquent une collaboration avec des personnes extérieures à votre organisation. La collaboration [Azure AD B2B](/azure/active-directory/b2b/) permet de partager en toute sécurité les applications et services de votre organisation avec des utilisateurs invités et des partenaires externes à partir de n’importe quelle organisation, tout en conservant le contrôle sur vos propres données d’entreprise.  La [gestion des droits d’utilisation Azure AD](entitlement-management-overview.md) permet de sélectionner les utilisateurs de l’organisation autorisés à demander l’accès et à être ajoutés en tant qu’invités B2B à l’annuaire de l’organisation, et garantit que ces invités sont supprimés lorsqu’ils n’ont plus besoin de l’accès.

## <a name="access-lifecycle"></a>Cycle de vie des accès

Les organisations ont besoin d’un processus pour gérer l’accès au-delà de ce qui a été initialement configuré pour un utilisateur lors de la création de son identité.  En outre, les entreprises doivent pouvoir procéder efficacement à une mise à l’échelle pour être en mesure de développer et d’appliquer des contrôles et une stratégie d’accès de manière continue.

![Cycle de vie des accès](./media/identity-governance-overview/access-lifecycle.png)

En règle générale, le service informatique délègue aux décideurs d’entreprise la prise de décision en matière d’approbation d’accès.  De plus, le service informatique peut impliquer les utilisateurs eux-mêmes.  Par exemple, les utilisateurs accédant à des données client confidentielles dans l’application marketing d’une société en Europe doivent connaître les stratégies de l’entreprise. Les utilisateurs invités d’une organisation n’ont peut-être pas connaissance de ses exigences en matière de traitement des données.

Les organisations peuvent automatiser le processus de cycle de vie des accès à l’aide de technologies comme les [groupes dynamiques](../users-groups-roles/groups-dynamic-membership.md), moyennant l’approvisionnement des utilisateurs sur des [applications SaaS](../saas-apps/tutorial-list.md) ou des [applications intégrées à SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md).  Les organisations peuvent également contrôler les [utilisateurs invités pouvant accéder aux applications locales](../b2b/hybrid-cloud-to-on-premises.md).  Ces droits d’accès peuvent ensuite être révisés régulièrement par le biais de [révisions d’accès Azure AD](access-reviews-overview.md) récurrentes.   La [gestion des droits d’utilisation Azure AD](entitlement-management-overview.md) permet également de définir la manière dont les utilisateurs demandent l’accès pour différents packages d’appartenance aux groupes et aux équipes, rôles d’application et rôles SharePoint Online.

Lorsqu’un utilisateur tente d’accéder à des applications, Azure AD applique des stratégies [d’accès conditionnel](/azure/active-directory/conditional-access/). Par exemple, les stratégies d’accès conditionnel peuvent inclure l’affichage de [conditions d’utilisation](../conditional-access/terms-of-use.md) et un processus [garantissant que l’utilisateur a accepté ces conditions](../conditional-access/require-tou.md) avant de lui permettre d’accéder à une application.

## <a name="privileged-access-lifecycle"></a>Cycle de vie des accès privilégiés

Les autres fournisseurs ont toujours décrit l’accès privilégié comme une fonctionnalité distincte d’Identity Governance. Toutefois, chez Microsoft, nous pensons que la gouvernance de l’accès privilégié est une composante essentielle d’Identity Governance, notamment en raison du risque d’utilisation inappropriée pesant sur les organisations, inhérent à ces droits d’administrateur. Les employés, fournisseurs et sous-traitants qui bénéficient de droits d’administration doivent être gouvernés.

![Cycle de vie des accès privilégiés](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) offre des contrôles supplémentaires conçus pour sécuriser les droits d’accès aux ressources dans Azure AD, Azure et d’autres services Microsoft Online Services.  L’accès juste-à-temps et les fonctionnalités d’alerte de changement de rôle fournis par Azure AD PIM, en plus de l’authentification multifacteur et de l’accès conditionnel, offrent un ensemble complet de contrôles de gouvernance contribuant à sécuriser les ressources de votre société (annuaire, Office 365 et rôles de ressources Azure). Comme avec d’autres formes d’accès, les organisations peuvent utiliser les révisions d’accès pour configurer une recertification récurrente des accès pour tous les utilisateurs bénéficiant de rôles d’administrateur.

## <a name="getting-started"></a>Prise en main

Pour commencer à utiliser la gestion des droits d’utilisation, les révisions d’accès, Privileged Identity Management et les conditions d’utilisation, voir l’onglet Bien démarrer **d’Identity Governance** sur le Portail Azure.

![Prise en main d’Identity Governance](./media/identity-governance-overview/getting-started.png)


Si vous avez des commentaires sur les fonctionnalités de gouvernance des identités, cliquez sur **Vous avez des commentaires ?** dans le portail Azure pour les envoyer. L’équipe les examine régulièrement.

Il n’existe pas de solution ou de recommandation parfaite pour tous les clients. Cependant, vous pouvez vous appuyer sur les guides de configuration suivants afin d’appréhender les stratégies de base recommandées par Microsoft pour renforcer la sécurité et la productivité des employés.

- [Configurations des identités et de l’accès aux appareils](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Sécurisation de l’accès privilégié](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Annexe : rôles les moins privilégiés pour la gestion dans les fonctionnalités Identity Governance

Il est recommandé d’utiliser le rôle le moins privilégié pour effectuer des tâches d’administration dans Identity Governance. Nous vous recommandons d’utiliser Azure AD PIM pour activer un rôle en fonction des besoins afin d’effectuer ces tâches. Voici les rôles d’annuaire les moins privilégiés pour configurer les fonctionnalités d’Identity Governance :

| Fonctionnalité | Rôle moins privilégié |
| ------- | --------------------- |
| Gestion des droits d’utilisation | Administrateur d’utilisateurs (à l’exception de l’ajout de sites SharePoint Online aux catalogues, qui nécessite un administrateur général) |
| Révisions d’accès | Administrateur d’utilisateurs (à l’exception des révisions d’accès des rôles Azure ou Azure AD, qui requièrent un administrateur de rôle privilégié) |
|Privileged Identity Management | Administrateur de rôle privilégié |
| Conditions d’utilisation | Administrateur de la sécurité ou administrateur de l’accès conditionnel |

## <a name="next-steps"></a>Étapes suivantes

- [Présentation de la gestion des droits d’utilisation Azure AD](entitlement-management-overview.md)
- [Présentation des révisions d’accès Azure AD](access-reviews-overview.md)
- [Qu’est-ce qu’Azure AD Privileged Identity Management ?](../privileged-identity-management/pim-configure.md)
- [Que puis-je faire avec Conditions d’utilisation ?](active-directory-tou.md)


