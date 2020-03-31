---
title: Choisir le protocole de fédération approprié pour une application multilocataire
description: Conseils pour les éditeurs de logiciels indépendants sur l’intégration avec Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b3edbbe037c3874d639476e516b3732b7573d9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443374"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Choisir le protocole de fédération approprié pour votre application mutualisée

Lorsque vous développez votre application software as a service (SaaS), vous devez sélectionner le protocole de fédération qui répond le mieux à vos besoins et à ceux de vos clients. Cette décision est basée sur votre plateforme de développement et votre désir d’intégrer les données disponibles au sein de l’écosystème Office 365 et Azure AD de vos clients.

Consultez la liste complète des [protocoles disponibles pour les intégrations de SSO](what-is-single-sign-on.md) avec Azure Active Directory.
Le tableau suivant compare 
* Open Authentication 2.0 (OAuth 2.0)
* Open ID Connect (OIDC)
* SAML (Security Assertion Markup Language)
* Web Services Federation (WS-Fed)

| Fonctionnalité| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| Authentification unique basée sur le Web| √| √ |
| Déconnexion unique basée sur le Web| √| √ |
| Authentification unique basée sur mobile| √| √* |
| Déconnexion unique basée sur mobile| √| √* |
| Stratégies d’accès conditionnel pour les applications mobiles| √| X |
| Expérience d’authentification multifacteur intégrée pour les applications mobiles| √| X |
| Accès à Microsoft Graph| √| X |

*Possible, mais Microsoft ne fournit pas d’exemples ou de conseils.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 et Open ID Connect

OAuth 2.0 est un protocole [standard de l’industrie](https://oauth.net/2/) pour l’autorisation. OIDC (OpenID Connect) est une couche d’authentification [standard de l’industrie](https://openid.net/connect/) basée sur le protocole OAuth 2.0.

### <a name="benefits"></a>Avantages

Microsoft recommande d’utiliser OIDC/OAuth 2.0 car ils ont l’authentification et l’autorisation intégrées aux protocoles. Avec SAML, vous devez également implémenter l’autorisation.

L’autorisation intégrée à ces protocoles permet à votre application d’accéder à de nombreuses données utilisateur et organisationnelles et de les intégrer via l’API Microsoft Graph.

L’utilisation d’OAuth 2.0 et d’OIDC simplifie l’expérience utilisateur de vos clients lors de l’adoption du SSO pour votre application. Vous pouvez facilement définir les ensembles d’autorisations nécessaires, qui sont ensuite automatiquement reproduits pour l’administrateur ou pour l’utilisateur final avec son consentement.

De plus, l’utilisation de ces protocoles permet à vos clients d’utiliser des politiques d’accès conditionnel et d’authentification multifacteur pour contrôler l’accès aux applications. Microsoft fournit des bibliothèques et des [exemples de code sur plusieurs plateformes technologiques](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) pour faciliter votre développement.  

### <a name="implementation"></a>Implémentation

Vous enregistrez votre application auprès de Microsoft Identity, qui est un fournisseur OAuth 2.0. Vous pouvez également enregistrer votre application OAuth 2.0 auprès de tout autre fournisseur d’identité que vous souhaitez intégrer. 

Pour plus d’informations sur la façon d’enregistrer votre application et d’implémenter ces protocoles pour le SSO dans les applications Web, voir [Autoriser l’accès aux applications Web en utilisant OpenID Connect et Azure Active Directory](../develop/sample-v2-code.md).  Pour plus d’informations sur l’implémentation de ces protocoles pour le SSO dans les applications mobiles, voir ce qui suit : 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Plateforme Windows universelle](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 et WSFed

Security Assertion Markup Language (SAML) est généralement utilisé pour les applications Web. Consultez [Comment Azure utilise le protocole SAML](../develop/active-directory-saml-protocol-reference.md) pour un obtenir un aperçu. 

Web Services Federation (WSFed) est un [standard de l’industrie](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) généralement utilisé pour les applications Web qui sont développées à l’aide de la plateforme .net.

### <a name="benefits"></a>Avantages

SAML 2.0 est un standard éprouvé et la plupart des plates-formes technologiques prennent en charge les bibliothèques open source pour SAML 2.0. Vous pouvez fournir à vos clients une interface d’administration pour configurer SSO SAML. Ils peuvent configurer SSO SAML pour Microsoft Azure AD et tout autre fournisseur d’identité qui prend en charge SAML 2

### <a name="trade-offs"></a>Compromis

Si vous utilisez les protocoles SAML 2.0 ou WSFed pour les applications mobiles, certaines stratégies d’accès conditionnel, notamment l’authentification multifacteur, présenteront une expérience détériorée. En outre, si vous souhaitez accéder à Microsoft Graph, vous devez implémenter l’autorisation via OAuth 2.0 pour générer les jetons nécessaires. 

### <a name="implementation"></a>Implémentation

Microsoft ne fournit pas de bibliothèques pour l’implémentation SAML ou ne recommande pas de bibliothèques spécifiques. Il existe de nombreuses bibliothèques open source disponibles.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO et l’utilisation d’API REST de Microsoft Graph 

Microsoft Graph est la structure de données de l’ensemble de Microsoft 365, y compris Office 365, Windows 10 et Enterprise Mobility et Security, ainsi que d’autres produits tels que Dynamics 365. Cela inclut les schémas de base des entités telles que Utilisateurs, Groupes, Calendrier, Courrier, Courrier, Fichiers, et plus encore, qui déterminent la productivité des utilisateurs. Microsoft Graph offre trois interfaces pour les développeurs : une API basée sur REST, une connexion de données Microsoft Graph et des connecteurs qui permettent aux développeurs d’ajouter leurs propres données dans le Microsoft Graph.  

L’utilisation de l’un des protocoles ci-dessus pour le SSO permet à votre application d’accéder aux données volumineuses disponibles grâce à l’API REST de Microsoft Graph. Cela permet à vos clients d’obtenir plus de retours sur leur investissement dans Microsoft 365. Par exemple, votre application peut appeler l’API Microsoft Graph pour l’intégrer à l’instance Office 365 de vos clients et aux éléments Microsoft Office et SharePoint des utilisateurs en surface de votre application. 

Si vous utilisez Open ID Connect pour vous authentifier, votre expérience de développement est transparente car vous utiliserez OAuth2, la base d’Open ID Connect, pour acquérir des jetons qui peuvent être utilisés pour appeler les API Microsoft Graph. Si votre application utilise SAML ou WSFed, vous devez ajouter du code supplémentaire dans votre application pour que ces OAuth2 puissent acquérir les jetons nécessaires pour invoquer les API de Microsoft Graph. 

## <a name="next-steps"></a>Étapes suivantes

[Activer SSO pour votre application multilocataire](isv-sso-content.md)

[Créer une documentation pour une application multilocataire](isv-create-sso-documentation.md)
