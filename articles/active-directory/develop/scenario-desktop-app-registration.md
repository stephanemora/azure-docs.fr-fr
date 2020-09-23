---
title: Inscrire des applications de bureau appelant des API web – Plateforme d’identités Microsoft | Azure
description: Apprenez à générer une application de bureau qui appelle des API web (inscription d’application).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6796ac42a10d3b976b23f5af1418b1789011d61b
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89440947"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Application de bureau qui appelle des API web : Inscription d'application

Cet article porte sur les spécificités de l’inscription d’application pour une application de bureau.

## <a name="supported-account-types"></a>Types de comptes pris en charge

Les types de compte pris en charge dans une application de bureau dépendent de l’expérience que vous souhaitez mettre en avant. En raison de cette relation, les types de compte pris en charge dépendent des flux que vous voulez utiliser.

### <a name="audience-for-interactive-token-acquisition"></a>Audience pour l’acquisition de jetons interactive

Si votre application de bureau utilise l’authentification interactive, vous pouvez connecter des utilisateurs depuis n’importe quel [type de compte](quickstart-register-app.md).

### <a name="audience-for-desktop-app-silent-flows"></a>Audience pour les flux silencieux d’application de bureau

- Pour utiliser l’authentification Windows intégrée ou un nom d’utilisateur et un mot de passe, votre application doit connecter des utilisateurs dans votre locataire, par exemple, si vous êtes développeur d’application métier. Ou bien, dans des organisations Azure Active Directory, votre application doit se connecter aux utilisateurs de votre propre locataire s’il s’agit d’un scénario de fournisseur de logiciel indépendant. Ces flux d’authentification ne sont pas pris en charge pour des comptes Microsoft personnels.
- Si vous voulez utiliser le flux de code d’appareil, vous ne pouvez pas encore connecter des utilisateurs avec leurs comptes Microsoft personnels.
- Si vous connectez des utilisateurs avec des identités de réseaux sociaux qui passent par une stratégie et une autorité entreprise-client (B2C), vous pouvez uniquement utiliser l’authentification interactive par mot de passe et nom d’utilisateur.

## <a name="redirect-uris"></a>URI de redirection

Les URI de redirection à utiliser dans une application de bureau dépendent du flux que vous voulez utiliser.

- Si vous utilisez l’authentification interactive ou le flux de code d’appareil, utilisez `https://login.microsoftonline.com/common/oauth2/nativeclient`. Pour obtenir cette configuration, sélectionnez l’URL correspondante dans la section **Authentification** pour votre application.

  > [!IMPORTANT]
  > Aujourd’hui, MSAL.NET utilise une autre URI de redirection par défaut dans les applications de bureau qui s’exécutent sur Windows (`urn:ietf:wg:oauth:2.0:oob`). À l’avenir, nous voulons changer cela, et vous conseillons donc d’utiliser `https://login.microsoftonline.com/common/oauth2/nativeclient`.

- Si vous générez une application Objective-C ou Swift native pour macOS, enregistrez l’URI de redirection en fonction de l’identificateur de bundle de votre application, au format suivant : msauth.<your.app.bundle.id>://auth. Remplacez <your.app.bundle.id> par l’identificateur de bundle de votre application.
- Si votre application n’utilise que l’authentification Windows intégrée ou un mot de passe et un nom d’utilisateur, vous n’avez pas besoin d’inscrire d’URI de redirection pour votre application. Ces flux effectuent un aller-retour vers le point de terminaison de la plateforme d’identités Microsoft v2.0. Votre application ne sera pas rappelée sur un URI spécifique.
- Pour différencier le flux de code d’appareil, l’authentification Windows intégrée et un mot de passe associé à un nom d’utilisateur, d’un flux d’application cliente confidentielle qui ne dispose d’aucune URI de redirection non plus (le flux d’informations d’identification client utilisé dans les applications démon), vous devez préciser que votre application est une application cliente publique. Pour réaliser cette configuration, accédez à la section **Authentification** de votre application. Dans la sous-section **Paramètres avancés** du paragraphe **Type de client par défaut**, sélectionnez **Oui** pour **Considérer l’application comme un client public**.

  ![Autoriser un client public](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Autorisations des API

Les applications de bureau appellent des API pour le compte de l’utilisateur connecté. Elles doivent demander des autorisations déléguées. Elles ne peuvent pas demander d’autorisations d’application, qui ne sont gérées que dans des [applications démon](scenario-daemon-overview.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Application de bureau : Configuration de l’application](scenario-desktop-app-configuration.md)
