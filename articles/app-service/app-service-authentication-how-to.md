---
title: Personnaliser les paramètres d’authentification et d’autorisation dans Azure App Service | Microsoft Docs
description: Découvrez comment personnaliser les paramètres d’authentification et d’autorisation dans App Service, mais aussi obtenir des revendications d’utilisateur et des jetons distincts.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2018
ms.author: cephalin
ms.openlocfilehash: c41cb3ef2939fe7271b1f8738fcf0cb95c4b1111
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763140"
---
# <a name="customize-authentication-and-authorization-in-azure-app-service"></a>Personnaliser les paramètres d’authentification et d’autorisation dans Azure App Service

Cet article vous explique comment personnaliser [les paramètres d’authentification et d’autorisation dans App Service](app-service-authentication-overview.md), et comment gérer les identités à partir de votre application. 

Pour commencer rapidement, consultez l’un des didacticiels suivants :

* [Didacticiel : Authentifier et autoriser les utilisateurs de bout en bout dans Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)
* [Didacticiel : Authentifier et autoriser les utilisateurs de bout en bout dans Azure App Service pour Linux](containers/tutorial-auth-aad.md)
* [Comment configurer votre application pour utiliser une connexion Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [Comment configurer votre application pour utiliser une connexion Facebook](app-service-mobile-how-to-configure-facebook-authentication.md)
* [Comment configurer votre application pour utiliser une connexion Google](app-service-mobile-how-to-configure-google-authentication.md)
* [Comment configurer votre application pour utiliser une connexion par compte Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [Comment configurer votre application pour utiliser une connexion Twitter](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="configure-multiple-sign-in-options"></a>Configurer des options de connexion multiples

La configuration du portail ne permet pas par défaut de présenter plusieurs options de connexion à vos utilisateurs (tels que Facebook et Twitter). Toutefois, il est relativement simple d’ajouter cette fonctionnalité à votre application web. Voici la procédure à suivre :

Tout d’abord, sur la page **Authentification / Autorisation** du portail Azure, configurez chaque fournisseur d’identité que vous souhaitez activer.

Sous **Mesure à prendre quand une demande n’est pas authentifiée**, sélectionnez **Autoriser les requêtes anonymes (aucune action)**.

Sur la page de connexion, dans la barre de navigation ou tout autre emplacement de votre application web, ajoutez un lien de connexion pour chacun des fournisseurs que vous avez activés (`/.auth/login/<provider>`). Par exemple : 

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Lorsque l’utilisateur clique sur l’un des liens, la page de connexion respective s’ouvre pour que l’utilisateur se connecte.

## <a name="access-user-claims"></a>Accéder aux revendications d’utilisateur

App Service transmet des revendications d’utilisateur à votre application au moyen d’en-têtes spéciaux. Les demandes externes ne sont pas autorisées à définir ces en-têtes. Ces derniers ne s’affichent donc que s’ils sont définis par App Service. Voici quelques exemples d’en-têtes :

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Tout code, quels que soient le langage ou l’infrastructure utilisés, peut trouver les informations qu’il recherche dans ces en-têtes. Dans le cas d’applications ASP.NET 4.6, le paramètre **ClaimsPrincipal** est automatiquement défini sur les valeurs appropriées.

Votre application peut également obtenir des détails supplémentaires sur l’utilisateur authentifié en appelant `/.auth/me`. Les Kits de développement logiciel (SDK) serveur de Mobile Apps offrent des méthodes d’assistance permettant de manipuler ces données. Pour plus d’informations, consultez [Comment utiliser le Kit de développement logiciel Node.js dans Azure Mobile Apps](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity), et [Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Récupérer des jetons dans le code d’application

À partir de votre code serveur, les jetons spécifiques au fournisseur sont ajoutés dans l’en-tête de requête, afin que vous puissiez y accéder facilement. Le tableau suivant présente les noms d’en-tête de jeton possibles :

| | |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Jeton Facebook | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Compte Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

À partir de votre code client (par exemple, une application mobile ou un navigateur JavaScript), envoyez une requête `GET` HTTP à `/.auth/me`. La réponse JSON retournée contient les jetons spécifiques au fournisseur.

> [!NOTE]
> Les jetons d’accès permettent d’accéder aux ressources du fournisseur. Ils ne s’affichent donc que si vous configurez votre fournisseur avec une clé secrète client. Pour savoir comment obtenir des jetons d’actualisation, consultez [Actualiser des jetons d’accès](#refresh-access-tokens).

## <a name="refresh-access-tokens"></a>Actualiser des jetons d’accès

Lorsque le jeton d’accès de votre fournisseur a expiré, vous devez réauthentifier l’utilisateur. Vous pouvez éviter l’expiration du jeton en effectuant un appel `GET` au point de terminaison `/.auth/refresh` de votre application. Lorsqu’il est appelé, App Service actualise automatiquement les jetons d’accès dans le magasin de jetons pour l’utilisateur authentifié. Les demandes de jeton suivantes effectuées via le code de votre application permettent d’obtenir les jetons actualisés. Toutefois, pour que l’actualisation des jetons soit effective, le magasin de jetons doit contenir les [jetons d’actualisation](https://auth0.com/learn/refresh-tokens/) pour votre fournisseur. La procédure pour obtenir des jetons d’actualisation est fournie par chaque fournisseur. La liste suivante en fournit toutefois un bref résumé :

- **Google** : ajouter un paramètre de chaîne de requête `access_type=offline` à votre appel d’API `/.auth/login/google`. Si vous utilisez le kit de développement logiciel Mobile Apps, vous pouvez ajouter le paramètre à l’une des surcharges `LogicAsync` (voir [Google Refresh Tokens](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens) (Jetons d’actualisation Google)).
- **Facebook** : ne fournit pas de jetons d’actualisation. Les jetons de longue durée expirent au bout de 60 jours (voir [Facebook Expiration and Extension of Access Tokens](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension) (Expiration et prolongation des jetons d’accès Facebook)).
- **Twitter** : les jetons d’accès n’expirent pas (voir [Twitter OAuth FAQ](https://developer.twitter.com/docs/basics/authentication/guides/oauth-faq) (FAQ sur l’authentification OAuth Twitter)).
- **Compte Microsoft** : au moment de [configurer les paramètres d’authentification de compte Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md), sélectionnez l’étendue `wl.offline_access`.
- **Azure Active Directory** : dans [https://resources.azure.com](https://resources.azure.com), procédez comme suit :
    1. En haut de la page, sélectionnez **Lecture/écriture**.
    1. Dans le navigateur de gauche, accédez à **abonnements** > **_\<subscription\_name_** > **resourceGroups** > _**\<resource\_group\_name>**_ > **fournisseurs** > **Microsoft.Web** > **sites** > _**\<app\_name>**_ > **config** > **authsettings**. 
    1. Cliquez sur **Modifier**.
    1. Modifiez la propriété suivante. Remplacez la valeur _\<app\_id>_ par l’ID d’application Azure Active Directory du service auquel vous souhaitez accéder.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    1. Cliquez sur **Put**. 

Une fois que votre fournisseur est configuré, vous pouvez vérifier si le magasin de jetons contient des jetons d’actualisation en appelant `/.auth/me`. 

Pour actualiser votre jeton d’accès à tout moment, il vous suffit d’appeler `/.auth/refresh` dans n’importe quel langage. L’extrait de code suivant utilise jQuery pour actualiser vos jetons d’accès à partir d’un client JavaScript.

```JavaScript
function refreshTokens() {
  var refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Si un utilisateur révoque les autorisations accordées à votre application, votre appel à `/.auth/me` peut échouer avec une réponse `403 Forbidden`. Pour diagnostiquer les erreurs, consultez vos journaux d’application pour obtenir des informations supplémentaires.

## <a name="extend-session-expiration-grace-period"></a>Étendre la période de grâce d’expiration de session

Après expiration d’une session authentifiée, une période de grâce de 72 heures s’applique par défaut. Au cours de cette période de grâce, vous pouvez actualiser le cookie ou le jeton de session via App Service sans réauthentifier l’utilisateur. Vous pouvez simplement appeler `/.auth/refresh` lorsque votre cookie ou jeton de session n’est plus valide. Vous n’avez pas besoin de vérifier vous-même les dates d’expiration des jetons. À la fin de la période de grâce de 72 heures, l’utilisateur doit se connecter à nouveau pour obtenir un jeton ou un cookie de session valide.

Si le délai de 72 heures n’est pas suffisamment long pour vous, vous pouvez étendre cette fenêtre d’expiration. Étendre la fenêtre d’expiration sur une longue période peut avoir une incidence majeure sur la sécurité (par exemple lorsqu’un jeton d’authentification est divulgué ou volé). Nous vous recommandons donc de conserver la valeur par défaut de 72 heures ou de définir la période d’extension sur la valeur minimale.

Pour étendre la fenêtre d’expiration par défaut, exécutez la commande suivante dans [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> La période de grâce s’applique uniquement à la session App Service authentifiée, et non aux jetons fournis par les fournisseurs d’identité. Il n’existe aucune période de grâce pour les jetons de fournisseur ayant expiré. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Restreindre le domaine des comptes de connexion

Les options Compte Microsoft et Azure Active Directory permettent de se connecter à partir de multiples domaines. Par exemple, l’option Compte Microsoft prend en charge les comptes _outlook.com_, _live.com_ et _hotmail.com_. Azure Active Directory prend en charge un nombre illimité de domaines personnalisés pour les comptes de connexion. Ce comportement peut ne pas être souhaitable pour une application interne pour laquelle vous souhaitez refuser l’accès à tout utilisateur disposant d’un compte _outlook.com_. Pour restreindre le nom de domaine des comptes de connexion, procédez comme suit.

Dans [https://resources.azure.com](https://resources.azure.com), accédez à **abonnements** > **_\<subscription\_name_** > **resourceGroups** > _**\<resource\_group\_name>**_ > **fournisseurs** > **Microsoft.Web** > **sites** > _**\<app\_name>**_ > **config** > **authsettings**. 

Cliquez sur **Modifier**, modifiez la propriété suivante, puis cliquez sur **Put**. Veillez à remplacer la valeur _\<domain\_name>_ par le domaine souhaité.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Didacticiel : Authentifier et autoriser des utilisateurs de bout en bout (Windows)](app-service-web-tutorial-auth-aad.md)
> [Didacticiel : Authentifier et autoriser des utilisateurs de bout en bout (Linux)](containers/tutorial-auth-aad.md)