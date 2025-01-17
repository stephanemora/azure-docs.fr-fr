---
title: Restrictions des URI de redirection (URL de réponse) | Azure AD
titleSuffix: Microsoft identity platform
description: Description des restrictions et limitations relatives au format des URI de redirection (URL de réponse) appliquées par la plateforme d’identité Microsoft.
author: madansr7
ms.author: saumadan
manager: CelesteDG
ms.date: 09/03/2021
ms.topic: conceptual
ms.subservice: develop
ms.custom: contperf-fy21q4-portal, aaddev
ms.service: active-directory
ms.reviewer: marsma, lenalepa, manrath
ms.openlocfilehash: d20d14619111515332b6aa5aec9239d0a6d50283
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353042"
---
# <a name="redirect-uri-reply-url-restrictions-and-limitations"></a>Limitations et restrictions des URI de redirection (URL de réponse)

Un URI de redirection, ou URL de réponse, correspond à l’emplacement vers lequel le serveur d’autorisation envoie l’utilisateur une fois l’application autorisée et un code d’autorisation ou un jeton d’accès attribué. Le serveur d’autorisation envoie le code ou le jeton à l’URI de redirection. Il est donc important que vous inscriviez l’emplacement qui convient dans le cadre du processus d’inscription de l’application.

Le modèle d’application Azure Active Directory (Azure AD) spécifie ces restrictions pour les URI de redirection :

