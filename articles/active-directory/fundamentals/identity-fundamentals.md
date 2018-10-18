---
title: Quels sont les principes fondamentaux de la gestion des identités et des accès Azure ? - Azure Active Directory | Microsoft Docs
description: Découvrez les fonctions de protection avancées et les outils supplémentaires qui sont disponibles dans les éditions Premium d’Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2018
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: f7baa29c77ae4af9813bfc755a39cc07288a3ad2
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734671"
---
# <a name="what-are-the-fundamentals-of-azure-identity-and-access-management"></a>Quels sont les principes fondamentaux de la gestion des identités et des accès Azure ?
Azure AD Premium est une solution de gestion des identités et des accès dans le cloud dotée de fonctions avancées de protection. Ces fonctions avancées offre une identité sécurisée pour toutes vos applications, une protection des identités (renforcée par le [graphique de sécurité d’analyse décisionnelle Microsoft](https://www.microsoft.com/security/intelligence)) et [Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md). Azure AD vous aide à protéger les identités des utilisateurs en temps réel, ce qui vous aide à créer des stratégies d’accès adaptatives basées sur les risques afin de préserver les données de votre entreprise.

Regardez cette vidéo pour un aperçu rapide de la protection et de la gestion des identités Azure AD :
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

Azure AD fournit également un ensemble d’outils qui peuvent vous aider à sécuriser, automatiser et gérer votre environnement, notamment pour la réinitialisation des mots de passe, la gestion des utilisateurs et des groupes ainsi que les requêtes d’application. Azure AD peut également vous aider à gérer les appareils appartenant à l’utilisateur ainsi qu’à accéder et contrôler les applications SaaS (Software as a Servcie).

Pour plus d’informations sur les coûts des éditions Premium d’Azure Active Directory et les outils associés, consultez la [Tarification Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>Se connecter à Active Directory en local avec Azure AD et Office 365
Étendez votre implémentation locale d’Active Directory au cloud en intégrant vos répertoires locaux à Azure AD via la [gestion des identités hybrides](https://aka.ms/aadframework). [Azure AD Connect](../connect/active-directory-aadconnect.md) offre cette intégration, ce qui donne à vos utilisateurs une identité unique et un accès par authentification unique (SSO) à vos ressources locales et vos services cloud, par exemple Office 365.

Azure AD Connect remplace les versions antérieures des outils d’intégration d’identité comme DirSync et Azure AD Sync. Il aide à prendre en charge vos besoins de synchronisation des identités entre vos répertoires en local et Azure AD. La synchronisation Azure AD Connect est activée via :

- **Synchronisation.** Ce composant est chargé de créer des utilisateurs, des groupes et d’autres objets. Il permet également de s’assurer que les informations d’identité relatives aux utilisateurs locaux correspondent à celles qui se trouvent dans Azure AD. L’activation de l’écriture différée de mot de passe permet également de maintenir la synchronisation de vos répertoires locaux lorsque les utilisateurs mettent à jour les mots de passe dans Azure AD.

- Authentification. Choisir la méthode d’authentification adaptée est une décision importante pour la configuration d’une solution d’identité hybride Azure AD. Vous pouvez choisir entre l’authentification cloud (synchronisation du hachage de mot de passe / authentification directe) ou l’authentification fédérée (AD FS) pour votre organisation. Pour plus d’informations sur les options disponibles, consultez [Choisir la méthode d’authentification adaptée à votre solution d’identité hybride Azure Active Directory](https://aka.ms/auth-options).

- **Supervision de l’intégrité.** Azure AD Connect Health offre une supervision robuste et un emplacement central dans le portail Azure pour l’affichage de cette activité. Pour plus d’informations, consultez [Surveillez votre infrastructure d’identité locale et vos services de synchronisation dans le cloud](../connect-health/active-directory-aadconnect-health.md).

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>Augmenter la productivité et réduire les coûts du support technique avec des expériences en libre-service et d’authentification unique
Les utilisateurs gagnent du temps s’ils disposent d’un seul nom d’utilisateur et un seul mot de passe, ainsi que d’une expérience cohérente sur chaque appareil. Ils gagnent également du temps en réalisant des tâches en libre-service comme [réinitialiser un mot de passe oublié](../user-help/active-directory-passwords-update-your-own-password.md) ou demander l’accès à une application sans attendre l’aide du support technique.

Afin d’améliorer l’authentification unique et l’expérience cohérente, Azure AD [étend votre répertoire Active Directory local](../connect/active-directory-aadconnect.md) au cloud. Cela permet aux utilisateurs d’utiliser leur compte professionnel principal non seulement pour se connecter aux appareils appartenant au domaine et aux ressources de l’entreprise, mais également à toutes les applications SaaS et web nécessaires à leur travail. 

En outre, l’accès aux applications peut être automatiquement approvisionné (ou déprovisionné) selon les appartenances au groupe et le statut d’employé d’un utilisateur. Cela vous aide à contrôler l’accès aux applications de la galerie ou à vos propres applications locales que vous avez développées et publiées via le [Proxy d’application Azure AD](../manage-apps/application-proxy.md).

## <a name="manage-and-control-access-to-your-organizational-resources"></a>Gérer et contrôler l’accès à vos ressources d’organisation
Les solutions de gestion des identités et des accès Microsoft vous aident à protéger l’accès aux applications et aux ressources dans le centre de données de votre organisation, mais aussi dans le cloud. Cette gestion des accès permet d’offrir des niveaux supplémentaires de validation, notamment l’[Authentification multifacteur](../authentication/concept-mfa-howitworks.md) et les [stratégies d’accès conditionnel](../conditional-access/overview.md). La supervision des activités suspectes via les fonctions avancées de création de rapport, d’audit et d’alertes de sécurité, peut aussi aider à limiter les problèmes de sécurité éventuels.

L’utilisation des stratégies d’accès conditionnel dans Azure AD Premium vous permet de créer des règles d’accès basées sur la stratégie pour n’importe quelle application connectée à Azure AD telles que les applications SaaS, les applications personnalisées s’exécutant dans le cloud ou en local, ou encore les applications web. Azure AD évalue ces règles en temps réel, puis les applique à chaque fois qu’un utilisateur tente d’accéder à une application. Les stratégies de protection des identités Azure vous permettent d’agir automatiquement (en bloquant l’accès, en appliquant l’authentification multifacteur ou en réinitialisant les mots de passe utilisateur) si une activité suspecte est détectée.

## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory Privileged Identity Management
[Privileged Identity Management (PIM)](../privileged-identity-management/pim-getting-started.md), qui fait partie de l’édition Premium 2 d’Azure Active Directory, vous permet de découvrir, de restreindre et de surveiller les comptes administratifs ainsi que leur accès aux ressources dans votre répertoire Azure Active Directory et d’autres services en ligne Microsoft. PIM vous permet également d’administrer des accès administratifs à la demande pendant la période de temps exacte dont vous avez besoin. Cela signifie que vous pouvez autoriser les administrateurs à demander une élévation temporaire et authentifiée par MFA de leurs privilèges pour une période de temps préconfigurée avant que leurs comptes ne retournent à un état d’utilisateur normal.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’architecture Azure AD, consultez [Qu’est-ce que l’architecture Azure AD ?](active-directory-architecture.md).
