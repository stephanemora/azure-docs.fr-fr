---
title: Configurer l’authentification Twitter
description: Découvrez comment configurer l’authentification Twitter pour en faire un fournisseur d’identité pour votre application App Service.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 04/19/2018
ms.custom: seodec18
ms.openlocfilehash: b5ec17c18cec8053f0732366c0cc5d0c5003e4de
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670798"
---
# <a name="configure-your-app-service-app-to-use-twitter-login"></a>Configurer votre application App Service pour utiliser une connexion Twitter

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cet article vous montre comment configurer Azure App Service pour utiliser Twitter comme fournisseur d’authentification.

Pour effectuer la procédure décrite dans cet article, vous avez besoin d’un compte Twitter avec une adresse e-mail vérifiée et un numéro de téléphone. Pour créer un compte Twitter, consultez la page [twitter.com].

## <a name="register"></a>Inscription de votre application avec Twitter

1. Connectez-vous au [portail Azure] et accédez à votre application. Copiez votre **URL**. Vous allez l’utiliser pour configurer votre application Twitter.
1. Accédez au site web [Twitter Developers], connectez-vous avec vos identifiants Twitter, puis sélectionnez **Create New App** (Créer une application).
1. Entrez le **nom** et la **description** de votre nouvelle application. Collez l’**URL** de votre application dans le champ **Site web**. Dans le champ **URL de rappel**, entrez l’URL de votre application App Service et ajoutez le chemin `/.auth/login/aad/callback`. Par exemple : `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Veillez à utiliser le schéma HTTPS.
1. Au bas de la page, lisez et acceptez les termes du contrat. Sélectionnez **Create your Twitter application** (Créer votre application Twitter). Les détails de l’application s’affichent.
1. Sélectionnez l’onglet **Paramètres**, cochez **Autoriser la connexion à Twitter via cette application**, puis sélectionnez **Mettre à jour les paramètres**.
1. Sélectionnez l’onglet **Keys and Access Tokens** .

   Prenez note de ces valeurs :
   - Consumer Key (API Key)
   - Consumer Secret (API Secret)

   > [!NOTE]
   > La clé secrète consommateur est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.

## <a name="secrets"></a>Ajout des informations Twitter à votre application

1. Accédez à votre application dans le [portail Azure].
1. Sélectionnez **Paramètres** > **Authentification/Autorisation**, puis vérifiez que **l’authentification App Service** est activée, sur **On**.
1. Sélectionnez **Twitter**.
1. Collez les valeurs `API Key` et `API Secret` que vous avez obtenues précédemment.
1. Sélectionnez **OK**.

   ![Capture d’écran des paramètres Twitter de l’application mobile][1]

   Par défaut, App Service fournit une authentification, mais ne restreint pas l’accès autorisé au contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application.

1. (Facultatif) Pour restreindre l’accès à votre site aux seuls utilisateurs authentifiés par Twitter, définissez **Action à exécuter quand une demande n’est pas authentifiée** sur **Twitter**. Quand vous définissez cette fonctionnalité, votre application exige que toutes les demandes soient authentifiées. Elle redirige également toutes les demandes non authentifiées vers Twitter à des fins d’authentification.

   > [!CAUTION]
   > Cette manière de restreindre l’accès s’applique à tous les appels à votre application qui peuvent ne pas être souhaitables pour les applications qui ont une page d’accès publique disponible, comme dans de nombreuses applications monopages. Pour de telles applications, préférez **Autoriser les requêtes anonymes (aucune action)** . L’application démarre alors elle-même manuellement l’authentification. Pour plus d’informations, consultez [Flux d’authentification](overview-authentication-authorization.md#authentication-flow).

1. Sélectionnez **Enregistrer**.

Vous êtes maintenant prêt à utiliser Twitter pour l'authentification dans votre application.

## <a name="related-content"> </a>Étapes suivantes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Portail Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
