---
title: Configurer l’authentification Azure AD
description: Découvrez comment configurer l’authentification Azure Active Directory pour en faire un fournisseur d’identité pour votre application App Service.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 09/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: b833604ce18873e22c22990a26dcbae1d9928628
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670883"
---
# <a name="configure-your-app-service-app-to-use-azure-ad-login"></a>Configurer votre application App Service pour utiliser une connexion Azure AD

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cet article vous montre comment configurer Azure App Service pour utiliser Azure Active Directory (Azure AD) en tant que fournisseur d’authentification.

> [!NOTE]
> À ce jour, Azure App Service et Azure Functions sont uniquement pris en charge par Azure AD v1.0. Ces solutions ne sont pas prises en charge par la [plateforme d’identité Microsoft v2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-overview), qui comprend les bibliothèques d’authentification Microsoft (MSAL).

Adoptez ces bonnes pratiques pour configurer votre application et l’authentification :

- Donnez à chaque application App Service ses propres autorisations et son propre consentement.
- Configurez chaque application App Service avec sa propre inscription.
- Évitez de partager des autorisations entre des environnements en utilisant des inscriptions d’application distinctes pour des emplacements de déploiement distincts. Dans le cadre des tests d’un nouveau code, cette pratique peut permettre d’éviter que des problèmes n’affectent l’application de production.

## <a name="express"> </a>Configurer avec des paramètres express

1. Dans le [portail Azure], accédez à votre application App Service.
1. Sélectionnez **Paramètres** > **Authentification/Autorisation** dans le volet gauche, puis vérifiez que **l’authentification App Service** est activée, sur **On**.
1. Sélectionnez **Azure Active Directory**, puis **Rapide** sous **Mode de gestion**.
1. Cliquez sur **OK** pour inscrire l'application App Service auprès d'Azure Active Directory. Une nouvelle inscription d’application est créée.

   Si vous préférez choisir une inscription d’application existante :

   1. Choisissez **Sélectionner une application existante**, puis recherchez le nom d’une inscription d’application précédemment créée au sein de votre locataire.
   1. Sélectionnez l’inscription d’application, puis sélectionnez **OK**.
   1. Sélectionnez ensuite **OK** dans la page des paramètres Azure Active Directory.

   Par défaut, App Service fournit une authentification, mais ne restreint pas l’accès autorisé au contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application.
