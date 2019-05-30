---
title: Gouvernance des identités - Azure Active Directory | Microsoft Docs
description: Azure Active Directory une gouvernance des identités permet d’équilibrer les besoins de votre organisation pour la productivité des employés et de sécurité avec une visibilité et les processus appropriés.
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
ms.date: 04/29/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1be6fc2b7d6da85778524cb8986f399c341370c1
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307039"
---
# <a name="what-is-azure-ad-identity-governance"></a>Qu’est Azure AD Identity gouvernance ?

Azure Active Directory (Azure AD) une gouvernance des identités permet d’équilibrer les besoins de votre organisation pour la productivité des employés et de sécurité avec une visibilité et les processus appropriés. Ce service vous offre les fonctionnalités requises pour vous assurer que les utilisateurs appropriés bénéficient d’un accès approprié aux ressources appropriées. Il vous permet également de protéger, surveiller et auditer l’accès aux ressources critiques, tout en garantissant la productivité des employés.  

Gouvernance des identités donner aux organisations la possibilité d’effectuer les tâches suivantes sur les employés, les partenaires commerciaux et les fournisseurs et les services et applications :

- Gouverner le cycle de vie des identités
- Gouverner le cycle de vie des accès
- Sécuriser l’administration

Plus précisément, ce service vise à aider les organisations à répondre à ces quatre questions clés :

- Quels utilisateurs doivent pouvoir accéder à quelles ressources ?
- Que font les utilisateurs de cet accès ?
- Des contrôles organisationnels efficaces sont-ils disponibles pour gérer l’accès ?
- Des auditeurs peuvent-ils vérifier que les contrôles fonctionnent ?

## <a name="identity-lifecycle"></a>Cycle de vie des identités

Gouvernance des identités permet aux organisations d’atteindre un équilibre entre *productivité* -la vitesse à laquelle une personne peut avoir accès aux ressources dont ils ont besoin, tels que lorsqu’ils rejoignent mon organisation ? et *sécurité* (comment leur accès doit évoluer au fil du temps, par exemple à la suite de modifications de leur statut professionnel).  Gestion du cycle de vie des identités constitue le fondement de gouvernance des identités et une gouvernance efficace à l’échelle nécessite la modernisation de l’infrastructure de gestion de cycle de vie des identités pour les applications.

![Cycle de vie des identités](./media/identity-governance-overview/identity-lifecycle.png)

Pour de nombreuses organisations, le cycle de vie des identités pour les employés est lié à la représentation de l’utilisateur concerné dans un système de GCH (gestion du capital humain).  Azure AD Premium gère automatiquement les identités des utilisateurs pour les personnes représentées dans Workday, et ce, dans Active Directory et Azure Active Directory, comme décrit dans le didacticiel [Workday inbound provisioning (preview)](../saas-apps/workday-inbound-tutorial.md) (provisionnement entrant Workday (préversion)).  Azure AD Premium inclut également [Microsoft Identity Manager](/microsoft-identity-manager/), qui peut importer des enregistrements à partir de systèmes de GCH locaux tels que SAP, Oracle eBusiness et Oracle PeopleSoft.

De plus en plus de scénarios impliquent une collaboration avec des personnes extérieures à votre organisation. La collaboration [Azure AD B2B](/azure/active-directory/b2b/) permet de partager en toute sécurité les applications et services de votre organisation avec des utilisateurs invités et des partenaires externes à partir de n’importe quelle organisation, tout en conservant le contrôle sur vos propres données d’entreprise.

## <a name="access-lifecycle"></a>Cycle de vie des accès

Les organisations ont besoin d’un processus pour gérer l’accès au-delà de ce qui a été initialement configuré pour un utilisateur lors de la création de son identité.  En outre, les entreprises doivent pouvoir procéder efficacement à une mise à l’échelle pour être en mesure de développer et d’appliquer des contrôles et une stratégie d’accès de manière continue.

![Cycle de vie des accès](./media/identity-governance-overview/access-lifecycle.png)

