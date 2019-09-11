---
title: Configurer l’authentification Azure Active Directory - Azure App Service
description: Découvrez comment configurer l'authentification Azure Active Directory pour votre application App Service.
author: cephalin
services: app-service
documentationcenter: ''
manager: gwallace
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service
ms.workload: web,mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/03/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 8b4b6549f9553773cc44c311f49befbb3eec9dc9
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233095"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>Configurer votre application App Service pour utiliser une connexion Azure Active Directory

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

> [!NOTE]
> Actuellement, AAD V2 (notamment MSAL) n’est pas pris en charge pour Azure App Service et Azure Functions.
>

Cet article montre comment configurer Azure App Service pour utiliser Azure Active Directory en tant que fournisseur d’authentification.

Il vous est recommandé de configurer chaque instance d’App Service avec sa propre inscription. Ainsi, elle a ses propres autorisations et son propre consentement. Pensez également à utiliser des inscriptions d’applications distinctes pour des emplacements de déploiement distincts. Cela permet d’éviter le partage des autorisations entre environnements. Ainsi, en cas de problème dans le nouveau code que vous testez, l’environnement de production n’est pas affecté.

## <a name="express"> </a>Configurer avec des paramètres express

1. Dans le [portail Azure], accédez à votre application App Service. Dans la barre de navigation gauche, sélectionnez **Authentification / Autorisation**.
2. Si l'option **Authentification / Autorisation** n'est pas activée, sélectionnez **Activer**.
3. Sélectionnez **Azure Active Directory**, puis **Rapide** sous **Mode de gestion**.
4. Cliquez sur **OK** pour inscrire l'application App Service auprès d'Azure Active Directory. Une nouvelle inscription d’application est créée. Si vous choisissez une inscription d'app existante à la place, cliquez sur **Sélectionner une application existante** et recherchez le nom d’une inscription d'app précédemment créée au sein de votre client. Cliquez sur l'inscription d'app pour la sélectionner, puis sur **OK**. Cliquez ensuite sur **OK** dans la page des paramètres Azure Active Directory.
Par défaut, App Service fournit une authentification, mais ne restreint pas l'accès autorisé à votre contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application.
5. (Facultatif) Pour restreindre l’accès à votre application aux seuls utilisateurs authentifiés par Azure Active Directory, définissez **Mesure à prendre quand une requête n’est pas authentifiée** sur **Se connecter avec Azure Active Directory**. Cela implique que toutes les demandes soient authentifiées. Toutes les demandes non authentifiées sont redirigées vers Azure Active Directory pour être authentifiées.

    > [!NOTE]
    > Cette manière de restreindre l’accès s’applique à tous les appels à votre application qui peuvent ne pas être souhaitables pour les applications souhaitant une page d’accès publique disponible, comme dans de nombreuses applications à page unique. Pour de telles applications **Autoriser les demandes anonymes (aucune action)** peut être préféré. L’application démarre alors elle-même manuellement la connexion, comme décrit [ici](overview-authentication-authorization.md#authentication-flow).
6. Cliquez sur **Enregistrer**.

## <a name="advanced"> </a>Configurer avec des paramètres avancés

Vous pouvez également fournir manuellement des paramètres de configuration, si le locataire Azure Active Directory que vous souhaitez utiliser diffère de celui avec lequel vous vous connectez à Azure. Pour terminer la configuration, vous devez d’abord créer une inscription dans Azure Active Directory, puis fournir des informations d’inscription à App Service.

### <a name="register"> </a>Créer une inscription d’application dans Azure AD pour votre application App Service

Lorsque vous créez une inscription d’application manuellement, notez les trois informations suivantes dont vous aurez besoin au moment de la configuration de votre application App Service : ID client, ID de locataire et éventuellement, clé secrète client et URI d’ID d’application.

1. Dans le [portail Azure], accédez à votre application App Service et notez l’**URL** de votre application. Elle vous permettra de configurer l'inscription de votre application Azure Active Directory.
1. Dans le menu de gauche du [portail Azure], sélectionnez **Active Directory** > **Inscriptions d'applications** > **Nouvelle inscription**. 
1. Sur la page **Inscrire une application**, entrez un **Nom** pour votre inscription d'application.
1. Dans **URI de redirection**, sélectionnez **Web**, entrez l'URL de votre application App Service, puis ajoutez le chemin d'accès `/.auth/login/aad/callback`. Par exemple : `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Sélectionnez ensuite **Créer**.
1. Une fois l’inscription d'application créée, copiez l'**ID de l'application (client)** et l'**ID de l'annuaire (locataire)** pour plus tard.
1. Sélectionnez **Personnalisation**. Dans **URL de la page d’accueil**, entrez l'URL de votre application App Service, puis sélectionnez **Enregistrer**.
1. Sélectionnez **Exposer une API** > **Définir**. Collez l’URL de votre application App Service, puis sélectionnez **Enregistrer**.

    > [!NOTE]
    > Cette valeur correspond à l'**URI d'ID d'application** de votre inscription d'application. Si vous souhaitez qu’une application web frontale accède à une API de serveur principal, par exemple, et que le serveur principal accorde explicitement l’accès au serveur frontal, vous avez besoin de l'**URI d'ID d'application** du *serveur frontal* lorsque vous configurez la ressource d’application App Service du *serveur principal*.
1. sélectionner **Ajouter une étendue**. Dans **Nom de l'étendue**, entrez *user_impersonation*. Dans les zones de texte, entrez le nom et la description de l'étendue de consentement que vous voulez que les utilisateurs voient sur la page de consentement, comme *Accéder à mon application*. Lorsque vous avez terminé, cliquez sur **Ajouter une étendue**.
1. (Facultatif) Pour créer une clé secrète client, sélectionnez **Certificats et secrets** > **Nouvelle clé secrète client** > **Ajouter**. Copiez la valeur de la clé secrète client qui s'affiche sur la page. Lorsque vous quittez cette page, cette valeur ne s'affiche plus.
1. (Facultatif) Pour ajouter plusieurs **URL de réponse**, sélectionnez **Authentification** dans le menu.

### <a name="secrets"> </a>Ajout d'informations Azure Active Directory à votre application App Service

1. Dans le [portail Azure], accédez à votre application App Service. Dans le menu de gauche, sélectionnez **Authentification/Autorisation**. Si la fonctionnalité Authentification/Autorisation n’est pas activée, sélectionnez **Activé**. 
1. (Facultatif) Par défaut, l’authentification App Service autorise l’accès non authentifié à votre application. Pour appliquer l'authentification utilisateur, définissez **Mesure à prendre quand une requête n’est pas authentifiée**, sur **Se connecter avec Azure Active Directory**.
1. Sous Fournisseurs d’authentification, cliquez sur **Azure Active Directory**.
1. Dans **Mode d'administration**, sélectionnez **Avancé** et configurez l'authentification App Service selon le tableau suivant :

    |Champ|Description|
    |-|-|
    |ID client| Utilisez l'**ID d’application (client)** de l’inscription de l’application. |
    |ID d'émetteur| Utilisez `https://login.microsoftonline.com/<tenant-id>` et remplacez *\<tenant-id>* par l'**ID de l'annuaire (locataire)** de l'inscription de l'application. |
    |Clé secrète client (facultative)| Utilisez la clé secrète client que vous avez générée lors de l’inscription de l’application.|
    |Audiences de jeton autorisées| S’il s’agit d'une application *back-end* et que vous souhaitez autoriser les jetons d’authentification à partir d’une application frontale, ajoutez l'**URI d'ID d'application** du *serveur frontal* ici. |
1. Sélectionnez **OK**, puis cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Azure Active Directory pour l'authentification dans votre application App Service.

## <a name="configure-a-native-client-application"></a>Configurer une application cliente native
Vous pouvez inscrire des clients natifs si vous souhaitez effectuer des connexions à l’aide d’une bibliothèque de client telle que **Bibliothèque d'authentification Active Directory**.

1. Dans le menu de gauche du [portail Azure], sélectionnez **Active Directory** > **Inscriptions d'applications** > **Nouvelle inscription**. 
1. Sur la page **Inscrire une application**, entrez un **Nom** pour votre inscription d'application.
1. Dans **URI de redirection**, sélectionnez **Client public (mobile et bureau)** , entrez l’URL de votre application App Service, puis ajoutez le chemin d'accès `/.auth/login/aad/callback`. Par exemple : `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Sélectionnez ensuite **Créer**.

    > [!NOTE]
    > Pour une application Windows, utilisez plutôt le [SID de package](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) en tant qu’URI.
1. Une fois l’inscription de l’application créée, copiez la valeur de l'**ID d’application (client)** .
1. Dans le menu de gauche, sélectionnez **Autorisations des API** > **Ajouter une autorisation** > **Mes API**.
1. Sélectionnez l’inscription d'application que vous avez créée précédemment pour votre application App Service. Si celle-ci n'apparaît pas, vérifiez que vous avez ajouté l'étendue **user_impersonation** dans [Créer une inscription d’application dans Azure AD pour votre application App Service](#register).
1. Sélectionnez **user_impersonation**, puis cliquez sur **Ajouter des autorisations**.

Vous avez maintenant configuré une application cliente native qui peut accéder à votre application App Service.

## <a name="related-content"></a>Contenu connexe

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/configure-authentication-provider-aad/app-service-webapp-auth-config.png



<!-- URLs. -->

[Portail Azure]: https://portal.azure.com/
[alternative method]:#advanced
