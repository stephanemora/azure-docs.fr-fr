---
title: Configurer l’authentification Facebook
description: Découvrez comment configurer l’authentification Facebook pour en faire un fournisseur d’identité pour votre application App Service ou Azure Functions.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: bc639eaea76b3309d6ed047e73c726040da19639
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078007"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Configurer votre application App Service ou Azure Functions pour utiliser une connexion Facebook

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cet article vous montre comment configurer Azure App Service ou Azure Functions pour utiliser Facebook comme fournisseur d’authentification.

Pour effectuer la procédure de cet article, vous avez besoin d’un compte Facebook avec une adresse e-mail vérifiée et un numéro de téléphone mobile. Pour créer un compte Facebook, allez sur [facebook.com].

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Inscrire votre application auprès de Facebook

1. Accédez au site web [Développeurs Facebook] et connectez-vous à l’aide des informations d’identification de votre compte Facebook.

   Si vous n’avez pas de compte Facebook pour les développeurs, sélectionnez **Prise en main** et suivez les étapes d’inscription.
1. Sélectionnez **Mes applications** > **Ajouter une nouvelle application**.
1. Dans le champ **Nom d’affichage** :
   1. Tapez un nom unique pour votre application.
   1. Indiquez votre **E-mail du contact**.
   1. Sélectionnez **Créer un ID d'application**.
   1. Effectuez la vérification de sécurité.

   Le tableau de bord du développeur pour votre nouvelle application Facebook s’ouvre.
1. Sélectionnez **Tableau de bord** > **Connexion Facebook** > **Configurer** > **Web**.
1. Dans le volet de navigation de gauche, sous **Connexion Facebook**, sélectionnez **Paramètres**.
1. Dans le champ **URI de redirection OAuth Valides**, entrez `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`. N’oubliez pas de remplacer `<app-name>` par le nom de votre application Azure App Service.
1. Sélectionnez **Enregistrer les modifications**.
1. Dans le volet gauche, sélectionnez **Paramètres** > **De base**. 
1. Dans le champ **Secret d’application**, sélectionnez **Afficher**. Copiez les valeurs **ID d’application** et **Secret d’application**. Vous les utiliserez plus tard pour configurer votre application App Service dans Azure.

   > [!IMPORTANT]
   > La clé secrète de l'application est une information d'identification de sécurité importante. Ne partagez cette clé secrète avec personne et ne la distribuez pas dans une application cliente.
   >

1. Le compte Facebook que vous avez utilisé pour inscrire l'application est un administrateur de l'application. À ce stade, seuls les administrateurs peuvent se connecter à cette application.

   Pour authentifier d’autres comptes Facebook, sélectionnez **Révision de l’application** et activez **Rendre \<your-app-name> public** pour activer l’accès public général à l’application à l’aide de l’authentification Facebook.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Ajouter des informations Facebook à votre application

1. Connectez-vous au [Portail Azure] et accédez à votre application.
1. Sélectionnez **Authentification** dans le menu de gauche. Cliquez sur **Add Identity Provider** (Ajouter un fournisseur d’identité).
1. Sélectionnez **Facebook** dans la liste déroulante de fournisseurs d’identité. Collez-y les valeurs d'ID d'application et de clé secrète d'application que vous avez obtenues précédemment.

    Le secret est stocké sous la forme d’un [paramètre d’application](./configure-common.md#configure-app-settings) d’emplacement fixe nommé `FACEBOOK_PROVIDER_AUTHENTICATION_SECRET`. Vous pouvez mettre à jour ce paramètre ultérieurement pour utiliser des [références Key Vault](./app-service-key-vault-references.md) si vous souhaitez gérer le secret dans Azure Key Vault.

1. S’il s’agit du premier fournisseur d’identité configuré pour l’application, vous êtes également invité à fournir une section **Paramètres d’authentification App Service**. Sinon, vous pouvez passer à l’étape suivante.
    
    Ces options déterminent la manière dont votre application répond aux requêtes non authentifiées, et les sélections par défaut redirigent toutes les requêtes de connexion à l’aide de ce nouveau fournisseur. Vous pouvez modifier ce comportement maintenant ou ajuster ces paramètres ultérieurement à partir de l’écran principal **Authentification** en choisissant **Modifier** en regard de **Paramètres d’authentification**. Pour en savoir plus sur ces options, consultez [Flux d’authentification](overview-authentication-authorization.md#authentication-flow).

1. (Facultatif) Cliquez sur **Suivant : Étendues** et ajoutez les étendues nécessaires à l’application. Celles-ci seront demandées au moment de la connexion pour les flux basés sur un navigateur.
1. Cliquez sur **Add**.

Vous êtes maintenant prêt à utiliser Facebook pour l’authentification dans votre application. Le fournisseur est répertorié sur l’écran **Authentification**. À partir de là, vous pouvez modifier ou supprimer cette configuration de fournisseur.

## <a name="next-steps"></a><a name="related-content"> </a>Étapes suivantes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->
[Développeurs Facebook]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure portal]: https://portal.azure.com/
