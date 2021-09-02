---
title: Configurer l’authentification Google
description: Découvrez comment configurer l’authentification Google pour en faire un fournisseur d’identité pour votre application App Service ou Azure Functions.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 46ccfd04722cb9333a8f6e546505ed1e10f657af
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532904"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>Configurer votre application App Service ou Azure Functions pour utiliser une connexion Google

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique montre comment configurer Azure App Service ou Azure Functions pour utiliser Google comme fournisseur d’authentification.

Pour effectuer la procédure décrite dans cette rubrique, vous devez disposer d'un compte Google avec une adresse électronique vérifiée. Pour créer un compte Google, accédez à [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register-your-application-with-google"></a><a name="register"> </a>Inscrire votre application auprès de Google

1. Pour créer un ID client et un secret client, suivez les instructions Google de la page [Google Sign-In for server-side apps](https://developers.google.com/identity/sign-in/web/server-side-flow) (Connexion Google pour les applications côté serveur). Il n’est pas nécessaire d’apporter des modifications au code. Utilisez simplement les informations suivantes :
    - Pour **Origines JavaScript autorisées**, utilisez `https://<app-name>.azurewebsites.net` avec le nom de votre application dans *\<app-name>* .
    - Pour **URI de redirection autorisée**, utilisez `https://<app-name>.azurewebsites.net/.auth/login/google/callback`.
1. Copiez les valeurs de l’ID d’application et du secret d’application.

    > [!IMPORTANT]
    > Le secret d’application est une information d’identification de sécurité importante. Ne partagez cette clé secrète avec personne et ne la distribuez pas dans une application cliente.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Ajouter des informations Google à votre application

1. Connectez-vous au [Portail Azure] et accédez à votre application.
1. Sélectionnez **Authentification** dans le menu de gauche. Cliquez sur **Add Identity Provider** (Ajouter un fournisseur d’identité).
1. Sélectionnez **Google** dans la liste déroulante de fournisseurs d’identité. Collez-y les valeurs d'ID d'application et de clé secrète d'application que vous avez obtenues précédemment.

    Le secret est stocké sous la forme d’un [paramètre d’application](./configure-common.md#configure-app-settings) d’emplacement fixe nommé `GOOGLE_PROVIDER_AUTHENTICATION_SECRET`. Vous pouvez mettre à jour ce paramètre ultérieurement pour utiliser des [références Key Vault](./app-service-key-vault-references.md) si vous souhaitez gérer le secret dans Azure Key Vault.

1. S’il s’agit du premier fournisseur d’identité configuré pour l’application, vous êtes également invité à fournir une section **Paramètres d’authentification App Service**. Sinon, vous pouvez passer à l’étape suivante.
    
    Ces options déterminent la manière dont votre application répond aux requêtes non authentifiées, et les sélections par défaut redirigent toutes les requêtes de connexion à l’aide de ce nouveau fournisseur. Vous pouvez modifier ce comportement maintenant ou ajuster ces paramètres ultérieurement à partir de l’écran principal **Authentification** en choisissant **Modifier** en regard de **Paramètres d’authentification**. Pour en savoir plus sur ces options, consultez [Flux d’authentification](overview-authentication-authorization.md#authentication-flow).

1. Cliquez sur **Add**.

> [Remarque] Pour ajouter une étendue : vous pouvez définir les autorisations de votre application dans le portail d’inscription du fournisseur. L’application peut demander des étendues au moment de la connexion, qui tirent parti de ces autorisations.

Vous êtes maintenant prêt à utiliser Google pour l'authentification dans votre application. Le fournisseur est répertorié sur l’écran **Authentification**. À partir de là, vous pouvez modifier ou supprimer cette configuration de fournisseur.

## <a name="next-steps"></a><a name="related-content"> </a>Étapes suivantes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure portal]: https://portal.azure.com/

