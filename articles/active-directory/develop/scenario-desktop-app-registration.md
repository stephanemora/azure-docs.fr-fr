---
title: Inscrire des applications de bureau appelant des API web | Azure
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: 8a1a2d7f5272def78cd162da1f6ac0265d4fb30b
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102517734"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Application de bureau qui appelle des API web : Inscription d'application

Cet article porte sur les spécificités de l’inscription d’application pour une application de bureau.

## <a name="supported-account-types"></a>Types de comptes pris en charge

Les types de compte pris en charge dans une application de bureau dépendent de l’expérience que vous souhaitez mettre en avant. En raison de cette relation, les types de compte pris en charge dépendent des flux que vous voulez utiliser.

### <a name="audience-for-interactive-token-acquisition"></a>Audience pour l’acquisition de jetons interactive

Si votre application de bureau utilise l’authentification interactive, vous pouvez connecter des utilisateurs depuis n’importe quel [type de compte](quickstart-register-app.md).

### <a name="audience-for-desktop-app-silent-flows"></a>Audience pour les flux silencieux d’application de bureau

- Pour utiliser l’authentification Windows intégrée ou un nom d’utilisateur et un mot de passe, votre application doit connecter des utilisateurs dans votre locataire, par exemple, si vous êtes développeur d’application métier. Ou bien, dans des organisations Azure Active Directory, votre application doit se connecter aux utilisateurs de votre propre locataire s’il s’agit d’un scénario de fournisseur de logiciel indépendant. Ces flux d’authentification ne sont pas pris en charge pour des comptes Microsoft personnels.
- Si vous connectez des utilisateurs avec des identités de réseaux sociaux qui passent par une stratégie et une autorité entreprise-client (B2C), vous pouvez uniquement utiliser l’authentification interactive par mot de passe et nom d’utilisateur.

## <a name="redirect-uris"></a>URI de redirection

Les URI de redirection à utiliser dans une application de bureau dépendent du flux que vous voulez utiliser.

- Si vous utilisez l’authentification interactive ou le flux de code d’appareil, utilisez `https://login.microsoftonline.com/common/oauth2/nativeclient`. Pour obtenir cette configuration, sélectionnez l’URL correspondante dans la section **Authentification** pour votre application.

  > [!IMPORTANT]
  > L’utilisation de `https://login.microsoftonline.com/common/oauth2/nativeclient` comme URI de redirection est recommandée comme bonne pratique de sécurité.  Si aucun URI de redirection n’est spécifié, MSAL.NET utilise `urn:ietf:wg:oauth:2.0:oob` par défaut, ce qui n’est pas recommandé.  Ce paramètre par défaut sera mis à jour en tant que changement cassant dans la prochaine version majeure.

- Si vous générez une application Objective-C ou Swift native pour macOS, enregistrez l’URI de redirection en fonction de l’identificateur de bundle de votre application, au format suivant : `msauth.<your.app.bundle.id>://auth`. Remplacez `<your.app.bundle.id>` par l’identificateur de bundle de votre application.
- Si votre application n’utilise que l’authentification Windows intégrée ou un mot de passe et un nom d’utilisateur, vous n’avez pas besoin d’inscrire d’URI de redirection pour votre application. Ces flux effectuent un aller-retour vers le point de terminaison de la plateforme d’identités Microsoft v2.0. Votre application ne sera pas rappelée sur un URI spécifique.
- Pour différencier le [flux de code d’appareil](scenario-desktop-acquire-token.md#device-code-flow), l’[authentification Windows intégrée](scenario-desktop-acquire-token.md#integrated-windows-authentication) et un [mot de passe associé à un nom d’utilisateur](scenario-desktop-acquire-token.md#username-and-password) d’une application cliente confidentielle à l’aide d’un flux d’informations d’identification client utilisé dans des [applications démon](scenario-daemon-overview.md), dont aucune ne requiert d’URI de redirection, configurez-la en tant qu’application cliente publique. Pour obtenir cette configuration :

    1. Dans le <a href="https://portal.azure.com/" target="_blank">portail Azure</a>, sélectionnez votre application dans **Inscriptions d’applications**, puis sélectionnez **Authentification**.
    1. Dans **Paramètres avancés** > **Autoriser les flux de clients publics** > **Activer les flux mobiles et de bureau suivants**, sélectionnez **Oui**.

        :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Activer le paramètre client public dans le volet Authentification du portail Azure":::

## <a name="api-permissions"></a>Autorisations des API

Les applications de bureau appellent des API pour le compte de l’utilisateur connecté. Elles doivent demander des autorisations déléguées. Elles ne peuvent pas demander d’autorisations d’application, qui ne sont gérées que dans des [applications démon](scenario-daemon-overview.md).

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant de ce scénario, [Configuration du code de l’application](scenario-desktop-app-configuration.md).
