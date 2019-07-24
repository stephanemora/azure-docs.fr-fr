---
title: Application de bureau qui appelle des API web (inscription d’application) - Plateforme d’identité Microsoft
description: Apprenez à générer une application de bureau qui appelle des API web (inscription d’application)
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
ms.openlocfilehash: 5ab2701a82da0b8f7bc4e23a3d947be905593e85
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057220"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>Application de bureau qui appelle des API web - Inscription d’application

Cet article contient les spécificités d’une inscription d’application pour une application de bureau.

## <a name="supported-accounts-types"></a>Types de comptes pris en charge

Les types de compte pris en charge dans l’application de bureau dépendent de l’expérience que vous souhaitez mettre en avant, et donc des flux que vous souhaitez utiliser.

### <a name="audience-for-interactive-token-acquisition"></a>Audience pour l’acquisition de jetons interactive

Si votre application de bureau utilise l’authentification interactive, vous pouvez connecter des utilisateurs depuis n’importe quel [type de comptes](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).

### <a name="audience-for-desktop-app-silent-flows"></a>Audience pour les flux silencieux d’application de bureau

- Si vous comptez utiliser l’authentification Windows intégrée ou le nom d’utilisateur/mot de passe, votre application doit connecter des utilisateurs dans votre locataire (développeur LOB), ou dans des organisations Azure Active Directory (scénario ISV). Ces flux d’authentification ne sont pas pris en charge pour des comptes Microsoft personnels
- Si vous voulez utiliser le flux de code de l’appareil, vous ne pouvez pas encore connecter des utilisateurs avec leurs comptes Microsoft personnels
- Si vous connectez des utilisateurs avec des identités sociales en passant une stratégie et une autorité B2C, vous pouvez uniquement utiliser l’authentification par mot de passe/utilisateur et l’authentification interactive.

## <a name="redirect-uris"></a>URI de redirection

Les URI de redirection à utiliser dans une application de bureau dépendent du flux que vous voulez utiliser.

- Si vous utilisez l’**authentification interactive** ou le **Flux de code de l’appareil**, vous utilisez `https://login.microsoftonline.com/common/oauth2/nativeclient`. Vous obtenez cette configuration en cliquant sur l’URL correspondante dans la section **Authentification** de votre application
  
  > [!IMPORTANT]
  > Aujourd’hui, MSAL.NET utilise une autre URI de redirection par défaut dans des applications de bureau qui s’exécutent sur Windows (`urn:ietf:wg:oauth:2.0:oob`). À l’avenir, nous voulons changer cela. Nous vous conseillons donc d’utiliser `https://login.microsoftonline.com/common/oauth2/nativeclient`.

- Si votre application n’utilise que l’authentification intégrée Windows, par mot de passe/nom d’utilisateur, vous n’avez pas besoin d’inscrire d’URI de redirection pour votre application. En effet, ces flux effectuent un aller-retour vers le point de terminaison v2.0 de la plateforme d’identités Microsoft et votre application n’est pas rappelée sur une URI spécifique. 
- Pour différencier le flux de code d’appareil, l’authentification Windows intégrée et l’authentification par mot de passe/nom d’utilisateur d’un flux d’application cliente confidentiel, qui ne dispose d’aucune URI de redirection (le flux d’informations d’identification client utilisé dans les applications démon), vous devez préciser que votre application est une application cliente publique. Vous obtenez cette configuration en accédant à la section **Authentification** de votre application, et dans la sous-section **Paramètres avancés**, répondez **Oui** à la question **Considérer l’application comme un client public** (dans le paragraphe **Type de client par défaut**)

  ![Autoriser un client public](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Autorisations des API

Les applications de bureau appellent des API pour le compte de l’utilisateur connecté. Elles doivent demander des autorisations déléguées. Elles ne peuvent demander de permissions d’application (qui ne sont gérées que dans des [applications démon](scenario-daemon-overview.md))

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Application de bureau - configuration de l’application](scenario-desktop-app-configuration.md)