En règle générale, le service informatique délègue aux décideurs d’entreprise la prise de décision en matière d’approbation d’accès.  De plus, le service informatique peut impliquer les utilisateurs eux-mêmes.  Par exemple, les utilisateurs accédant à des données client confidentielles dans l’application marketing d’une société en Europe doivent connaître les stratégies de l’entreprise. Les utilisateurs invités d’une organisation n’ont peut-être pas connaissance de ses exigences en matière de traitement des données.

Les organisations peuvent automatiser le processus de cycle de vie des accès à l’aide de technologies comme les [groupes dynamiques](../users-groups-roles/groups-dynamic-membership.md), moyennant l’approvisionnement des utilisateurs sur des [applications SaaS](../saas-apps/tutorial-list.md) ou des [applications intégrées à SCIM](../manage-apps/use-scim-to-provision-users-and-groups.md).  Les organisations peuvent également contrôler les [utilisateurs invités pouvant accéder aux applications locales](../b2b/hybrid-cloud-to-on-premises.md).  Ces droits d’accès peuvent ensuite être révisés régulièrement par le biais de [révisions d’accès Azure AD](access-reviews-overview.md) récurrentes.

Lorsqu’un utilisateur tente d’accéder à des applications, Azure AD applique des stratégies [d’accès conditionnel](/azure/active-directory/conditional-access/). Par exemple, les stratégies d’accès conditionnel peuvent inclure l’affichage de [conditions d’utilisation](../conditional-access/terms-of-use.md) et un processus [garantissant que l’utilisateur a accepté ces conditions](../conditional-access/require-tou.md) avant de lui permettre d’accéder à une application.

## <a name="privileged-access-lifecycle"></a>Cycle de vie des accès privilégiés

Historiquement, un accès privilégié a été décrit par d’autres fournisseurs comme une fonctionnalité distincte à partir de la gouvernance des identités. Toutefois, chez Microsoft, nous pensons que de qui régissent l’accès privilégié est une partie essentielle de gouvernance des identités, en particulier, étant donné le risque de mauvaise utilisation associée à ces administrateur droits peuvent provoquer une organisation. Les employés, fournisseurs et sous-traitants qui bénéficient de droits d’administration doivent être gouvernés.

![Cycle de vie des accès privilégiés](./media/identity-governance-overview/privileged-access-lifecycle.png)

Azure AD Privileged Identity Management (PIM) offre des contrôles supplémentaires conçus pour sécuriser les droits d’accès aux ressources dans Azure AD, Azure et d’autres services en ligne Microsoft.  L’accès juste-à-temps et les fonctionnalités d’alerte de changement de rôle fournis par Azure AD PIM, en plus de l’authentification multifacteur et de l’accès conditionnel, offrent un ensemble complet de contrôles de gouvernance contribuant à sécuriser les ressources de votre société (annuaire, Office 365 et rôles de ressources Azure). Comme avec d’autres formes d’accès, les organisations peuvent utiliser les révisions d’accès pour configurer une recertification récurrente des accès pour tous les utilisateurs bénéficiant de rôles d’administrateur.

## <a name="getting-started"></a>Prise en main

Il n’existe aucune solution parfaite ou une recommandation pour chaque client, les configurations suivantes fournissent des instructions pour ce que les stratégies de base que Microsoft vous recommande de vous suivent pour garantir une force de travail plus sécurisé et productif.

- [Configurations des identités et de l’accès aux appareils](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Sécurisation de l’accès privilégié](../users-groups-roles/directory-admin-roles-secure.md)

Vous pouvez également consulter l’onglet mise en route de **gouvernance des identités** dans le portail Azure pour commencer à utiliser la gestion des habilitations, accéder aux révisions, Privileged Identity Management et conditions d’utilisation.

![Gouvernance des identités mise en route](./media/identity-governance-overview/getting-started.png)

## <a name="next-steps"></a>Étapes suivantes

- [Qu’est la gestion des droits Azure AD ? (Préversion)](entitlement-management-overview.md)
- [Présentation des révisions d’accès Azure AD](access-reviews-overview.md)
- [Qu’est-ce qu’Azure AD Privileged Identity Management ?](../privileged-identity-management/pim-configure.md)
- [Que puis-je faire avec Conditions d’utilisation ?](active-directory-tou.md)
