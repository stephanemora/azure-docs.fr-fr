---
title: Applications web dans Azure Active Directory
description: Décrit les applications web et les principes de base sur le flux de protocole, l’inscription et l’expiration des jetons pour ce type d’application.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: fe9fb67331340314ebeea3c9e8d34436b9088856
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699748"
---
# <a name="web-apps"></a>les applications web

Les applications web sont des applications qui authentifient l’utilisateur d’un navigateur web auprès d’une application web. Dans ce scénario, l’application web dirige le navigateur de l’utilisateur pour connecter ce dernier à Azure AD. Azure AD renvoie une réponse de connexion via le navigateur de l’utilisateur, celle-ci contenant des revendications relatives à l’utilisateur dans un jeton de sécurité. Ce scénario prend en charge la connexion à l’aide des protocoles OpenID Connect, SAML 2.0 et WS-Federation.

## <a name="diagram"></a>Diagramme

![Flux d’authentification pour le scénario du type navigateur vers application web](./media/authentication-scenarios/web_browser_to_web_api.png)

## <a name="protocol-flow"></a>Flux de protocole

1. Quand un utilisateur accède à l’application et doit se connecter, il est redirigé via une demande de connexion au point de terminaison d’authentification d’Azure AD.
1. L’utilisateur se connecte sur la page de connexion.
1. Si l’authentification réussit, Azure AD crée un jeton d’authentification et renvoie une réponse de connexion à l’URL de réponse de l’application qui a été configurée dans le portail Azure. Pour une application de production, cette URL de réponse doit être au format HTTPS. Le jeton renvoyé inclut des revendications sur l’utilisateur et Azure AD dont l’application a besoin pour valider le jeton.
1. L’application valide le jeton à l’aide d’une clé de signature publique et des informations sur l’émetteur disponibles dans le document de métadonnées de fédération pour Azure AD. Une fois que l’application a validé le jeton, elle démarre une nouvelle session avec l’utilisateur. Cette session permet à l’utilisateur d’accéder à l’application jusqu’à expiration de la session.

## <a name="code-samples"></a>Exemples de code

Consultez les exemples de code pour les scénarios du type navigateur web vers application web. Et consultez régulièrement cette page à laquelle nous ajoutons fréquemment des exemples.

## <a name="app-registration"></a>Inscription d'application

Pour inscrire une application web, consultez [Inscrire une application](quickstart-register-app.md).

* Locataire unique : si vous créez une application uniquement pour votre organisation, vous devez l’inscrire dans l’annuaire de votre entreprise à l’aide du portail Azure.
* Application mutualisée : si vous créez une application qui peut être utilisée par des utilisateurs externes, vous devez l’inscrire dans le répertoire de votre entreprise, mais également dans celui de chaque organisation qui utilise l’application. Afin de mettre votre application à disposition dans ces annuaires, vous pouvez inclure pour vos clients un processus d’inscription qui leur permet de donner leur consentement à votre application. Quand ils s’inscrivent auprès de votre application, une boîte de dialogue contenant les autorisations requises par l’application s’affiche, et ils ont ensuite la possibilité de donner leur consentement. Selon les autorisations requises, il est possible qu’un administrateur de l’autre organisation doive donner le consentement. Une fois le consentement donné par l’utilisateur ou l’administrateur, l’application est inscrite dans l’annuaire de l’organisation de l’utilisateur ou de l’administrateur.

## <a name="token-expiration"></a>Expiration du jeton

La session utilisateur expire lorsque la durée de vie du jeton émis par Azure AD arrive à expiration. Votre application peut raccourcir cette durée au besoin, par exemple en déconnectant les utilisateurs suite à une période d’inactivité. Lorsque la session arrive à expiration, l’utilisateur est invité à se connecter à nouveau.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [types d’applications et scénarios](app-types.md)
* En savoir plus sur les [principes de base de l’authentification](v1-authentication-scenarios.md) Azure AD
