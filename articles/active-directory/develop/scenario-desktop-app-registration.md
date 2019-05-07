---
title: Application de bureau que les appels de web API (inscription de l’application) - plateforme d’identité Microsoft
description: Découvrez comment créer une application de bureau que les appels de web API (inscription de l’application)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5da934709274d90668d94dfea3a9c223e191d032
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076059"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>Application de bureau que les appels de web API - inscription de l’application

Cet article contient des spécificités de l’inscription d’application pour une application de bureau.

## <a name="supported-accounts-types"></a>Types de comptes pris en charge

Les types de compte pris en charge dans l’application de bureau dépendent de l’expérience que vous souhaitez la mise en évidence de, et par conséquent sur les flux que vous souhaitez utiliser.

### <a name="audience-for-interactive-token-acquisition"></a>Audience pour l’acquisition des jetons interactive

Si votre application de bureau utilise l’authentification interactive, vous pouvez connecter des utilisateurs à partir d’un [type de compte](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)

### <a name="audience-for-desktop-app-silent-flows"></a>Audience pour les flux en mode silencieux d’application de bureau

- Si vous avez l’intention d’utiliser l’authentification intégrée Windows ou nom d’utilisateur/mot de passe, votre application doit se connecter les utilisateurs dans votre propre client (développeur métier), ou dans les organisations de répertoire Azure Active (scénario ISV). Ces flux d’authentification ne sont pas pris en charge pour les comptes personnels Microsoft
- Si vous souhaitez utiliser le flux de code d’appareil, vous ne pouvez pas connecter les utilisateurs avec leurs comptes personnels Microsoft encore
- Si vous vous connectez les utilisateurs avec des identités sociales en passant d’une autorité de B2C et de la stratégie, vous pouvez uniquement utiliser l’authentification interactive et nom d’utilisateur-mot de passe.

## <a name="redirect-uris"></a>URI de redirection

À nouveau l’URI à utiliser dans l’application de bureau de redirection dépendra du flux que vous souhaitez utiliser.

- Si vous utilisez l’authentification interactive, vous souhaiterez utiliser `https://login.microsoftonline.com/common/oauth2/nativeclient`. Vous allez obtenir cette configuration en cliquant sur l’URL correspondante dans le **authentification** section pour votre application
  
  > [!IMPORTANT]
  > Aujourd'hui MSAL.NET utilise un autre URI de redirection par défaut dans les applications de bureau s’exécutant sur Windows (`urn:ietf:wg:oauth:2.0:oob`). À l’avenir, il nous faut changer cette valeur par défaut, et par conséquent, nous vous recommandons d’utiliser `https://login.microsoftonline.com/common/oauth2/nativeclient`

- Si votre application utilise uniquement l’authentification Windows intégrée, nom d’utilisateur/mot de passe ou le périphérique de flux de Code, vous n’avez pas besoin de s’inscrire à un URI de redirection pour votre application. En effet, ces flux faire un aller-retour vers le point de terminaison v2.0 de plateforme identité Microsoft et votre application ne soit rappelée sur n’importe quel URI spécifique. Pour distinguer les à partir d’un flux d’application client confidentiel, qui n’a pas URI de redirection soit (le client d’informations d’identification flux utilisé dans les applications de démon), vous avez besoin exprimer que votre application est une application cliente publique. Cette configuration est obtenue en accédant à la **authentification** section pour votre application et dans le **paramètres avancés** sous-section, choisissez **Oui**, à la question **Traiter application comme un client public** (dans le **type de client par défaut** paragraphe)

  ![Autoriser le client public](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Autorisations des API

Applications de bureau appellent des API pour le compte de l’utilisateur connecté. Ils doivent demander des autorisations déléguées. Ils ne peuvent pas demander les autorisations d’application (qui sont géré uniquement dans [applications de démon](scenario-daemon-overview.md))

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Application de bureau - configuration de l’application](scenario-desktop-app-configuration.md)