1. (Facultatif) Pour restreindre l’accès à votre application aux seuls utilisateurs authentifiés par Azure Active Directory, définissez **Action à exécuter quand une demande n’est pas authentifiée** sur **Se connecter avec Azure Active Directory**. Quand vous définissez cette fonctionnalité, votre application exige que toutes les demandes soient authentifiées. Elle redirige également toutes les demandes non authentifiées vers Azure Active Directory à des fins d’authentification.

    > [!CAUTION]
    > Cette manière de restreindre l’accès s’applique à tous les appels à votre application qui peuvent ne pas être souhaitables pour les applications qui ont une page d’accès publique disponible, comme dans de nombreuses applications monopages. Pour de telles applications, préférez **Autoriser les requêtes anonymes (aucune action)** . L’application démarre alors elle-même manuellement la connexion. Pour plus d’informations, consultez [Flux d’authentification](overview-authentication-authorization.md#authentication-flow).
1. Sélectionnez **Enregistrer**.

## <a name="advanced"> </a>Configurer avec des paramètres avancés

Vous pouvez configurer les paramètres de l’application manuellement si vous voulez utiliser un locataire Azure AD différent de celui que vous utilisez pour vous connecter à Azure. Pour effectuer cette configuration personnalisée, vous devez :

1. Créer une inscription dans Azure AD.
1. Fournir certains des détails d’inscription à App Service.

### <a name="register"> </a>Créer une inscription d’application dans Azure AD pour votre application App Service

Vous avez besoin des informations suivantes quand vous configurez votre application App Service :

- ID client
- ID client
- Clé secrète client (facultative)
- URI d’ID d’application

Procédez comme suit :

1. Connectez-vous au [portail Azure] et accédez à votre application App Service. Notez l’**URL** de votre application. Vous allez l’utiliser pour configurer l’inscription de votre application Azure Active Directory.
1. Sélectionnez **Azure Active Directory** > **Inscriptions d’applications** > **Nouvelle inscription**.
1. Sur la page **Inscrire une application**, entrez un **Nom** pour votre inscription d'application.
1. Dans **URI de redirection**, sélectionnez **Web**, entrez l’URL de votre application App Service, puis ajoutez le chemin `/.auth/login/aad/callback`. Par exemple : `https://contoso.azurewebsites.net/.auth/login/aad/callback`. 
1. Sélectionnez **Create** (Créer).
1. Une fois l’inscription d’application créée, copiez l’**ID de l’application (client)** et l’**ID de l’annuaire (locataire)** pour plus tard.
1. Sélectionnez **Personnalisation**. Dans **URL de la page d’accueil**, entrez l’URL de votre application App Service, puis sélectionnez **Enregistrer**.
1. Sélectionnez **Exposer une API** > **Définir**. Collez l’URL de votre application App Service, puis sélectionnez **Enregistrer**.

   > [!NOTE]
   > Cette valeur correspond à l'**URI d'ID d'application** de votre inscription d'application. Si votre application web nécessite un accès à une API dans le cloud, vous avez besoin de l’**URI d’ID d’application** de l’application web lorsque vous configurez la ressource App Service cloud. Vous pouvez utiliser cette valeur, par exemple, si vous souhaitez que le service cloud accorde explicitement l’accès à l’application web.

1. sélectionner **Ajouter une étendue**.
   1. Dans **Nom de l’étendue**, entrez *user_impersonation*.
   1. Dans les zones de texte, entrez le nom et la description de l’étendue de consentement que vous voulez que les utilisateurs voient dans la page de consentement. Par exemple, entrez *Accéder à mon application*. 
   1. Sélectionnez **Ajouter une étendue**.
1. (Facultatif) Pour créer une clé secrète client, sélectionnez **Certificats et secrets** > **Nouvelle clé secrète client** > **Ajouter**. Copiez la valeur de la clé secrète client qui s'affiche sur la page. Elle ne s’affichera plus.
1. (Facultatif) Pour ajouter plusieurs **URL de réponse**, sélectionnez **Authentification**.

### <a name="secrets"> </a>Ajout d'informations Azure Active Directory à votre application App Service

1. Dans le [portail Azure], accédez à votre application App Service. 
1. Sélectionnez **Paramètres > Authentification/Autorisation** dans le volet gauche, puis vérifiez que l’**authentification App Service** est activée, sur **On**.
1. (Facultatif) Par défaut, l’authentification App Service autorise l’accès non authentifié à votre application. Pour appliquer l'authentification utilisateur, définissez **Mesure à prendre quand une requête n’est pas authentifiée**, sur **Se connecter avec Azure Active Directory**.
1. Sous Fournisseurs d’authentification, cliquez sur **Azure Active Directory**.
1. Dans **Mode d'administration**, sélectionnez **Avancé** et configurez l'authentification App Service selon le tableau suivant :

    |Champ|Description|
    |-|-|
    |ID client| Utilisez l'**ID d’application (client)** de l’inscription de l’application. |
    |ID d'émetteur| Utilisez `https://login.microsoftonline.com/<tenant-id>` et remplacez *\<tenant-id>* par l'**ID de l'annuaire (locataire)** de l'inscription de l'application. |
    |Clé secrète client (facultative)| Utilisez la clé secrète client que vous avez générée lors de l’inscription de l’application.|
    |Audiences de jeton autorisées| S’il s’agit d’une application cloud ou serveur et que vous souhaitez autoriser les jetons d’authentification d’une application web, ajoutez l’**URI d’ID d’application** de l’application web ici. |

    > [!NOTE]
    > L’**ID client** configuré est *toujours* implicitement considéré comme étant une audience autorisée, quelle que soit la façon dont vous avez configuré les **audiences de jeton autorisées**.
1. Sélectionnez **OK**, puis cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Azure Active Directory à des fins d’authentification dans votre application App Service.

## <a name="configure-a-native-client-application"></a>Configurer une application cliente native

Vous pouvez inscrire des clients natifs pour autoriser l’authentification à l’aide d’une bibliothèque de client comme la **Bibliothèque d’authentification Active Directory**.

1. Dans le [portail Azure], sélectionnez **Active Directory** > **Inscriptions d’applications** > **Nouvelle inscription**.
1. Sur la page **Inscrire une application**, entrez un **Nom** pour votre inscription d'application.
1. Dans **URI de redirection**, sélectionnez **Client public (mobile et bureau)** , entrez l’URL de votre application App Service, puis ajoutez le chemin `/.auth/login/aad/callback`. Par exemple : `https://contoso.azurewebsites.net/.auth/login/aad/callback`.
1. Sélectionnez **Create** (Créer).

    > [!NOTE]
    > Pour une application Windows, utilisez plutôt le [SID de package](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) en tant qu’URI.
1. Une fois l’inscription d’application créée, copiez la valeur de l’**ID d’application (client)** .
1. Sélectionnez **Autorisations des API** > **Ajouter une autorisation** > **Mes API**.
1. Sélectionnez l’inscription d'application que vous avez créée précédemment pour votre application App Service. Si celle-ci n’apparaît pas, vérifiez que vous avez ajouté l’étendue **user_impersonation** dans [Créer une inscription d’application dans Azure AD pour votre application App Service](#register).
1. Sélectionnez **user_impersonation**, puis **Ajouter des autorisations**.

Vous avez maintenant configuré une application cliente native qui peut accéder à votre application App Service.

## <a name="related-content"> </a>Étapes suivantes

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
