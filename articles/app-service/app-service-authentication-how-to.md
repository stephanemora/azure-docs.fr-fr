---
title: Utilisation avancée de l’authentification/autorisation
description: Apprenez à personnaliser les paramètres d’authentification et d’autorisation dans App Service pour différents scénarios et à obtenir des revendications d’utilisateur et des jetons distincts.
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: d57b196bf95ebdf31bc459ad4b9d718fd32ca495
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672234"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Utilisation avancée des paramètres d’authentification et d’autorisation dans Azure App Service

Cet article vous explique comment personnaliser les [paramètres d’authentification et d’autorisation intégrés dans App Service](overview-authentication-authorization.md), et comment gérer les identités à partir de votre application. 

Pour commencer rapidement, consultez l’un des didacticiels suivants :

* [Tutoriel : Authentifier et autoriser les utilisateurs de bout en bout dans Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)
* [Tutoriel : Authentifier et autoriser les utilisateurs de bout en bout dans Azure App Service pour Linux](containers/tutorial-auth-aad.md)
* [Comment configurer votre application pour utiliser une connexion Azure Active Directory](configure-authentication-provider-aad.md)
* [Comment configurer votre application pour utiliser une connexion Facebook](configure-authentication-provider-facebook.md)
* [Comment configurer votre application pour utiliser une connexion Google](configure-authentication-provider-google.md)
* [Comment configurer votre application pour utiliser une connexion par compte Microsoft](configure-authentication-provider-microsoft.md)
* [Comment configurer votre application pour utiliser une connexion Twitter](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Utiliser plusieurs fournisseurs de connexion

La configuration du portail n’offre pas de solution clé en main pour présenter à vos utilisateurs plusieurs fournisseurs de connexion (telles que Facebook et Twitter). Toutefois, il est relativement simple d’ajouter cette fonctionnalité à votre application. Voici la procédure à suivre :

Tout d’abord, sur la page **Authentification / Autorisation** du portail Azure, configurez chaque fournisseur d’identité que vous souhaitez activer.

Sous **Mesure à prendre quand une demande n’est pas authentifiée**, sélectionnez **Autoriser les requêtes anonymes (aucune action)** .

Dans la page de connexion, la barre de navigation ou tout autre emplacement de votre application, ajoutez un lien de connexion pour chacun des fournisseurs que vous avez activés (`/.auth/login/<provider>`). Par exemple :

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Lorsque l’utilisateur clique sur l’un des liens, la page de connexion respective s’ouvre pour que l’utilisateur se connecte.

Pour rediriger l’utilisateur post-connexion vers une URL personnalisée, utilisez le paramètre de chaîne de requête `post_login_redirect_url` (à ne pas confondre avec l’URI de redirection de votre configuration de fournisseur d’identité). Par exemple, pour diriger l’utilisateur vers `/Home/Index` après sa connexion, utilisez le code HTML suivant :

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Valider les jetons des fournisseurs

Dans une connexion dirigée par le client, l'application connecte manuellement l'utilisateur au fournisseur, puis soumet le jeton d'authentification à App Service pour validation (voir [Flux d'authentification](overview-authentication-authorization.md#authentication-flow)). Cette validation proprement dite ne vous octroie pas l'accès aux ressources souhaitées, mais une validation réussie vous conférera un jeton de session que vous pourrez utiliser pour accéder aux ressources de l'application. 

Pour valider le jeton du fournisseur, l'application App Service doit d'abord être configurée avec le fournisseur souhaité. Au moment de l'exécution, après avoir récupéré le jeton d'authentification auprès de votre fournisseur, envoyez-le à `/.auth/login/<provider>` pour validation. Par exemple : 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Le format du jeton varie légèrement selon le fournisseur. Pour plus de détails, consultez le tableau suivant :

| Valeur du fournisseur | Requis dans le corps de la demande | Commentaires |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | La propriété `expires_in` est facultative. <br/>Lors de la demande de jeton à partir des services Live, demandez toujours l'étendue `wl.basic`. |
| `google` | `{"id_token":"<id_token>"}` | La propriété `authorization_code` est facultative. Lorsqu'elle est spécifiée, elle peut également être accompagnée de la propriété `redirect_uri`. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Utilisez un [jeton d'accès utilisateur](https://developers.facebook.com/docs/facebook-login/access-tokens) valide à partir de Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Si le jeton du fournisseur est validé, l'API renvoie un `authenticationToken` dans le corps de la réponse. Il s'agit de votre jeton de session. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Une fois en possession de ce jeton de session, vous pouvez accéder aux ressources d'application protégées en ajoutant l'en-tête `X-ZUMO-AUTH` à vos requêtes HTTP. Par exemple : 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Déconnexion d’une session

Les utilisateurs peuvent initier une déconnexion en envoyant une requête `GET` au point de terminaison `/.auth/logout` de l’application. La requête `GET` effectue les opérations suivantes :

- Efface les cookies d’authentification de la session active.
- Supprime du magasin de jetons les jetons de l’utilisateur actuel.
- Pour Azure Active Directory et Google, effectue une déconnexion côté serveur sur le fournisseur d’identité.

Voici un lien de déconnexion simple dans une page web :

```HTML
<a href="/.auth/logout">Sign out</a>
```

Par défaut, une déconnexion réussie redirige le client vers l’URL `/.auth/logout/done`. Vous pouvez modifier la page de redirection après déconnexion en ajoutant le paramètre de requête `post_logout_redirect_uri`. Par exemple :

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Il est recommandé d’[encoder](https://wikipedia.org/wiki/Percent-encoding) la valeur de `post_logout_redirect_uri`.

Lorsque vous utilisez une URL complète, celle-ci doit être hébergée dans le même domaine, ou configurée comme URL de redirection externe autorisée pour votre application. Dans l’exemple suivant, pour rediriger vers l’URL `https://myexternalurl.com` qui n’est pas hébergée dans le même domaine :

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Exécutez la commande suivante dans [Azure Cloud Shell](../cloud-shell/quickstart.md) :

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Conserver les fragments d’URL

Lorsque des utilisateurs se connectent à votre application, ils veulent être redirigés vers la même section de la même page, par exemple, `/wiki/Main_Page#SectionZ`. Toutefois, étant donné que des [fragments d’URL](https://wikipedia.org/wiki/Fragment_identifier) (par exemple, `#SectionZ`) ne sont jamais envoyés au serveur, ils ne sont pas conservés par défaut après que la connexion OAuth est terminée et redirige vers votre application. Les utilisateurs vivent ensuite une expérience médiocre quand ils ont besoin d’accéder à nouveau à l’ancre souhaitée. Cette limitation s’applique à toutes les solutions d’authentification côté serveur.

Dans l’authentification App Service, vous pouvez conserver des fragments d’URL dans le cadre de la connexion OAuth. Pour ce faire, définissez un paramètre d’application nommé `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` sur `true`. Vous pouvez le faire dans le [portail Azure](https://portal.azure.com), ou simplement exécuter la commande suivante dans [Azure Cloud Shell](../cloud-shell/quickstart.md) :

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Accéder aux revendications d’utilisateur

App Service transmet des revendications d’utilisateur à votre application au moyen d’en-têtes spéciaux. Les demandes externes ne sont pas autorisées à définir ces en-têtes. Ces derniers ne s’affichent donc que s’ils sont définis par App Service. Voici quelques exemples d’en-têtes :

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Tout code, quels que soient le langage ou l’infrastructure utilisés, peut trouver les informations qu’il recherche dans ces en-têtes. Dans le cas d’applications ASP.NET 4.6, le paramètre **ClaimsPrincipal** est automatiquement défini sur les valeurs appropriées. Toutefois, ASP.NET Core ne fournit pas d’intergiciel d’authentification qui s’intègre aux revendications d’utilisateur App Service. Pour une solution de contournement, consultez [MaximeRouiller.Azure.AppService.EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

Votre application peut également obtenir des détails supplémentaires sur l’utilisateur authentifié en appelant `/.auth/me`. Les Kits de développement logiciel (SDK) serveur de Mobile Apps offrent des méthodes d’assistance permettant de manipuler ces données. Pour plus d’informations, consultez [Comment utiliser le Kit de développement logiciel Node.js dans Azure Mobile Apps](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity), et [Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Récupérer des jetons dans le code d’application

À partir de votre code serveur, les jetons spécifiques au fournisseur sont ajoutés dans l’en-tête de requête, afin que vous puissiez y accéder facilement. Le tableau suivant présente les noms d’en-tête de jeton possibles :

| Fournisseur | Noms des en-têtes |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Jeton Facebook | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Compte Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

À partir de votre code client (par exemple, une application mobile ou un navigateur JavaScript), envoyez une requête `GET` HTTP à `/.auth/me`. La réponse JSON retournée contient les jetons spécifiques au fournisseur.

> [!NOTE]
> Les jetons d’accès permettent d’accéder aux ressources du fournisseur. Ils ne s’affichent donc que si vous configurez votre fournisseur avec une clé secrète client. Pour savoir comment obtenir des jetons d’actualisation, consultez Actualiser des jetons d’accès.

## <a name="refresh-identity-provider-tokens"></a>Actualiser des jetons de fournisseur d’identité

Lorsque le jeton d'accès de votre fournisseur (et non le [jeton de session](#extend-session-token-expiration-grace-period)) expire, vous devez réauthentifier l’utilisateur avant de réutiliser ce jeton. Vous pouvez éviter l’expiration du jeton en effectuant un appel `GET` au point de terminaison `/.auth/refresh` de votre application. Lorsqu’il est appelé, App Service actualise automatiquement les jetons d’accès dans le magasin de jetons pour l’utilisateur authentifié. Les demandes de jeton suivantes effectuées via le code de votre application permettent d’obtenir les jetons actualisés. Toutefois, pour que l’actualisation des jetons soit effective, le magasin de jetons doit contenir les [jetons d’actualisation](https://auth0.com/learn/refresh-tokens/) pour votre fournisseur. La procédure pour obtenir des jetons d’actualisation est fournie par chaque fournisseur. La liste suivante en fournit toutefois un bref résumé :

- **Google** : ajouter un paramètre de chaîne de requête `access_type=offline` à votre appel d’API `/.auth/login/google`. Si vous utilisez le kit de développement logiciel Mobile Apps, vous pouvez ajouter le paramètre à l’une des surcharges `LogicAsync` (voir [Google Refresh Tokens](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens) (Jetons d’actualisation Google)).
- **Facebook** : ne fournit pas de jetons d’actualisation. Les jetons de longue durée expirent au bout de 60 jours (voir [Facebook Expiration and Extension of Access Tokens](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension) (Expiration et prolongation des jetons d’accès Facebook)).
- **Twitter** : les jetons d’accès n’expirent pas (voir les [questions fréquentes sur Twitter OAuth](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Compte Microsoft** : au moment de [configurer les paramètres d’authentification de compte Microsoft](configure-authentication-provider-microsoft.md), sélectionnez l’étendue `wl.offline_access`.
- **Azure Active Directory** : Dans [https://resources.azure.com](https://resources.azure.com), effectuez les étapes suivantes :
    1. En haut de la page, sélectionnez **Lecture/écriture**.
    2. Dans le navigateur de gauche, accédez à **abonnements** >  **_\<subscription\_name_**  > **resourceGroups** >  **_\<resource\_group\_name>_**  > **fournisseurs** > **Microsoft.Web** > **sites** >  **_\<app\_name>_**  > **config** > **authsettings**. 
    3. Cliquez sur **Modifier**.
    4. Modifiez la propriété suivante. Remplacez la valeur _\<app\_id>_ par l’ID d’application Azure Active Directory du service auquel vous souhaitez accéder.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Cliquez sur **Put**. 

Une fois que votre fournisseur est configuré, vous pouvez [rechercher le jeton d’actualisation et l’heure d’expiration pour le jeton d’accès](#retrieve-tokens-in-app-code) dans le magasin de jetons. 

Pour actualiser votre jeton d’accès à tout moment, il vous suffit d’appeler `/.auth/refresh` dans n’importe quel langage. L’extrait de code suivant utilise jQuery pour actualiser vos jetons d’accès à partir d’un client JavaScript.

```JavaScript
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

## <a name="limit-the-domain-of-sign-in-accounts"></a>Restreindre le domaine des comptes de connexion

Les options Compte Microsoft et Azure Active Directory permettent de se connecter à partir de multiples domaines. Par exemple, l’option Compte Microsoft prend en charge les comptes _outlook.com_, _live.com_ et _hotmail.com_. Azure Active Directory prend en charge un nombre illimité de domaines personnalisés pour les comptes de connexion. Toutefois, vous pouvez accélérer l’accès de vos utilisateurs directement à votre propre page de connexion Azure AD (par exemple, `contoso.com`). Pour suggérer le nom de domaine des comptes de connexion, procédez comme suit.

Dans [https://resources.azure.com](https://resources.azure.com), accédez à **abonnements** >  **_\< subscription\_ name_**  > **resourceGroups** >  **_\< resource\_ group\_ name>_**  > **fournisseurs** > **Microsoft.Web** > **sites** >  **_\< app\_ name>_**  > **config** > **authsettings**. 

Cliquez sur **Modifier**, modifiez la propriété suivante, puis cliquez sur **Put**. Veillez à remplacer la valeur _\<domain\_name>_ par le domaine souhaité.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Ce paramètre ajoute le paramètre de chaîne de requête `domain_hint` à l’URL de redirection de la connexion. 

> [!IMPORTANT]
> Le client peut supprimer le paramètre `domain_hint` après avoir reçu l’URL de redirection, puis de se connecter avec un autre domaine. Bien que cette fonction soit pratique, il ne s’agit pas d’une fonctionnalité de sécurité.
>

## <a name="authorize-or-deny-users"></a>Autoriser ou refuser des utilisateurs

Bien que App Service se charge du cas d’autorisation le plus simple (c’est-à-dire le rejet des demandes non authentifiées), votre application peut nécessiter un comportement d’autorisation plus précis, tel que la limitation de l’accès à un groupe spécifique d’utilisateurs. Dans certains cas, vous devez écrire un code d’application personnalisé pour autoriser ou refuser l’accès à l’utilisateur connecté. Dans d’autres cas, App Service ou votre fournisseur d’identité peut être en mesure de vous aider sans modification du code.

- [Au niveau du serveur](#server-level-windows-apps-only)
- [Au niveau du fournisseur d’identité](#identity-provider-level)
- [Au niveau de l’application](#application-level)

### <a name="server-level-windows-apps-only"></a>Au niveau du serveur (applications Windows uniquement)

Pour toute application Windows, vous pouvez définir le comportement d’autorisation du serveur web IIS en modifiant le fichier *Web.config*. Les applications Linux n’utilisent pas IIS et ne peuvent pas être configurées à l’aide du fichier *Web.config*.

1. Accédez à `https://<app-name>.scm.azurewebsites.net/DebugConsole`.

1. Dans l’explorateur de navigateur de vos fichiers App Service, accédez à *site/wwwroot*. S’il n’existe pas de fichier *Web.config*, créez-en un en sélectionnant **+**  > **Nouveau fichier**. 

1. Sélectionnez le crayon correspondant à *Web. config* pour le modifier. Ajoutez le code de configuration suivant, puis cliquez sur **Enregistrer**. Si le fichier *Web.config* existe, ajoutez simplement l’élément `<authorization>` avec tout ce qui se trouve dans celui-ci. Ajoutez les comptes que vous souhaitez autoriser dans l’élément `<allow>`.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Au niveau du fournisseur d’identité

Le fournisseur d’identité peut fournir un autorisation clé en main. Par exemple :

- Pour [Azure App Service](configure-authentication-provider-aad.md), vous pouvez [gérer l’accès au niveau entreprise](../active-directory/manage-apps/what-is-access-management.md) directement dans Azure AD. Pour obtenir des instructions, voir [Comprendre comment retirer l’accès d’un utilisateur à une application](../active-directory/manage-apps/methods-for-removing-user-access.md).
- Pour [Google](configure-authentication-provider-google.md) , les projets d’API Google appartenant à une [organisation](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) peuvent être configurés de façon à autoriser l’accès uniquement aux utilisateurs de votre organisation (voir la [page de support **Setting up OAuth 2.0** de Google](https://support.google.com/cloud/answer/6158849?hl=en)).

### <a name="application-level"></a>Niveau d’application

Si l’un des autres niveaux ne fournit pas l’autorisation dont vous avez besoin, ou si votre plateforme ou fournisseur d’identité ne sont pas pris en charge, vous devez écrire du code personnalisé afin d’autoriser les utilisateurs en fonction de leurs [revendications](#access-user-claims).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Authentifier et autoriser des utilisateurs de bout en bout (Windows)](app-service-web-tutorial-auth-aad.md)
> [Tutoriel : Authentifier et autoriser des utilisateurs de bout en bout (Linux)](containers/tutorial-auth-aad.md)
