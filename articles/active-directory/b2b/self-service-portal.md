---
title: Portail d’inscription en libre-service pour Azure Active Directory B2B Collaboration | Microsoft Docs
description: Azure Active Directory B2B Collaboration prend en charge les relations interentreprises en permettant aux partenaires commerciaux d’accéder de façon sélective à vos applications d’entreprise
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/08/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 5ee55034e84fe09484a2f7613cc2224be70fdebb
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259535"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Portail d’inscription en libre-service pour Azure AD B2B Collaboration

Les clients peuvent faire beaucoup d’opérations avec les fonctionnalités intégrées qui sont exposées par le biais du [portail Azure](https://portal.azure.com) et du [panneau d’accès aux applications](https://myapps.microsoft.com) pour les utilisateurs. Cependant, vous devrez peut-être personnaliser le flux de travail d’intégration pour les utilisateurs B2B en fonction des besoins de votre organisation. Vous pouvez le faire avec [l’API d’invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

En tant qu’organisation invitante, vous ne pouvez pas savoir à l’avance quels collaborateurs externes auront besoin d’accéder à vos ressources. Vos devez disposer d’un moyen de permettre à des utilisateurs d’entreprises partenaires de s’inscrire eux-mêmes avec un ensemble de stratégies que vous contrôlez en tant qu’organisation invitante. Ce scénario est possible via les API. Il existe un [exemple de projet sur GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) qui fait précisément cela.

Le projet GitHub montre comment les organisations peuvent utiliser les API afin d’offrir à leurs partenaires approuvés des fonctionnalités d’inscription en libre-service basées sur une stratégie, avec des règles déterminant les applications auxquelles ils peuvent accéder. Les utilisateurs du partenaire peuvent accéder aux ressources lorsqu’ils en ont besoin. Ils peuvent le faire de façon sécurisée, sans demander à l’organisation invitante de les intégrer manuellement. Vous pouvez facilement déployer le projet dans un abonnement Azure de votre choix.

## <a name="as-is-code"></a>Code en l’état

Ce code est mis à disposition en tant qu’exemple pour illustrer l’utilisation de l’API Invitation Azure Active Directory B2B. Il doit être personnalisé par votre équipe de développement ou un partenaire, et doit être révisé avant tout déploiement en environnement de production.

## <a name="next-steps"></a>Étapes suivantes

* [Qu'est-ce que la collaboration B2B d'Azure AD ?](what-is-b2b.md)
* [Attribution de licences Azure AD B2B Collaboration](licensing-guidance.md)
* [Questions fréquemment posées (FAQ) sur Azure Active Directory B2B Collaboration](faq.md)