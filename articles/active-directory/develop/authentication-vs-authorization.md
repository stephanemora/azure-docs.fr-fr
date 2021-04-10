---
title: Authentification ou autorisation | Azure
titleSuffix: Microsoft identity platform
description: Découvrez les concepts de base de l’authentification et de l’autorisation dans la plateforme d’identités Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 5244a8e572abb56c00d87d0bdd7e8d1291af9b9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99581905"
---
# <a name="authentication-vs-authorization"></a>Authentification ou autorisation

Cet article définit l’authentification et l’autorisation. Il explique aussi brièvement comment utiliser la Plateforme d’identités Microsoft pour authentifier et autoriser des utilisateurs dans les applications web, les API web et les applications qui appellent des API web protégées. Si vous rencontrez un terme que vous ne connaissez pas, consultez notre [glossaire](developer-glossary.md) ou nos [vidéos sur la Plateforme d’identités Microsoft](identity-videos.md), qui couvrent les concepts de base.

## <a name="authentication"></a>Authentification

*L’authentification* est le processus visant à prouver que vous êtes bien qui vous prétendez être. En anglais, elle est parfois abrégée en *AuthN*. La Plateforme d’identités Microsoft utilise le protocole [OpenID Connect](https://openid.net/connect/) pour gérer l’authentification.

## <a name="authorization"></a>Autorisation

L’*autorisation* correspond à l’action d’accorder à une partie authentifiée le droit de faire quelque chose. Elle détermine les données auxquelles vous pouvez accéder, et ce que vous pouvez faire de ces données. En anglais, elle est parfois abrégée en *AuthZ*. La Plateforme d’identités Microsoft utilise le protocole [OAuth 2.0](https://oauth.net/2/) pour gérer l’autorisation.

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Authentification et autorisation à l’aide de la Plateforme d’identités Microsoft

Lorsque l’on crée des applications qui gèrent chacune leurs propres informations de nom d’utilisateur et de mot de passe, ajouter ou supprimer des utilisateurs sur plusieurs applications représente une charge administrative importante. Vos applications peuvent au contraire déléguer cette responsabilité à un fournisseur d’identité centralisé.

Azure Active Directory (Azure AD) est un fournisseur d’identité centralisée, présent dans le cloud. Le fait de lui déléguer l’authentification et l’autorisation rend possibles différents scénarios :

- Stratégies d’accès conditionnel selon lesquelles un utilisateur doit se trouver dans un emplacement spécifique
- Recours à [l’authentification multifacteur](../authentication/concept-mfa-howitworks.md), parfois appelée authentification à deux facteurs ou 2FA
- Cas de figure selon lequel un utilisateur se connecte une seule fois, puis est automatiquement connecté à toutes les applications web qui partagent le même répertoire centralisé, grâce à une fonctionnalité nommée *authentification unique (SSO)*

La Plateforme d’identités Microsoft simplifie l’autorisation et l’authentification pour les développeurs d’applications en fournissant l’identité en tant que service (« identity as a service » ou IaaS). Elle prend en charge les protocoles standard et les bibliothèques open source de différentes plateformes pour permettre aux développeurs de commencer à coder rapidement. Ces derniers peuvent générer des applications qui connectent toutes les identités Microsoft, récupèrent des jetons pour appeler [Microsoft Graph](https://developer.microsoft.com/graph/), et accèdent aux API Microsoft et à d’autres API créées par leurs soins.

Cette vidéo décrit la Plateforme d’identité Microsoft et les concepts de base de l’authentification moderne : 

> [!VIDEO https://www.youtube.com/embed/tkQJSHFsduY]

Voici une comparaison des protocoles utilisés par la Plateforme d’identités Microsoft :

* **OAuth et OpenID Connect** : la plateforme utilise OAuth pour l’autorisation et OpenID Connect (OIDC) pour l’authentification. Comme OpenID Connect s’appuie sur OAuth 2.0, la terminologie et le flux sont similaires entre ces deux protocoles. Il est même possible d’authentifier un utilisateur (par OpenID Connect) et d’obtenir l’autorisation d’accéder à l’une des ressources protégées qu’il possède (par OAuth 2.0) dans une même requête. Pour plus d’informations, consultez [Protocoles OAuth 2.0 et OpenID Connect](active-directory-v2-protocols.md) et [Protocole OpenID Connect](v2-protocols-oidc.md).
* **OAuth et SAML** : la plateforme utilise OAuth 2.0 pour l’autorisation et SAML pour l’authentification. Pour savoir comment utiliser ces protocoles ensemble afin d’authentifier un utilisateur et d’obtenir l’autorisation d’accéder à une ressource protégée, consultez [Plateforme d’identités Microsoft et flux d’assertion du porteur SAML OAuth 2.0](./scenario-token-exchange-saml-oauth.md).
* **OpenID Connect et SAML** : la plateforme utilise à la fois OpenID Connect et SAML pour authentifier un utilisateur et activer l’authentification unique. L’authentification SAML étant couramment utilisée avec les fournisseurs d’identité comme les services de fédération Active Directory (AD FS) fédérés à Azure AD, elle sert souvent dans les applications d’entreprise. OpenID Connect est fréquemment employé pour les applications qui se trouvent exclusivement dans le cloud, notamment les applications mobiles, les sites web et les API web.

## <a name="next-steps"></a>Étapes suivantes

Voici d’autres rubriques qui couvrent les concepts de base de l’authentification et de l’autorisation :

* Pour savoir comment les jetons d’accès, les jetons d’actualisation et les jetons d’ID sont utilisés dans l’autorisation et l’authentification, consultez [Jetons de sécurité](security-tokens.md).
* Pour plus d’informations sur le processus d’inscription d’une application lui permettant de s’intégrer avec la Plateforme d’identités Microsoft, consultez [Modèle d’application](application-model.md).