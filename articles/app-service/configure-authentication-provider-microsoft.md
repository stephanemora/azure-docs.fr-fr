---
title: Configurer l’authentification Microsoft
description: Découvrez comment configurer l’authentification avec un compte Microsoft pour en faire un fournisseur d’identité pour votre application App Service.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: f9158a4094b7d2ec148c2cae85decb3ad959b7c3
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671916"
---
# <a name="configure-your-app-service-app-to-use-microsoft-account-login"></a>Configurer votre application App Service pour utiliser une connexion à un compte Microsoft

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique montre comment configurer Azure App Service pour utiliser un compte Microsoft comme fournisseur d’authentification. 

## <a name="register-microsoft-account"></a>Inscription de votre application avec un compte Microsoft

1. Accédez à [**Inscriptions d’applications**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) dans le portail Azure. Si besoin, connectez-vous avec votre compte Microsoft.
1. Sélectionnez **Nouvelle inscription**, puis entrez un nom d’application.
1. Dans **URI de redirection**, sélectionnez **Web**, puis entrez `https://<app-domain-name>/.auth/login/microsoftaccount/callback supply the endpoint for your application`. Remplacez *\<app-domain-name>* par le nom de domaine de votre application.  Par exemple : `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Veillez à utiliser le schéma HTTPS dans l’URL.

1. Sélectionnez **Inscription**.
1. Copier l’**ID d’application (client)** . Vous en aurez besoin ultérieurement.
1. Dans le volet gauche, sélectionnez **Certificats et secrets** > **Nouvelle clé secrète client**. Entrez une description, sélectionnez la durée de validité, puis sélectionnez **Ajouter**.
1. Copiez la valeur qui apparaît dans la page **Certificats et secrets**. Une fois que vous avez quitté la page, elle ne s’affiche plus.

    > [!IMPORTANT]
    > Le mot de passe est une information d’identification de sécurité importante. Ne partagez le mot de passe avec personne et ne le distribuez pas dans une application cliente.

## <a name="secrets"></a>Ajout des informations de compte Microsoft à votre application App Service

1. Accédez à votre application dans le [portail Azure].
1. Sélectionnez **Paramètres** > **Authentification/Autorisation**, puis vérifiez que **l’authentification App Service** est activée, sur **On**.
1. Sous **Fournisseurs d’authentification**, sélectionnez **compte Microsoft**. Collez l’ID d’application (client) et la clé secrète client que vous avez obtenus précédemment. Activez toutes les étendues nécessaires à votre application.
1. Sélectionnez **OK**.

   App Service fournit une authentification, mais ne restreint pas l’accès autorisé au contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application.

1. (Facultatif) Pour restreindre l’accès aux utilisateurs du compte Microsoft, définissez **Action à exécuter quand une demande n’est pas authentifiée** pour **Se connecter avec un compte Microsoft**. Quand vous définissez cette fonctionnalité, votre application exige que toutes les demandes soient authentifiées. Elle redirige également toutes les demandes non authentifiées vers le compte Microsoft à des fins d’authentification.

   > [!CAUTION]
   > Cette manière de restreindre l’accès s’applique à tous les appels à votre application qui peuvent ne pas être souhaitables pour les applications qui ont une page d’accès publique disponible, comme dans de nombreuses applications monopages. Pour de telles applications, préférez **Autoriser les requêtes anonymes (aucune action)** . L’application démarre alors elle-même manuellement l’authentification. Pour plus d’informations, consultez [Flux d’authentification](overview-authentication-authorization.md#authentication-flow).

1. Sélectionnez **Enregistrer**.

Vous êtes maintenant prêt à utiliser un compte Microsoft pour l’authentification dans votre application.

## <a name="related-content"> </a>Étapes suivantes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Portail Azure]: https://portal.azure.com/
