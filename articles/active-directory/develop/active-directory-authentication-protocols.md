---
title: Protocoles d’authentification Azure Active Directory | Microsoft Docs
description: Vue d’ensemble des protocoles d’authentification pris en charge par Azure Active Directory (AD)
author: rwike77
services: active-directory
manager: CelesteDG
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/27/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: b370736a5e4994651499716e3a923cf59465ca96
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74845414"
---
# <a name="azure-active-directory-authentication-protocols"></a>Protocoles d’authentification d’Azure Active Directory

Azure Active Directory (Azure AD) prend en charge plusieurs protocoles d’authentification et d’autorisation parmi ceux les plus couramment utilisés. Les rubriques de cette section décrivent les protocoles pris en charge et leur implémentation dans Azure AD. Les rubriques comprennent une revue des types de revendications pris en charge, une présentation de l’utilisation des métadonnées de fédération, de la documentation de référence détaillée sur les protocoles OAuth 2.0. et SAML 2.0 et une section de dépannage.

## <a name="authentication-protocols-articles-and-reference"></a>Articles et références sur les protocoles d’authentification

* [Informations importantes sur la substitution des clés de signature dans Azure AD](active-directory-signing-key-rollover.md) : découvrez le rythme de substitution des clés de signature d’Azure AD, les modifications que vous pouvez effectuer pour mettre la clé à jour automatiquement et une description de la mise à jour des scénarios d’application les plus courants.
* [Types de jeton et de revendication pris en charge](v1-id-and-access-tokens.md) : découvrez les revendications des jetons émis par Azure AD.
* [Métadonnées de fédération](azure-ad-federation-metadata.md) : découvrez comment trouver et interpréter les documents de métadonnées générés par Azure AD.
* [OAuth 2.0 dans Azure AD](v1-protocols-oauth-code.md) : découvrez comment implémenter OAuth 2.0 dans Azure AD.
* [OpenID Connect 1.0](v1-protocols-openid-connect-code.md) : découvrez comment utiliser OAuth 2.0, un protocole d’autorisation, pour l’authentification.
* [Appels de service à service avec les informations d’identification du client](v1-oauth2-client-creds-grant-flow.md) : découvrez comment utiliser les flux d’octroi d’informations d’identification du client OAuth 2.0 pour les appels de service à service.
* [Appels de service à service avec le flux Pour le compte de](v1-oauth2-on-behalf-of-flow.md) : découvrez comment utiliser les flux Pour le compte de OAuth 2.0 pour les appels de service à service.
* [Informations de référence sur le protocole SAML](active-directory-saml-protocol-reference.md) : découvrez les profils SAML d’authentification unique et de déconnexion unique d’Azure AD.

## <a name="see-also"></a>Voir aussi

* [Guide du développeur Azure Active Directory](v1-overview.md)
* [Exemples de code Azure Active Directory](sample-v1-code.md)
