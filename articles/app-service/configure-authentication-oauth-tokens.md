---
title: Jetons OAuth dans AuthN/AuthZ
description: Découvrez comment récupérer des jetons et des jetons d’actualisation et étendre les sessions lors de l’utilisation de l’authentification et de l’autorisation intégrées dans App Service.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: f158f9f76820635a65737b75f3c016ff67a3a92a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352084"
---
# <a name="work-with-oauth-tokens-in-azure-app-service-authentication"></a>Utiliser des jetons OAuth dans le cadre de l’authentification Azure App Service

Cet article explique comment utiliser les jetons OAuth quand vous utilisez [l’authentification et l’autorisation intégrées dans App Service](overview-authentication-authorization.md). 

## <a name="retrieve-tokens-in-app-code"></a>Récupérer des jetons dans le code d’application

À partir de votre code serveur, les jetons spécifiques au fournisseur sont ajoutés dans l’en-tête de requête, afin que vous puissiez y accéder facilement. Le tableau suivant présente les noms d’en-tête de jeton possibles :

| Fournisseur | Noms des en-têtes |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Jeton Facebook | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

> [!NOTE]
> Différentes infrastructures de langage peuvent présenter ces en-têtes au code d’application dans différents formats, tels que des minuscules ou des majuscules.

À partir de votre code client (par exemple, une application mobile ou un navigateur JavaScript), envoyez une requête `GET` HTTP à `/.auth/me` (le [magasin de jetons](overview-authentication-authorization.md#token-store) doit être activé). La réponse JSON retournée contient les jetons spécifiques au fournisseur.

> [!NOTE]
> Les jetons d’accès permettent d’accéder aux ressources du fournisseur. Ils ne s’affichent donc que si vous configurez votre fournisseur avec une clé secrète client. Pour savoir comment obtenir des jetons d’actualisation, consultez Actualiser des jetons d’accès.

## <a name="refresh-auth-tokens"></a>Actualiser les jetons d’authentification

Lorsque le jeton d'accès de votre fournisseur (et non le [jeton de session](#extend-session-token-expiration-grace-period)) expire, vous devez réauthentifier l’utilisateur avant de réutiliser ce jeton. Vous pouvez éviter l’expiration du jeton en effectuant un appel `GET` au point de terminaison `/.auth/refresh` de votre application. Lorsqu’il est appelé, App Service actualise automatiquement les jetons d’accès dans le [magasin de jetons](overview-authentication-authorization.md#token-store) pour l’utilisateur authentifié. Les demandes de jeton suivantes effectuées via le code de votre application permettent d’obtenir les jetons actualisés. Toutefois, pour que l’actualisation des jetons soit effective, le magasin de jetons doit contenir les [jetons d’actualisation](https://auth0.com/learn/refresh-tokens/) pour votre fournisseur. La procédure pour obtenir des jetons d’actualisation est fournie par chaque fournisseur. La liste suivante en fournit toutefois un bref résumé :

- **Google** : ajouter un paramètre de chaîne de requête `access_type=offline` à votre appel d’API `/.auth/login/google`. Si vous utilisez le kit de développement logiciel Mobile Apps, vous pouvez ajouter le paramètre à l’une des surcharges `LogicAsync` (voir [Google Refresh Tokens](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens) (Jetons d’actualisation Google)).
- **Facebook** : ne fournit pas de jetons d’actualisation. Les jetons de longue durée expirent au bout de 60 jours (voir [Facebook Expiration and Extension of Access Tokens](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension) (Expiration et prolongation des jetons d’accès Facebook)).
- **Twitter** : les jetons d’accès n’expirent pas (voir les [questions fréquentes sur Twitter OAuth](https://developer.twitter.com/en/docs/authentication/faq)).
- **Microsoft** : Dans [https://resources.azure.com](https://resources.azure.com), effectuez les étapes suivantes :
    1. En haut de la page, sélectionnez **Lecture/écriture**.
    2. Dans le navigateur de gauche, accédez à **subscriptions** > ** _\<subscription\_name_** > **resourceGroups** > **_ \<resource\_group\_name> _** > **providers** > **Microsoft.Web** > **sites** > **_ \<app\_name>_** > **config** > **authsettingsV2**.
    3. Cliquez sur **Modifier**.
    4. Modifiez la propriété suivante.

        ```json
        "identityProviders": {
          "azureActiveDirectory": {
            "login": {
              "loginParameters": ["scope=openid profile email offline_access"]
            }
          }
        }
        ```

    5. Cliquez sur **Put**.
    
    > [!NOTE]
    > L’étendue qui vous donne un jeton d’actualisation est [offline_access](../active-directory/develop/v2-permissions-and-consent.md#offline_access). Découvrez l’utilisation dans le [Tutoriel : Authentifier et autoriser des utilisateurs de bout en bout dans Azure App Service](tutorial-auth-aad.md). Les autres étendues sont déjà demandées par défaut par App Service. Pour plus d’informations sur ces étendues par défaut, consultez [Étendues OpenID Connect](../active-directory/develop/v2-permissions-and-consent.md#openid-connect-scopes).

Une fois que votre fournisseur est configuré, vous pouvez [rechercher le jeton d’actualisation et l’heure d’expiration pour le jeton d’accès](#retrieve-tokens-in-app-code) dans le magasin de jetons. 

Pour actualiser votre jeton d’accès à tout moment, il vous suffit d’appeler `/.auth/refresh` dans n’importe quel langage. L’extrait de code suivant utilise jQuery pour actualiser vos jetons d’accès à partir d’un client JavaScript.

```javascript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Si un utilisateur révoque les autorisations accordées à votre application, votre appel à `/.auth/me` peut échouer avec une réponse `403 Forbidden`. Pour diagnostiquer les erreurs, consultez vos journaux d’activité d’application pour obtenir des informations supplémentaires.

## <a name="extend-session-token-expiration-grace-period"></a>Étendre la période de grâce d’expiration de jeton de session

La session authentifiée expire au bout de 8 heures. Après expiration d’une session authentifiée, une période de grâce de 72 heures s’applique par défaut. Au cours de cette période de grâce, vous pouvez actualiser le jeton de session via App Service sans réauthentifier l’utilisateur. Vous pouvez simplement appeler `/.auth/refresh` lorsque jeton de session n’est plus valide. Vous n’avez pas besoin de vérifier vous-même les dates d’expiration des jetons. À la fin de la période de grâce de 72 heures, l’utilisateur doit se connecter à nouveau pour obtenir un jeton de session valide.

Si le délai de 72 heures n’est pas suffisamment long pour vous, vous pouvez étendre cette fenêtre d’expiration. Étendre la fenêtre d’expiration sur une longue période peut avoir une incidence majeure sur la sécurité (par exemple lorsqu’un jeton d’authentification est divulgué ou volé). Nous vous recommandons donc de conserver la valeur par défaut de 72 heures ou de définir la période d’extension sur la valeur minimale.

Pour étendre la fenêtre d’expiration par défaut, exécutez la commande suivante dans [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> La période de grâce s’applique uniquement à la session App Service authentifiée, et non aux jetons fournis par les fournisseurs d’identité. Il n’existe aucune période de grâce pour les jetons de fournisseur ayant expiré. 
>

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Authentifier et autoriser des utilisateurs de bout en bout](tutorial-auth-aad.md)
