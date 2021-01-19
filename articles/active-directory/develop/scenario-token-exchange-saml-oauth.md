---
title: Scénario d’échange de jetons de la Plateforme d’identités Microsoft avec SAML et OIDC/OAuth dans Azure Active Directory
description: Découvrez les scénarios d’échange de jetons courants avec SAML et OIDC/OAuth dans Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 92d0dad86b3f048eb96dd7b17ed09f6e20d7cde2
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063295"
---
# <a name="microsoft-identity-platform-token-exchange-scenarios-with-saml-and-oidcoauth"></a>Scénarios d’échange de jeton de plateforme d’identités Microsoft avec SAML et OIDC/OAuth

SAML et OpenID Connect (OIDC)/OAuth sont des protocoles répandus permettant d’implémenter l’authentification unique (SSO). Certaines applications implémentent uniquement SAML, d’autres uniquement OIDC/OAuth. Les deux protocoles utilisent des jetons pour communiquer des secrets. Pour plus d’informations sur SAML, consultez [Protocole SAML d’authentification unique](single-sign-on-saml-protocol.md). Pour plus d’informations sur OIDC/OAuth, consultez [Protocoles OAuth 2.0 et OpenID Connect sur la Plateforme d’identités Microsoft](active-directory-v2-protocols.md).

Cet article décrit un scénario courant consistant à appeler l’API Graph, qui utilise OIDC/OAuth, alors que l’application implémente SAML. Des conseils de base sont donnés à eux qui travaillent avec ce cas de figure.

## <a name="scenario-you-have-a-saml-token-and-want-to-call-the-graph-api"></a>Scénario : Appeler l’API Graph tout en disposant d’un jeton SAML
De nombreuses applications sont implémentées avec SAML. Toutefois, l’API Graph utilise les protocoles OIDC/OAuth. Il est possible, bien que non trivial, d’ajouter des fonctionnalités OIDC/OAuth à une application SAML. Une fois la fonctionnalité OAuth disponible dans une application, l’API Graph peut être utilisée.

La stratégie générale consiste à ajouter la pile OIDC/OAuth à l’application. Dès que celle-ci implémente les deux standards, vous pouvez vous servir d’un cookie de session. Vous n’échangez pas explicitement de jeton. Vous ouvrez une session utilisateur avec SAML, qui génère un cookie de session. Lorsque l’API Graph appelle un flux OAuth, vous utilisez ce cookie de session pour vous authentifier. Cette stratégie suppose que les contrôles d’accès conditionnel sont réussis et que l’utilisateur est autorisé.

> [!NOTE]
> La Bibliothèque d’authentification Microsoft (MSAL) est la bibliothèque recommandée pour ajouter le comportement OIDC/OAuth. Pour plus d’informations sur MSAL, consultez [Vue d’ensemble de la bibliothèque d’authentification Microsoft (MSAL)](msal-overview.md). La bibliothèque précédente, appelée Bibliothèque d’authentification Active Directory (ADAL), n’est pas recommandée, car MSAL la remplace.

## <a name="next-steps"></a>Étapes suivantes
- [Flux d’authentification et scénarios d’applications](authentication-flows-app-scenarios.md)
