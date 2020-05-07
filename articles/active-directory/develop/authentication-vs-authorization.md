---
title: Authentification ou autorisation | Azure
titleSuffix: Microsoft identity platform
description: Découvrez les principes de base de l’authentification et de l’autorisation dans la Plateforme d’identités Microsoft (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 61c0202d12756201d45fe829078d84382e44b54e
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584161"
---
# <a name="authentication-vs-authorization"></a>Authentification ou autorisation

Cet article définit l’authentification et l’autorisation, et explique brièvement comment vous pouvez utiliser la Plateforme d’identités Microsoft pour authentifier et autoriser des utilisateurs dans vos applications web, API web ou applications appelant des API web protégées. Si vous voyez un terme que vous ne connaissez pas, consultez notre [glossaire](developer-glossary.md) ou nos [Vidéos sur la plateforme d’identités Microsoft](identity-videos.md), qui couvrent les concepts de base.

## <a name="authentication"></a>Authentification

L’**authentification** est le processus visant à prouver que vous êtes bien qui vous prétendez être. On abrège parfois l’authentification de cette manière : AuthN. La Plateforme d’identités Microsoft implémente le protocole [OpenID Connect](https://openid.net/connect/) pour gérer l’authentification.

## <a name="authorization"></a>Autorisation

L’**autorisation** correspond à l’action d’accorder à une partie authentifiée le droit de faire quelque chose. Elle détermine les données auxquelles vous pouvez accéder, et ce que vous pouvez faire de ces données. On abrège parfois l’autorisation de cette manière : AuthZ. La Plateforme d’identités Microsoft implémente le protocole [OAuth 2.0](https://oauth.net/2/) pour la gestion de l’autorisation.

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Authentification et autorisation à l’aide de la Plateforme d’identités Microsoft

Au lieu de créer des applications gérant chacune leurs propres informations de nom d’utilisateur et de mot de passe (ce qui entraîne une charge administrative importante lorsque vous devez ajouter ou supprimer des utilisateurs sur plusieurs applications), les applications peuvent déléguer cette responsabilité à un fournisseur d’identité centralisée.

Azure Active Directory (Azure AD) est un fournisseur d’identité centralisée, présent dans le cloud. La délégation de l’authentification et de l’autorisation à ce service permet des scénarios, tels que des stratégies d’accès conditionnel nécessitant la présence d’un utilisateur dans un lieu spécifique, l’utilisation de l’authentification multifacteur, ainsi que la possibilité pour l’utilisateur de se connecter une seule fois et d’être ensuite connecté automatiquement à toutes les applications web qui partagent le même annuaire centralisé. Cette fonctionnalité s’appelle **authentification unique (SSO)** .

La plateforme d’identités Microsoft simplifie l’authentification et l’autorisation pour les développeurs d’application en fournissant l’identité en tant que service, avec la prise en charge des protocoles standard tels que OAuth 2.0 et OpenID Connect, ainsi que des bibliothèques open source pour différentes plateformes afin de vous permettre de commencer à coder rapidement. Elle permet aux développeurs de générer des applications qui connectent toutes les identités Microsoft et obtiennent des jetons pour appeler [Microsoft Graph](https://developer.microsoft.com/graph/), d’autres API Microsoft ou des API que des développeurs ont créées. Pour plus d’informations, consultez [Évolution de la plateforme d’identités Microsoft](about-microsoft-identity-platform.md).

## <a name="next-steps"></a>Étapes suivantes

Pour d’autres rubriques traitant des concepts de base de l’authentification et de l’autorisation :

* Pour savoir comment les jetons d’accès, les jetons d’actualisation et les jetons d’ID sont utilisés dans l’authentification et l’autorisation, consultez [Jetons de sécurité](security-tokens.md).
* Pour découvrir le processus d’inscription de votre application afin qu’elle puisse s’intégrer avec la Plateforme d’identités Microsoft, consultez [Modèle d’application](application-model.md).
* Pour en savoir plus sur le flux de connexion des applications web, de bureau et mobiles dans la Plateforme d’identités Microsoft, consultez [Flux de connexion des applications](app-sign-in-flow.md).

Pour en savoir plus sur les protocoles que la Plateforme d’identités Microsoft implémente :

* Pour plus d’informations sur les normes OpenID Connect et OAuth 2.0, consultez [Protocoles OAuth 2.0 et OpenID Connect sur la Plateforme d’identités Microsoft](active-directory-v2-protocols.md).
* Pour plus d’informations sur la façon dont la Plateforme d’identités Microsoft prend en charge l’authentification unique, consultez [Protocole SAML d’authentification unique](single-sign-on-saml-protocol.md).
