---
title: Configurer des applications mobiles appelant des API web | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment créer une application mobile qui appelle des API web (configuration du code de l’application)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c5160d88e390f9bfe905dfee0684983a7a49365
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919883"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Application mobile qui appelle des API web - inscription d’application

Cet article contient les instructions d’inscription d’application pour la création d’une application mobile.

## <a name="supported-accounts-types"></a>Types de comptes pris en charge

Les types de comptes pris en charge dans les applications mobiles dépendent de l’expérience que vous souhaitez activer et des flux que vous souhaitez utiliser.

### <a name="audience-for-interactive-token-acquisition"></a>Audience pour l’acquisition de jetons interactive

La plupart des applications mobiles utilisent l’authentification interactive. Si c’est le cas, vous pouvez connecter des utilisateurs à partir de n’importe quel [type de compte](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)

### <a name="audience-for-integrated-authentication-usernamepassword-and-b2c"></a>Audience pour l’authentification intégrée, nom d’utilisateur/mot de passe et B2C

- Si vous envisagez d’utiliser l’authentification Windows intégrée (possible dans les applications UWP) ou le nom d’utilisateur/mot de passe, votre application doit se connecter aux utilisateurs de votre propre locataire (développeur LOB) ou dans les organisations Azure Active Directory (scénario ISV). Ces flux d’authentification ne sont pas pris en charge pour des comptes Microsoft personnels
- Si vous vous connectez à des utilisateurs avec des identités sociales en passant une stratégie et une autorité B2C, vous pouvez uniquement utiliser l’authentification interactive et le nom d’utilisateur-mot de passe. Le nom d’utilisateur-mot de passe est actuellement pris en charge uniquement sur Xamarin.iOS, Xamarin.Android et UWP.

Pour obtenir une vue d’ensemble, consultez [Scénarios et flux d’authentification pris en charge](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) et [Scénarios et plateformes et langues prises en charge](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="platform-configuration-and-redirect-uris"></a>Configuration de la plateforme et URI de redirection  

### <a name="interactive-authentication"></a>Authentification interactive

Lors de la création d’une application mobile à l’aide de l’authentification interactive, l’étape d’inscription la plus critique est l’URI de redirection. Il peut être défini par le biais de la [configuration de plateforme dans le panneau Authentification](https://aka.ms/MobileAppReg).

Cette expérience permet à votre application d’obtenir une authentification unique (SSO) au moyen de Microsoft Authenticator (et du portail d’entreprise Intune sur Android), ainsi que la prise en charge des stratégies de gestion des appareils.

Notez qu’il existe une expérience en version préliminaire dans le portail d’inscription des applications pour vous aider à calculer l’URI de réponse répartie pour les applications iOS et Android:

1. Dans l’inscription d’application, choisissez **Authentification**, puis sélectionnez **Essayer la nouvelle expérience**
   ![Image](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Sélectionnez **Ajouter une plateforme**
   ![Image](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Lorsque la liste des plateformes est prise en charge, sélectionnez **Image**
   ![iOS](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Entrez votre ID d’offre groupée comme demandé, puis appuyez sur **Inscrire**
   ![une image](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. L’URI de redirection est calculé pour vous.
   ![image](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Si vous préférez configurer manuellement l’URI de redirection, vous pouvez le faire au moyen du manifeste de l’application. Le format recommandé est le suivant :

- ***iOS*** :`msauth.<BUNDLE_ID>://auth` (par exemple « msauth.com.votreentreprise.appName://auth »)
- ***Android*** : `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Le hachage de signature Android peut être généré à l’aide des clés de mise en production ou de débogage par le biais de la commande KeyTool.

### <a name="username-password"></a>Nom d’utilisateur/mot de passe

Si votre application utilise uniquement le nom d’utilisateur/mot de passe, vous n’avez pas besoin d’inscrire un URI de redirection pour votre application. En effet, ce processus effectue un aller-retour vers le point de terminaison Microsoft Identity Platform v 2.0 et votre application n’est pas rappelée sur un URI spécifique. Toutefois, vous devez exprimer que votre application est une application cliente publique. Vous obtenez cette configuration en accédant à la section **Authentification** de votre application, et dans la sous-section **Paramètres avancés**, répondez **Oui** à la question **Considérer l’application comme un client public** (dans le paragraphe **Type de client par défaut**)

## <a name="api-permissions"></a>Autorisations des API

Les applications mobiles appellent des API pour le compte de l’utilisateur connecté. Votre application doit demander des permissions déléguées, également appelées « étendues ». En fonction de l’expérience souhaitée, il est possible de le faire de manière statique sur le Portail Azure, ou de manière dynamique au moment de l’exécution. Grâce à l’inscription statique des permissions, les administrateurs peuvent approuver facilement votre application. Cette méthode est recommandée.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configuration de code](scenario-mobile-app-configuration.md)
