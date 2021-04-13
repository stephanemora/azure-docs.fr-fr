---
title: Configurer l’authentification Twitter
description: Découvrez comment configurer l’authentification Twitter pour en faire un fournisseur d’identité pour votre application App Service ou Azure Functions.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 6ecce954991d9f3901c54a6f87fc803b32469862
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077973"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>Configurer votre application App Service ou Azure Functions pour utiliser une connexion Twitter

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cet article vous montre comment configurer Azure App Service ou Azure Functions pour utiliser Twitter comme fournisseur d’authentification.

Pour effectuer la procédure décrite dans cet article, vous avez besoin d’un compte Twitter avec une adresse e-mail vérifiée et un numéro de téléphone. Pour créer un compte Twitter, consultez la page [twitter.com].

## <a name="register-your-application-with-twitter"></a><a name="register"></a>Inscription de votre application avec Twitter

1. Connectez-vous au [Azure portal] et accédez à votre application. Copiez votre **URL**. Vous allez l’utiliser pour configurer votre application Twitter.
1. Accédez au site web [Twitter Developers], connectez-vous avec vos informations d’identification Twitter, puis sélectionnez **Create an app** (Créer une application).
1. Entrez le **App name** (Nom de l’application) et l’**Application description** (Description de l’application) de votre nouvelle application. Collez l’**URL** de votre application dans le champ **Website URL** (URL du site). Dans la section **Callback URLs** (URL de rappel), entrez l’URL HTTPS de votre application App Service et ajoutez le chemin d’accès `/.auth/login/twitter/callback`. Par exemple : `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. En bas de la page, saisissez au moins 100 caractères dans **Tell us how this app will be used** (Indiquez-nous comment cette application sera utilisée), puis sélectionnez **Create** (Créer). Cliquez à nouveau sur **Create** dans la fenêtre contextuelle. Les détails de l’application s’affichent.
1. Sélectionnez l’onglet **Keys and Access Tokens** .

   Prenez note de ces valeurs :
   - Clé API
   - Clé secrète API

   > [!IMPORTANT]
   > La clé secrète API est un élément important des informations d’identification en matière de sécurité. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"></a>Ajout des informations Twitter à votre application

1. Connectez-vous au [portail Azure] et accédez à votre application.
1. Sélectionnez **Authentification** dans le menu de gauche. Cliquez sur **Add Identity Provider** (Ajouter un fournisseur d’identité).
1. Sélectionnez **Twitter** dans la liste déroulante de fournisseurs d’identité. Collez les valeurs `API key` et `API secret key` que vous avez obtenues précédemment.

    Le secret est stocké sous la forme d’un [paramètre d’application](./configure-common.md#configure-app-settings) d’emplacement fixe nommé `TWITTER_PROVIDER_AUTHENTICATION_SECRET`. Vous pouvez mettre à jour ce paramètre ultérieurement pour utiliser des [références Key Vault](./app-service-key-vault-references.md) si vous souhaitez gérer le secret dans Azure Key Vault.

1. S’il s’agit du premier fournisseur d’identité configuré pour l’application, vous êtes également invité à fournir une section **Paramètres d’authentification App Service**. Sinon, vous pouvez passer à l’étape suivante.
    
    Ces options déterminent la manière dont votre application répond aux requêtes non authentifiées, et les sélections par défaut redirigent toutes les requêtes de connexion à l’aide de ce nouveau fournisseur. Vous pouvez modifier ce comportement maintenant ou ajuster ces paramètres ultérieurement à partir de l’écran principal **Authentification** en choisissant **Modifier** en regard de **Paramètres d’authentification**. Pour en savoir plus sur ces options, consultez [Flux d’authentification](overview-authentication-authorization.md#authentication-flow).

1. Cliquez sur **Add**.

Vous êtes maintenant prêt à utiliser Twitter pour l’authentification dans votre application. Le fournisseur est répertorié sur l’écran **Authentification**. À partir de là, vous pouvez modifier ou supprimer cette configuration de fournisseur.

## <a name="next-steps"></a><a name="related-content"> </a>Étapes suivantes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Twitter Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
