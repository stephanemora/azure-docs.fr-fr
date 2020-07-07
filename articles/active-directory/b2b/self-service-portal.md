---
title: Portail d’inscription en libre-service pour B2B Collaboration - Azure AD
description: Azure Active Directory B2B Collaboration prend en charge les relations interentreprises en permettant aux partenaires commerciaux d’accéder de façon sélective à vos applications d’entreprise
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff18d3d9cae6e887abbd9fb1845de62386062b2b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85386705"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Inscription en libre-service pour Azure AD B2B Collaboration

Les clients peuvent faire beaucoup d’opérations avec les fonctionnalités intégrées qui sont exposées par le biais du [portail Azure](https://portal.azure.com) et du [panneau d’accès aux applications](https://myapps.microsoft.com) pour les utilisateurs. Cependant, vous devrez peut-être personnaliser le flux de travail d’intégration pour les utilisateurs B2B en fonction des besoins de votre organisation.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>Gestion des droits d’utilisation Azure AD pour l’inscription des utilisateurs invités B2B

En tant qu’organisation invitante, vous ne pouvez pas savoir à l’avance quels collaborateurs externes auront besoin d’accéder à vos ressources. Vos devez disposer d’un moyen de permettre à des utilisateurs d’entreprises partenaires de s’inscrire avec les stratégies que vous contrôlez. Si vous souhaitez autoriser les utilisateurs d’autres organisations à demander l’accès, et qu’après approbation ils soient provisionnés avec des comptes invités et affectés à des groupes, des applications et des sites SharePoint Online, vous pouvez utiliser la [gestion des droits d’utilisation Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) pour configurer des stratégies qui [gèrent l’accès pour les utilisateurs externes](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users).

## <a name="azure-active-directory-b2b-invitation-api"></a>API d’invitation B2B Azure Active Directory

Les organisations peuvent utiliser l’[API gestionnaire d’invitation Microsoft Graph](https://docs.microsoft.com/graph/api/resources/invitation?view=graph-rest-1.0) pour créer leurs propres expériences d’intégration pour les utilisateurs invités B2B. Quand vous souhaitez proposer une inscription d’utilisateur invité en libre-service B2B, nous vous recommandons d’utiliser la [gestion des droits d’utilisation Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Si par contre vous souhaitez créer votre propre expérience, vous pouvez utiliser l’[API de création d’invitation](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0&tabs=http) pour envoyer automatiquement votre e-mail d’invitation personnalisé directement à l’utilisateur B2B, par exemple. Votre application peut aussi utiliser l’inviteRedeemUrl retourné dans la réponse de création pour créer votre propre invitation (par le biais du mécanisme de communication de votre choix) pour l’utilisateur invité.

## <a name="next-steps"></a>Étapes suivantes

* [Qu'est-ce que la collaboration B2B d'Azure AD ?](what-is-b2b.md)
* [Attribution de licences Azure AD B2B Collaboration](licensing-guidance.md)
* [Questions fréquemment posées (FAQ) sur Azure Active Directory B2B Collaboration](faq.md)
