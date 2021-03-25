---
title: Bibliothèques d’authentification de la plateforme d’identités Microsoft | Azure
description: Liste des bibliothèques clientes et des intergiciels (middleware) compatibles avec la plateforme d’identités Microsoft. Utilisez ces bibliothèques pour ajouter la prise en charge de la connexion des utilisateurs (authentification) et de l’accès (autorisation) aux API web protégées à vos applications.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 590e57d587c8e6e254811892b5c5e740b511c302
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104690651"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Bibliothèques d’authentification de plateforme d’identité Microsoft

Les tableaux suivants présentent la prise en charge de la bibliothèque d’authentification Microsoft pour plusieurs types d’applications. Ils incluent des liens vers le code source de la bibliothèque, où se procurer le package du projet de votre application et si la bibliothèque prend en charge la connexion des utilisateurs (authentification), l’accès aux API web protégées (autorisation) ou les deux.

La plateforme d’identités Microsoft a été certifiée par OpenID Foundation en tant que [fournisseur OpenID certifié](https://openid.net/certification/). Si vous préférez utiliser une bibliothèque autre que la bibliothèque d’authentification Microsoft (MSAL) ou une autre bibliothèque prise en charge par Microsoft, choisissez-en une avec une [implémentation OpenID Connect certifiée](https://openid.net/developers/certified/).

Si vous choisissez de coder manuellement votre propre implémentation de niveau protocole de [OAuth 2.0 ou OpenID Connect 1.0](active-directory-v2-protocols.md), portez une attention particulière aux considérations de sécurité dans les spécifications de chaque standard et suivez une méthodologie de cycle de vie de développement logiciel (SDL) telle que [Microsoft SDL][Microsoft-SDL].

## <a name="single-page-application-spa"></a>Application monopage (SPA)

Une application monopage s’exécute entièrement sur la surface du navigateur et récupère les données de page (HTML, CSS et JavaScript) de manière dynamique ou au moment du chargement de l’application. Elle peut appeler des API web pour interagir avec des sources de données principales.

Étant donné que le code d’une application monopage s’exécute entièrement dans le navigateur, il est considéré comme un *client public* qui ne peut pas stocker les secrets de façon sécurisée.

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="web-application"></a>Application web

Une application web exécute du code sur un serveur qui génère et envoie du code HTML, CSS et JavaScript au navigateur web d’un utilisateur pour qu’il soit affiché. L’identité de l’utilisateur est conservée en tant que session entre le navigateur de l’utilisateur (front-end) et le serveur web (back-end).

Étant donné que le code d’une application web s’exécute sur le serveur web, il est considéré comme un *client confidentiel* qui peut stocker les secrets de façon sécurisée.

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

## <a name="desktop-application"></a>Application de bureau

Une application de bureau est généralement du code binaire (compilé) qui couvre une interface utilisateur et qui est conçu pour s’exécuter sur le Bureau d’un utilisateur.

Étant donné qu’une application de bureau s’exécute sur le Bureau de l’utilisateur, elle est considérée comme un *client public* qui ne peut pas stocker les secrets de façon sécurisée.

[!INCLUDE [active-directory-develop-libraries-desktop](../../../includes/active-directory-develop-libraries-desktop.md)]

## <a name="mobile-application"></a>Application mobile

Une application mobile est généralement du code binaire (compilé) qui couvre une interface utilisateur et qui est conçu pour s’exécuter sur l’appareil mobile d’un utilisateur.

Étant donné qu’une application mobile s’exécute sur l’appareil mobile de l’utilisateur, elle est considérée comme un *client public* qui ne peut pas stocker les secrets de façon sécurisée.

[!INCLUDE [active-directory-develop-libraries-mobile](../../../includes/active-directory-develop-libraries-mobile.md)]

## <a name="service--daemon"></a>Service/démon

Les services et démons sont couramment utilisés pour la communication de serveur à serveur et d’autres communications sans assistance (parfois appelées *sans périphérique de contrôle*). Étant donné qu’il n’existe aucun utilisateur au clavier pour entrer des informations d’identification ou donner son consentement à l’accès aux ressources, ces applications s’authentifient en leur nom, et non en tant qu’utilisateur, lors de la demande d’un accès autorisé aux ressources d’une API web.

Un service ou un démon qui s’exécute sur un serveur est considéré comme un *client confidentiel* qui peut stocker ses secrets de façon sécurisée.

[!INCLUDE [active-directory-develop-libraries-daemon](../../../includes/active-directory-develop-libraries-daemon.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la bibliothèque d’authentification Microsoft, reportez-vous à la [vue d’ensemble de la bibliothèque d’authentification Microsoft (MSAL)](msal-overview.md).

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
