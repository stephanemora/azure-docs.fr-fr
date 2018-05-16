---
title: Comment configurer l'authentification Google pour votre application App Services
description: Découvrez comment configurer l'authentification Google pour votre application App Services.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.openlocfilehash: 1a174913446c0a1d5e3e3b01123db8b40bfd172c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Comment configurer votre application App Service de manière à utiliser la connexion via Google
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique montre comment configurer Azure App Service pour utiliser Google comme fournisseur d’authentification.

Pour effectuer la procédure décrite dans cette rubrique, vous devez disposer d'un compte Google avec une adresse électronique vérifiée. Pour créer un compte Google, accédez à [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"></a>Inscription de votre application avec Google
1. Connectez-vous au [portail Azure]et accédez à votre application. Copiez votre **URL**, que vous utiliserez plus tard pour configurer votre application de Google.
2. Rendez-vous sur le site Web [API Google](http://go.microsoft.com/fwlink/p/?LinkId=268303), connectez-vous avec votre compte Google, cliquez sur **Créer un projet**, saisissez un **Nom de projet**, puis cliquez sur **Créer**.
3. Une fois que le projet est créé, sélectionnez-le. Dans le tableau de bord du projet, cliquez sur **Go to APIs overview** (Aller à la vue d’ensemble des API).
4. Sélectionnez **Enable APIs and services** (Activer les API et services). Recherchez **Google+API**, puis sélectionnez cette entrée. Cliquez sur **Activer**.
6. Dans la barre de navigation à gauche, cliquez sur **Informations d’identification** > **Écran de consentement OAuth**, puis sélectionnez votre **Adresse de messagerie**, entrez un **Nom de produit** et cliquez sur **Enregistrer**.
7. Dans l’onglet **Informations d’identification**, cliquez sur **Créer des informations d’identification** > **ID client OAuth**. Cliquez sur **Configure consent screen** (Configurer l’écran de consentement), puis indiquez un **Nom de produit**. Cliquez ensuite sur **Enregistrer**
8. Dans l’écran « Create client ID » (Créer un ID client), sélectionnez **Application web**.
9. Collez **l’URL** App Service que vous avez copiée précédemment dans **Origines JavaScript autorisées**, puis collez l’URI de redirection dans **URI de redirection autorisée**. L’URI de redirection correspond à l’URL de votre application suivie du chemin d’accès, */.auth/login/google/callback*. Par exemple : `https://contoso.azurewebsites.net/.auth/login/google/callback`. Assurez-vous d'utiliser le schéma HTTPS. Cliquez ensuite sur **Créer**.
10. Sur l'écran suivant, notez les valeurs de l'ID du client et de la clé secrète client.

    > [!IMPORTANT]
    > La clé secrète client est une information d'identification de sécurité importante. Ne partagez cette clé secrète avec personne et ne la distribuez pas dans une application cliente.


## <a name="secrets"></a>Ajout des informations Google à votre application
1. Revenez au [portail Azure]et accédez à votre application. Cliquez sur **Paramètres**, puis sur **Authentification/Autorisation**.
2. Si la fonctionnalité Authentification / Autorisation n’est pas activée, positionnez le commutateur sur **On**.
3. Cliquez sur **Google**. Collez les valeurs d’ID de l’application et App Secret que vous avez obtenues précédemment et activez éventuellement les étendues que votre application requiert. Cliquez ensuite sur **OK**.
   
   ![][1]
   
   Par défaut, App Service fournit une authentification, mais ne restreint pas l'accès autorisé à votre contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application.
4. (Facultatif) Pour restreindre l’accès à votre site aux seuls utilisateurs authentifiés par Google, définissez **Action à exécuter quand une demande n’est pas authentifiée** sur **Google**. Cela implique que toutes les demandes soient authentifiées. Toutes les demandes non authentifiées sont redirigées vers Google pour être authentifiées.
5. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Google pour l'authentification dans votre application.

## <a name="related-content"></a>Contenu connexe
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[portail Azure]: https://portal.azure.com/