* Les URI de redirection doivent commencer par le schéma `https`. Il existe des [exceptions pour les URI de redirection localhost](#localhost-exceptions).

* Les URI de redirection respectent la casse et doivent correspondre à la casse du chemin d’accès de l’URL de votre application en cours d’exécution. Par exemple, si votre application comprend `.../abc/response-oidc` dans son chemin d’accès, ne spécifiez pas `.../ABC/response-oidc` dans l’URI de redirection. Étant donné que le navigateur web considère que les chemins respectent la casse, les cookies associés à `.../abc/response-oidc` peuvent être exclus s’ils sont redirigés vers l’URL `.../ABC/response-oidc` qui ne correspond pas à la casse.

* Les URI de redirection *non* configurés avec un segment de chemin d’accès sont retournés avec une barre oblique finale (« `/` ») dans la réponse. Cela ne s’applique que lorsque le mode de réponse est `query` ou `fragment`.

    Exemples :

    * `https://contoso.com` est retourné comme `https://contoso.com/`
    * `http://localhost:7071` est retourné comme `http://localhost:7071/`

* Les URI de redirection qui contiennent un segment de chemin d’accès ne sont *pas* accompagnés d’une barre oblique finale dans la réponse.

    Exemples :

    * `https://contoso.com/abc` est retourné comme `https://contoso.com/abc`
    * `https://contoso.com/abc/response-oidc` est retourné comme `https://contoso.com/abc/response-oidc`

## <a name="maximum-number-of-redirect-uris"></a>Nombre maximal d'URI de redirection

Ce tableau indique le nombre maximal d’URI de redirection que vous pouvez ajouter à une inscription d’application dans la plateforme d’identité Microsoft.

| Comptes en cours de connexion | Nombre maximal d'URI de redirection | Description |
|--------------------------|---------------------------------|-------------|
| Comptes professionnels et scolaires Microsoft d'un locataire Azure Active Directory (Azure AD) d'une organisation | 256 | Dans le manifeste de l'application, le champ `signInAudience` est défini sur *AzureADMyOrg* ou *AzureADMultipleOrgs* |
| Comptes personnels ou professionnels et scolaires Microsoft | 100 | Dans le manifeste de l'application, le champ `signInAudience` est défini sur *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Longueur maximale d’URI

Vous pouvez utiliser un maximum de 256 caractères pour chaque URI de redirection que vous ajoutez à une inscription d’application.

## <a name="redirect-uris-in-application-vs-service-principal-objects"></a>URI de redirection dans les objets de principal de service et d’application

* Ajoutez toujours les URI de redirection à l’objet d’application uniquement.
* N’ajoutez pas de valeurs d’URI de redirection à un principal de service, car ces valeurs peuvent être supprimées lorsque l’objet de principal de service est synchronisé avec l’objet d’application. Cela peut se produire en raison d’une opération de mise à jour qui déclenche une synchronisation entre les deux objets.

## <a name="supported-schemes"></a>Schémas pris en charge

**HTTPS** : le schéma HTTPS (`https://`) est pris en charge pour tous les URI de redirection basés sur HTTP.

**HTTP** : le schéma HTTP (`http://`) est pris en charge *uniquement* pour les URI *localhost* et doit être utilisé uniquement pendant le développement et le test de l’application locale active.

| Exemple d’URI de redirection                    | Validité |
|-----------------------------------------|----------|
| `https://contoso.com`                   | Valide    |
| `https://contoso.com/abc/response-oidc` | Valide    |
| `https://localhost`                     | Valide    |
| `http://contoso.com/abc/response-oidc`  | Non valide  |
| `http://localhost`                      | Valide    |
| `http://localhost/abc`                  | Valide    |

### <a name="localhost-exceptions"></a>Exceptions de Localhost

Selon le document [RFC 8252 : sections 8.3](https://tools.ietf.org/html/rfc8252#section-8.3) et [7.3](https://tools.ietf.org/html/rfc8252#section-7.3), les URI de redirection « loopback » ou « localhost » comportent deux aspects particuliers à prendre en considération :

1. `http` Les schémas d’URI sont acceptables car la redirection ne quitte jamais l’appareil. Par conséquent, ces deux URI sont acceptables :
    - `http://localhost/myApp`
    - `https://localhost/myApp`
1. En raison des plages de ports éphémères souvent nécessaires aux applications natives, le composant de port (par exemple `:5001` ou `:443`) est ignoré pour permettre une correspondance avec un URI de redirection. En conséquence, tous ces URI sont considérés comme équivalents :
    - `http://localhost/MyApp`
    - `http://localhost:1234/MyApp`
    - `http://localhost:5000/MyApp`
    - `http://localhost:8080/MyApp`

Du point de vue du développement, cela signifie plusieurs choses :

* N’inscrivez pas plusieurs URI de redirection quand seul le port diffère. Le serveur de connexion en choisit un arbitrairement et utilise le comportement associé à cet URI de redirection (par exemple, s’il s’agit d’une redirection de type `web`, `native` ou `spa`).

    Cela est particulièrement important lorsque vous souhaitez utiliser différents flux d’authentification dans la même inscription d’application, par exemple l’octroi d’un code d’autorisation et le flux implicite. Pour associer le comportement de réponse correct à chaque URI de redirection, le serveur de connexion doit pouvoir faire la distinction entre les URI de redirection et ne peut pas le faire lorsque seul le port diffère.
* Pour inscrire plusieurs URI de redirection sur localhost afin de tester différents flux pendant le développement, différenciez-les à l’aide du composant *path* de l’URI. Par exemple, `http://localhost/MyWebApp` ne correspond pas à `http://localhost/MyNativeApp`.
* L’adresse de bouclage IPv6 (`[::1]`) n’est pas prise en charge actuellement.

#### <a name="prefer-127001-over-localhost"></a>Donner la préférence à 127.0.0.1 plutôt qu’à localhost

Pour éviter que votre application ne soit interrompue par des pare-feu mal configurés ou des interfaces réseau renommées, utilisez l’adresse IP de bouclage littérale `127.0.0.1` dans votre URI de redirection, à la place de `localhost`. Par exemple : `https://127.0.0.1`.

Toutefois, vous ne pouvez pas utiliser la zone de texte **URI de redirection** dans le portail Azure pour ajouter un URI de redirection basé sur un bouclage qui utilise le schéma `http` :

:::image type="content" source="media/reply-url/portal-01-no-http-loopback-redirect-uri.png" alt-text="Boîte de dialogue d’erreur dans le portail Azure présentant l’URI de redirection de bouclage http non autorisé":::

Pour ajouter un URI de redirection qui utilise le schéma `http` avec l’adresse de bouclage `127.0.0.1`, vous devez actuellement modifier l’attribut [replyUrlsWithType dans le manifeste de l’application](reference-app-manifest.md#replyurlswithtype-attribute).

## <a name="restrictions-on-wildcards-in-redirect-uris"></a>Restrictions concernant les caractères génériques des URI de redirection

Les URI avec caractères génériques comme `https://*.contoso.com` peuvent paraître pratiques, mais doivent être évités en raison des implications en matière de sécurité. Selon la spécification OAuth 2.0 ([section 3.1.2 de RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)), un URI de point de terminaison de redirection doit être un URI absolu.

Les URI avec caractères génériques ne sont actuellement pas pris en charge dans les inscriptions d’applications configurées pour se connecter à des comptes Microsoft personnels et à des comptes professionnels ou scolaires. Les URI avec caractères génériques sont autorisés, mais uniquement pour les applications configurées pour se connecter à des comptes professionnels ou scolaires dans un locataire Azure AD d’une organisation.

Pour ajouter des URI de redirection avec des caractères génériques aux inscriptions d’applications qui se connectent à des comptes professionnels ou scolaires, utilisez l’éditeur de manifeste de l’application dans **Inscriptions d’applications** dans le portail Azure. Bien qu’il soit possible de définir un URI de redirection avec un caractère générique à l’aide de l’éditeur de manifeste, nous vous recommandons *fortement* de respecter la section 3.1.2 de la RFC 6749. et utilisent uniquement des URI absolus.

Si votre scénario implique plus d’URI de redirection que la limite maximale autorisée, envisagez l’approche de paramètre d’état suivante au lieu d’ajouter un URI de redirection avec caractères génériques.

#### <a name="use-a-state-parameter"></a>Utiliser un paramètre d’état

Si vous avez plusieurs sous-domaines et que votre scénario implique que vous redirigiez des utilisateurs, en cas d’authentification réussie, vers la même page que celle où ils ont été démarrés, l’utilisation d’un paramètre d’état peut être utile.

Dans cette approche :

1. Créez un URI de redirection « partagé » par l’application pour traiter les jetons de sécurité que vous recevez du point de terminaison d’autorisation.
1. Votre application peut envoyer des paramètres qui lui sont spécifiques (URL de sous-domaine avec origine de l’utilisateur ou informations sur une marque, par exemple) dans le paramètre d’état. Lorsque vous utilisez un paramètre d’état, protégez-vous contre la falsification de requête intersites (CSRF, Cross Site Request Forgery) comme spécifié dans la [section 10.12 du document RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)).
1. Les paramètres spécifiques à l’application incluent toutes les informations requises pour permettre à l'application d'offrir une bonne expérience à l’utilisateur, à savoir, concevoir l'état d'application approprié. Le point de terminaison d'autorisation Azure AD supprime le code HTML du paramètre d'état. Aussi, assurez-vous que vous ne transmettez pas de contenu HTML dans ce paramètre.
1. Quand Azure AD envoie une réponse à l'URI de redirection « partagé », il renvoie le paramètre d'état à l'application.
1. L'application peut ensuite utiliser la valeur du paramètre d'état pour déterminer l'URL vers laquelle envoyer l'utilisateur. Veillez à valider la protection CSRF.

> [!WARNING]
> Cette approche permet à un client compromis de modifier les paramètres supplémentaires envoyés dans le paramètre d'état, redirigeant ainsi l'utilisateur vers une URL différente, ce qui correspond à la [menace de redirecteur ouvert](https://tools.ietf.org/html/rfc6819#section-4.2.4) décrite dans le document RFC 6819. Par conséquent, le client doit protéger ces paramètres en chiffrant l’état ou en le vérifiant par un autre moyen, tel que la validation du nom de domaine dans l’URI de redirection par rapport au jeton.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [Manifeste de l’application](reference-app-manifest.md) de l’inscription d’application.
