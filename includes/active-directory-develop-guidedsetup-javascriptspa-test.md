---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 0c43fb72829614af339f7dcc1bb36edfc80e6c80
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121730"
---
## <a name="test-your-code"></a>Test de votre code

### <a name="test-with-node"></a>Effectuer des tests avec Node

Si vous n’utilisez pas Visual Studio, vérifiez que votre serveur web est démarré.

1. Configurez le serveur pour écouter un port TCP basé sur l’emplacement de votre fichier **index.html**. Pour Node, démarrez le serveur web afin d’écouter le port en exécutant les commandes ci-après dans une invite de ligne de commande à partir du dossier d’application :

    ```bash
    npm install
    node server.js
    ```
1. Ouvrez le navigateur et tapez http://<span></span>localhost:30662 ou http://<span></span>localhost:{port}, où **port** correspond au port qu’écoute votre serveur web. Vous devez voir apparaître le contenu de votre fichier index.html, ainsi que le bouton **Se connecter**.

<p><!-- -->

### <a name="test-with-visual-studio"></a>Effectuer des tests avec Visual Studio

Si vous utilisez Visual Studio, veillez à sélectionner la solution de projet, puis appuyez sur **F5** pour exécuter votre projet. Le navigateur accède à l’emplacement http://<span></span>localhost:{port}, et vous voyez le bouton **Se connecter**.

## <a name="test-your-application"></a>Tester votre application

Une fois que le navigateur a chargé votre fichier index.html, cliquez sur **Se connecter**. Vous êtes invité à vous connecter avec le point de terminaison de la plateforme d’identités Microsoft :

![Connexion au compte JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Accorder les droits d’accès à l’application

La première fois que vous vous connectez à votre application, vous êtes invité à autoriser l’application à accéder à votre profil et à vous connecter :

![Donner votre accord pour l’accès par l’application](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Afficher les résultats de l’application

Une fois connecté, vous devez voir apparaître sur la page vos informations de profil utilisateur renvoyées dans la réponse de l’API Microsoft Graph.

![Résultats attendus de l’appel à l’API Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Informations supplémentaires sur les étendues et les autorisations déléguées

L’API Microsoft Graph nécessite l’étendue `user.read` pour lire le profil d’un utilisateur. Par défaut, cette étendue est automatiquement ajoutée à toutes les applications inscrites dans le portail d’inscription. D’autres API pour Microsoft Graph ainsi que des API personnalisées pour votre serveur principal peuvent nécessiter des étendues supplémentaires. Par exemple, l’API Microsoft Graph requiert l’étendue `Calendars.Read` pour répertorier les calendriers de l’utilisateur.

Pour accéder aux calendriers de l’utilisateur dans le contexte d’une application, ajoutez l’autorisation déléguée `Calendars.Read` aux informations d’inscription de l’application. Ensuite, ajoutez l’étendue `Calendars.Read` à l’appel `acquireTokenSilent`.

>[!NOTE]
>L’utilisateur peut être invité à donner des consentements supplémentaires à mesure que vous augmentez le nombre d’étendues.

Si une API principale ne nécessite pas d’étendue (non recommandé), vous pouvez utiliser `clientId` en tant qu’étendue dans les appels pour acquérir des jetons.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
