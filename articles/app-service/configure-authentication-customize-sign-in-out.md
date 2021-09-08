---
title: Personnaliser les connexions et les déconnexions
description: Utilisez l’authentification et l’autorisation intégrées dans App Service et, en même temps, personnalisez le comportement de connexion et de déconnexion.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: bdb100afa821aa08fb831aac53b1eb80cdda043e
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122564050"
---
# <a name="customize-sign-in-and-sign-out-in-azure-app-service-authentication"></a>Personnaliser la connexion et la déconnexion dans l’authentification dans Azure App Service

Cet article vous explique comment personnaliser la connexion et la déconnexion de l’utilisateur tout en utilisant l’[authentification et l’autorisation dans App Service](overview-authentication-authorization.md). 

## <a name="use-multiple-sign-in-providers"></a>Utiliser plusieurs fournisseurs de connexion

La configuration du portail n’offre pas de solution clé en main pour présenter à vos utilisateurs plusieurs fournisseurs de connexion (telles que Facebook et Twitter). Toutefois, il est relativement simple d’ajouter cette fonctionnalité à votre application. Voici la procédure à suivre :

Tout d’abord, sur la page **Authentification / Autorisation** du portail Azure, configurez chaque fournisseur d’identité que vous souhaitez activer.

Sous **Mesure à prendre quand une demande n’est pas authentifiée**, sélectionnez **Autoriser les requêtes anonymes (aucune action)** .

Dans la page de connexion, la barre de navigation ou tout autre emplacement de votre application, ajoutez un lien de connexion pour chacun des fournisseurs que vous avez activés (`/.auth/login/<provider>`). Par exemple :

```html
<a href="/.auth/login/aad">Log in with the Microsoft Identity Platform</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
<a href="/.auth/login/apple">Log in with Apple</a>
```

Lorsque l’utilisateur clique sur l’un des liens, la page de connexion respective s’ouvre pour que l’utilisateur se connecte.

Pour rediriger l’utilisateur post-connexion vers une URL personnalisée, utilisez le paramètre de chaîne de requête `post_login_redirect_uri` (à ne pas confondre avec l’URI de redirection de votre configuration de fournisseur d’identité). Par exemple, pour diriger l’utilisateur vers `/Home/Index` après sa connexion, utilisez le code HTML suivant :

```html
<a href="/.auth/login/<provider>?post_login_redirect_uri=/Home/Index">Log in</a>
```

## <a name="client-directed-sign-in"></a>Connexion dirigée vers le client

Dans une connexion dirigée vers le client, l’application connecte l’utilisateur au fournisseur d’identité à l’aide d’un kit de développement logiciel (SDK) spécifique au fournisseur. Le code d’application envoie ensuite le jeton d’authentification obtenu à App Service pour validation (voir [Flux d’authentification](overview-authentication-authorization.md#authentication-flow)) à l’aide d’une requête HTTP POST. Le [kit de développement logiciel (SDK) des applications mobiles Azure](https://github.com/Azure/azure-mobile-apps) utilise ce flux de connexion. Cette validation proprement dite ne vous octroie pas l'accès aux ressources souhaitées, mais une validation réussie vous conférera un jeton de session que vous pourrez utiliser pour accéder aux ressources de l'application.

Pour valider le jeton du fournisseur, l'application App Service doit d'abord être configurée avec le fournisseur souhaité. Au moment de l'exécution, après avoir récupéré le jeton d'authentification auprès de votre fournisseur, envoyez-le à `/.auth/login/<provider>` pour validation. Par exemple :

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Le format du jeton varie légèrement selon le fournisseur. Pour plus de détails, consultez le tableau suivant :

| Valeur du fournisseur | Requis dans le corps de la demande | Commentaires |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | Les propriétés `id_token`, `refresh_token` et `expires_in` sont facultatives. |
| `microsoftaccount` | `{"access_token":"<access_token>"}` ou `{"authentication_token": "<token>"`| `authentication_token` est préférable à `access_token`. La propriété `expires_in` est facultative. <br/> Lors de la demande de jeton à partir des services Live, demandez toujours l'étendue `wl.basic`. |
| `google` | `{"id_token":"<id_token>"}` | La propriété `authorization_code` est facultative. Le fait de fournir une valeur `authorization_code` ajoute un jeton d’accès et un jeton d’actualisation au magasin de jetons. Lorsqu’elle est spécifiée, `authorization_code` peut également et éventuellement être accompagnée d’une propriété `redirect_uri`. |
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

```html
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

## <a name="limit-the-domain-of-sign-in-accounts"></a>Restreindre le domaine des comptes de connexion

Les options Compte Microsoft et Azure Active Directory permettent de se connecter à partir de multiples domaines. Par exemple, l’option Compte Microsoft prend en charge les comptes _outlook.com_, _live.com_ et _hotmail.com_. Azure Active Directory prend en charge un nombre illimité de domaines personnalisés pour les comptes de connexion. Toutefois, vous pouvez accélérer l’accès de vos utilisateurs directement à votre propre page de connexion Azure AD (par exemple, `contoso.com`). Pour suggérer le nom de domaine des comptes de connexion, procédez comme suit.

Dans [https://resources.azure.com](https://resources.azure.com), accédez à **subscriptions**> ** _\<subscription\_name_** > **resourceGroups** > **_ \<resource\_group\_name> _** > **providers** > **Microsoft.Web** > **sites** > **_ \<app\_name> _** > **config** > **authsettings**. 

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

Si l’un des autres niveaux ne fournit pas l’autorisation dont vous avez besoin, ou si votre plateforme ou fournisseur d’identité ne sont pas pris en charge, vous devez écrire du code personnalisé afin d’autoriser les utilisateurs en fonction de leurs [revendications](configure-authentication-user-identities.md).

## <a name="more-resources"></a>Plus de ressources

- [Tutoriel : Authentifier et autoriser des utilisateurs de bout en bout](tutorial-auth-aad.md)
- [Variables d’environnement et paramètres d’application pour l’authentification](reference-app-settings.md#authentication--authorization)
