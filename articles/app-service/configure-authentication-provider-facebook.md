---
title: Configurer l’authentification Facebook - Azure App Service
description: Découvrez comment configurer l'authentification Facebook pour votre application App Services.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/06/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: e91d55c29d325301b8ac70ddc63fb408961fbb2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66742975"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Comment configurer votre application App Service de manière à utiliser la connexion via Facebook
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique montre comment configurer Azure App Service pour utiliser Facebook comme fournisseur d'authentification.

Pour effectuer la procédure de cette rubrique, vous devez disposer d'un compte Facebook avec une adresse de messagerie vérifiée et un numéro de téléphone mobile. Pour créer un compte Facebook, allez sur [facebook.com].

## <a name="register"></a>Inscription de votre application sur Facebook
1. Accédez au site Web [Développeurs Facebook] et connectez-vous à l'aide des informations d'identification de votre compte Facebook.
3. (Facultatif) Si vous n’avez pas de compte Facebook pour les développeurs, cliquez sur **Prise en main** et suivez les étapes d’inscription.
4. Cliquez sur **Mes applications** > **Ajouter une nouvelle application**.
5. Dans **Nom d’affichage**, tapez le nom unique de votre application. Indiquez également votre **E-mail de contact**, puis cliquez sur **Créer un ID d’application** et exécutez la vérification de sécurité. Vous serez alors redirigé vers le tableau de bord du développeur pour votre nouvelle application Facebook.
6. Cliquez sur **Tableau de bord** > **Connexion Facebook** > **Configurer** > **Web**.
1. Dans le volet de navigation gauche sous **Connexion Facebook**, cliquez sur **Paramètres**.
1. Dans **URI de redirection OAuth valides**, saisissez `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` et remplacez *\<nom de l’application>* par le nom de votre application Azure App Service. Cliquez sur **Enregistrer les modifications**.
8. Dans le volet de navigation gauche, cliquez sur **Paramètres** > **De base**. Sur le champ **Clé secrète de l’application**, cliquez sur **Afficher**. Copiez les valeurs **ID d’application** et **Secret d’application**. Vous les utiliserez plus tard pour configurer votre application App Service dans Azure.
   
   > [!IMPORTANT]
   > La clé secrète de l'application est une information d'identification de sécurité importante. Ne partagez cette clé secrète avec personne et ne la distribuez pas dans une application cliente.
   > 
   > 
9. Le compte Facebook qui a été utilisé pour inscrire l'application est un administrateur de l'application. À ce stade, seuls les administrateurs peuvent se connecter à cette application. Pour authentifier d’autres comptes Facebook, cliquez sur **Révision de l’application** et activez **Rendre public \<nom-de-votre-application** pour activer l’accès public général à l’aide de l’authentification Facebook.

## <a name="secrets"></a>Ajout des informations Facebook à votre application
1. Connectez-vous au [portail Azure]et accédez à votre application App Service. Cliquez sur **Paramètres** > **Authentification / Autorisation**, et vérifiez que **l’authentification App Service** est activée, sur **On**.
2. Cliquez sur **Facebook**, collez les valeurs correspondant à l’ID et à la question secrète de l’application que vous avez obtenues précédemment et activez éventuellement les étendues nécessaires à votre application, puis cliquez sur **OK**.
   
    ![][0]
   
    Par défaut, App Service fournit une authentification, mais ne restreint pas l'accès autorisé à votre contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application.
3. (Facultatif) Pour restreindre l’accès à votre site aux seuls utilisateurs authentifiés par Facebook, définissez **Action à exécuter quand une demande n’est pas authentifiée** sur **Facebook**. Cela implique que toutes les demandes soient authentifiées. Toutes les demandes non authentifiées sont redirigées vers Facebook pour être authentifiées.
4. Lorsque vous avez terminé de configurer l’authentification, cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Facebook pour l'authentification dans votre application.

## <a name="related-content"></a>Contenu connexe
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Développeurs Facebook]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portail Azure]: https://portal.azure.com/
