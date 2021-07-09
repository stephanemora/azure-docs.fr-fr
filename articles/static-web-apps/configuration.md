---
title: Configurer Azure Static Web Apps
description: Apprenez à configurer des itinéraires, à appliquer des règles de sécurité et à définir des paramètres globaux pour Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 693a102c988d87dc4ed6ac9f0f4cb2176ec78ca5
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112059991"
---
# <a name="configure-azure-static-web-apps"></a>Configurer Azure Static Web Apps

La configuration d’Azure Static Web Apps est définie dans le fichier _staticwebapp.config.json_, qui contrôle les paramètres suivants :

- Routage
- Authentification
- Autorisation
- Règles de secours
- Remplacement des réponses HTTP
- Définitions globales d’en-tête HTTP
- Types MIME personnalisés

> [!NOTE]
> [_routes.jssur_](https://github.com/Azure/static-web-apps/wiki/routes.json-reference-(deprecated)) qui a été précédemment utilisé pour configurer le routage est déconseillé. Utilisez _staticwebapp.config.jssur_ tel que décrit dans cet article pour configurer le routage et d’autres paramètres pour votre application web statique.

## <a name="file-location"></a>Emplacement du fichier

L’emplacement recommandé pour le fichier _staticwebapp.config.json_ est le dossier défini comme `app_location` dans le [fichier de workflow](./github-actions-workflow.md). Toutefois, le fichier peut être placé à n’importe quel emplacement au sein du dossier du code source de votre application.

Pour plus de détails, consultez l’[exemple de fichier config](#example-configuration-file).

> [!IMPORTANT]
> Le fichier [_routes.json_](https://github.com/Azure/static-web-apps/wiki/routes.json-reference-(deprecated)) déconseillé est ignoré si un fichier _staticwebapp.config.json_ existe.

## <a name="routes"></a>Itinéraires

Les règles de routage vous permettent de définir le modèle des URL qui permettent d’accéder à votre application sur le web. Les itinéraires sont définis sous forme de tableau de règles d’acheminement. Pour obtenir des exemples d’utilisation, consultez l’[exemple de fichier config](#example-configuration-file).

- Les règles sont définies dans le tableau `routes`, même en présence d’un seul itinéraire.
- Les règles sont exécutées dans l’ordre dans lequel elles apparaissent dans le tableau `routes`.
- L’évaluation des règles s’arrête à la première correspondance : les règles d’acheminement ne sont pas enchaînées les unes aux autres.
- Vous avez un contrôle total sur les noms de rôles personnalisés.
  - Il existe quelques noms de rôles intégrés, notamment [`anonymous`](./authentication-authorization.md) et [`authenticated`](./authentication-authorization.md).

Les problèmes de routage chevauchent de manière significative les concepts d’authentification (identification de l’utilisateur) et d’autorisation (attribution de capacités à l’utilisateur). Lisez le Guide d’[authentification et d’autorisation](authentication-authorization.md), ainsi que cet article.

Le fichier par défaut pour le contenu statique est le fichier _index.html_.

## <a name="defining-routes"></a>Définition des itinéraires

Chaque règle est composée d’un modèle d’itinéraire, ainsi que d’une ou plusieurs des propriétés facultatives de la règle. Les règles de routage sont définies dans le tableau `routes`. Pour obtenir des exemples d’utilisation, consultez l’[exemple de fichier config](#example-configuration-file).

| Propriété de la règle                       | Obligatoire | Valeur par défaut                        | Commentaire                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------------------- | -------- | ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `route`                             | Oui      | n/a                                  | Modèle d’itinéraire demandé par l’appelant.<ul><li>[Les caractères génériques](#wildcards) sont pris en charge à la fin des chemins d’accès d’itinéraire.<ul><li>Par exemple, l’itinéraire _admin/\*_ correspond à n’importe quel itinéraire sous le chemin d’accès _admin_.</ul></ul>                                                                                                                                                                                                                                                                                                                                                                                                        |
| `rewrite`                           | Non       | n/a                                  | Définit le fichier ou le chemin d’accès retourné par la demande.<ul><li>S’exclut réciproquement d’une règle `redirect`.<li>Les règles de réécriture ne modifient pas l’emplacement du navigateur.<li>Les valeurs doivent être relatives à la racine de l’application.</ul>                                                                                                                                                                                                                                                                                                                                                                                                      |
| `redirect`                          | Non       | n/a                                  | Définit la destination de redirection du fichier ou du chemin d’accès pour une requête.<ul><li>S’exclut réciproquement d’une règle `rewrite`.<li>Les règles de redirection modifient l’emplacement du navigateur.<li>Le code de réponse par défaut est un code [`302`](https://developer.mozilla.org/docs/Web/HTTP/Status/302) (redirection temporaire), mais vous pouvez le remplacer par un code [`301`](https://developer.mozilla.org/docs/Web/HTTP/Status/301) (redirection permanente).</ul>                                                                                                                                                                                                              |
| `allowedRoles`                      | Non       | anonyme                            | Définit une liste de noms de rôles requis pour accéder à un itinéraire. <ul><li>Les caractères valides sont `a-z`, `A-Z`, `0-9` et `_`.<li>Le rôle intégré [`anonymous`](./authentication-authorization.md) s’applique à tous les utilisateurs non authentifiés.<li>Le rôle intégré [`authenticated`](./authentication-authorization.md) s’applique à tous les utilisateurs connectés.<li>Les utilisateurs doivent appartenir à au moins un rôle.<li>Les rôles sont mis en correspondance sur une base _OU_.<ul><li>Si un utilisateur se trouve dans l’un des rôles de la liste, l’accès est accordé.</ul><li>Les utilisateurs individuels sont associés à des rôles par le biais des [invitations](authentication-authorization.md).</ul> |
| `headers`<a id="route-headers"></a> | Non       | n/a                                  | Ensemble d’[en-têtes HTTP](https://developer.mozilla.org/docs/Web/HTTP/Headers) ajoutés à la réponse. <ul><li>Les en-têtes spécifiques à l’itinéraire remplacent [`globalHeaders`](#global-headers) lorsque l’en-tête spécifique à l’itinéraire est le même que l’en-tête global dans la réponse.<li>Pour supprimer un en-tête, définissez la valeur sur une chaîne vide.</ul>                                                                                                                                                                                                                                                                                          |
| `statusCode`                        | Non       | `200`, `301` ou `302` pour les redirections | [Code d’état HTTP](https://developer.mozilla.org/docs/Web/HTTP/Status) de la réponse.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `methods`                           | Non       | Toutes les méthodes                          | Liste des méthodes de demande qui correspondent à un itinéraire. Les méthodes disponibles sont notamment : `GET`, `HEAD`, `POST`, `PUT`, `DELETE`, `CONNECT`, `OPTIONS`, `TRACE` et `PATCH`.                                                                                                                                                                                                                                                                                                                                                                                                                                                             |

Chaque propriété a un objectif spécifique dans le pipeline de demande/réponse.

| Objectif                                        | Propriétés                                                                                   |
| ---------------------------------------------- | -------------------------------------------------------------------------------------------- |
| Faire correspondre les itinéraires                                   | `route`, `methods`                                                                           |
| Autoriser après la mise en correspondance d’un itinéraire             | `allowedRoles`                                                                               |
| Traiter après la mise en correspondance et l’autorisation d’une règle | `rewrite` (modifie la demande) <br><br>`redirect`, `headers`, `statusCode` (modifie la réponse) |

## <a name="securing-routes-with-roles"></a>Sécurisation des itinéraires avec des rôles

Les itinéraires sont sécurisés en ajoutant un ou plusieurs noms de rôles dans le tableau `allowedRoles` d’une règle, et les utilisateurs sont associés à des rôles personnalisés via des [invitations](./authentication-authorization.md). Pour obtenir des exemples d’utilisation, consultez l’[exemple de fichier config](#example-configuration-file).

Par défaut, chaque utilisateur appartient au rôle `anonymous` intégré et tous les utilisateurs connectés sont membres du rôle `authenticated`.

Par exemple, pour limiter un itinéraire aux seuls utilisateurs authentifiés, ajoutez le rôle `authenticated` intégré au groupe `allowedRoles`.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

Vous pouvez créer des rôles en fonction des besoins dans le tableau `allowedRoles`. Pour restreindre un itinéraire aux seuls administrateurs, vous pouvez définir votre propre rôle, nommé `administrator`, dans le tableau `allowedRoles`.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Vous avez un contrôle total sur les noms de rôles ; il n’existe pas de liste à laquelle vos rôles doivent adhérer.
- Les utilisateurs individuels sont associés à des rôles par le biais des [invitations](authentication-authorization.md).

## <a name="wildcards"></a>Caractères génériques

Les règles de caractères génériques correspondent à toutes les demandes d’un modèle d’itinéraire, sont uniquement prises en charge à la fin d’un chemin d’accès et peuvent être filtrées par extension de fichier. Pour obtenir des exemples d’utilisation, consultez l’[exemple de fichier config](#example-configuration-file).

Par exemple, pour implémenter des itinéraires pour une application de calendrier, vous pouvez réécrire toutes les URL qui se trouvent sous l’itinéraire _calendrier_ pour un seul fichier.

```json
{
  "route": "/calendar/*",
  "rewrite": "/calendar.html"
}
```

Le fichier _calendar.html_ peut ensuite utiliser le routage côté client pour offrir une vue différente des variantes d’URL, comme `/calendar/january/1`, `/calendar/2020` et `/calendar/overview`.

Vous pouvez filtrer les correspondances de caractères génériques par extension de fichier. Par exemple, si vous souhaitez ajouter une règle qui correspond uniquement aux fichiers HTML dans un chemin donné, vous pouvez créer la règle suivante :

```json
{
  "route": "/articles/*.html",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Pour filtrer sur plusieurs extensions de fichier, vous devez inclure les options entre accolades, comme illustré dans cet exemple :

```json
{
  "route": "/images/thumbnails/*.{png,jpg,gif}",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Les cas d’usage courants pour les itinéraires de caractères génériques sont les suivants :

- Servir un fichier spécifique pour un modèle de chemin complet
- Mapper différentes méthodes HTTP à un modèle de chemin complet
- Appliquer des règles d’authentification et d’autorisation
- Implémenter des règles de mise en cache spécialisées

## <a name="fallback-routes"></a>Itinéraires de secours

Les applications monopages s’appuient souvent sur le routage côté client. Ces règles d’acheminement côté client mettent à jour l’emplacement de la fenêtre du navigateur sans effectuer de demande au serveur. Si vous actualisez la page ou si vous accédez directement aux URL générées par les règles d’acheminement côté client, un itinéraire de secours côté serveur est requis pour servir la page HTML appropriée (qui est généralement le fichier _index.html_ de votre application côté client).

Vous pouvez configurer votre application pour qu’elle utilise des règles qui implémentent un itinéraire de secours, comme illustré dans l’exemple suivant, qui utilise un caractère générique de chemin d’accès avec un filtre de fichiers :

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  }
}
```

Dans l’exemple de structure de fichiers ci-dessous, les résultats suivants sont possibles avec cette règle.

```files
├── images
│   ├── logo.png
│   ├── headshot.jpg
│   └── screenshot.gif
│
├── css
│   └── global.css
│
└── index.html
```

| Demande...                                         | renvoie…                                                                                                    | avec l’état… |
| ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------- | ------------------ |
| _/about/_                                              | Le fichier _/index.html_                                                                                        | `200`              |
| _/images/logo.png_                                     | Le fichier image                                                                                                | `200`              |
| _/images/icon.svg_                                     | Le fichier _/index.html_, puisque l’extension de fichier _svg_ n’est pas listée dans le filtre `/images/*.{png,jpg,gif}` | `200`              |
| _/images/unknown.png_                                  | Erreur Fichier introuvable                                                                                          | `404`              |
| _/css/unknown.css_                                     | Erreur Fichier introuvable                                                                                          | `404`              |
| _/css/global.css_                                      | Le fichier de feuille de style                                                                                           | `200`              |
| Tout autre fichier en dehors des dossiers _/images_ ou _/css_ | Le fichier _/index.html_                                                                                        | `200`              |

## <a name="global-headers"></a>En-têtes globaux

La section `globalHeaders` fournit un ensemble d’[en-têtes HTTP](https://developer.mozilla.org/docs/Web/HTTP/Headers) appliqués à chaque réponse, sauf s’ils sont remplacés par une règle d’[en-tête d’itinéraire](#route-headers). Sinon, l’union des en-têtes de l’itinéraire et des en-têtes globaux est renvoyée.

Pour obtenir des exemples d’utilisation, consultez l’[exemple de fichier config](#example-configuration-file).

Pour supprimer un en-tête, définissez la valeur sur une chaîne vide (`""`).

Voici quelques cas d’usage courants pour les en-têtes globaux :

- Règles de mise en cache personnalisées
- Application de stratégies de sécurité
- Paramètres de codage

## <a name="response-overrides"></a>Remplacement des réponses

La section `responseOverrides` permet de définir une réponse personnalisée lorsque le serveur retourne un code d’erreur. Pour obtenir des exemples d’utilisation, consultez l’[exemple de fichier config](#example-configuration-file).

Les codes HTTP suivants peuvent être remplacés :

| Code d’état                                                   | Signification      | Cause possible                                                                                                                                                                                                                                                                                     |
| ------------------------------------------------------------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) | Demande incorrecte  | Lien d’invitation non valide.                                                                                                                                                                                                                                                                            |
| [401](https://developer.mozilla.org/docs/Web/HTTP/Status/401) | Non autorisé | Demande de pages à accès restreint sans authentification.                                                                                                                                                                                                                                                  |
| [403](https://developer.mozilla.org/docs/Web/HTTP/Status/403) | Interdit    | <ul><li>L’utilisateur est connecté, mais ne dispose pas des rôles nécessaires pour afficher la page.<li>L’utilisateur est connecté, mais le runtime ne peut pas récupérer les détails de l’utilisateur à partir de ses revendications d’identité.<li>Trop d’utilisateurs sont connectés au site avec des rôles personnalisés. Le runtime ne peut donc pas connecter l’utilisateur.</ul> |
| [404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) | Introuvable    | Fichier introuvable                                                                                                                                                                                                                                                                                     |

L’exemple de configuration suivant montre comment remplacer un code d’erreur.

```json
{
  "responseOverrides": {
    "400": {
      "rewrite": "/invalid-invitation-error.html",
      "statusCode": 200
    },
    "401": {
      "statusCode": 302,
      "redirect": "/login"
    },
    "403": {
      "rewrite": "/custom-forbidden-page.html",
      "statusCode": 200
    },
    "404": {
      "rewrite": "/custom-404.html",
      "statusCode": 200
    }
  }
}
```

## <a name="example-configuration-file"></a>Exemple de fichier de configuration

```json
{
  "routes": [
    {
      "route": "/profile",
      "allowedRoles": ["authenticated"]
    },
    {
      "route": "/admin/*",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/images/*",
      "headers": {
        "cache-control": "must-revalidate, max-age=15770000"
      }
    },
    {
      "route": "/api/*",
      "methods": ["GET"],
      "allowedRoles": ["registeredusers"]
    },
    {
      "route": "/api/*",
      "methods": ["PUT", "POST", "PATCH", "DELETE"],
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/api/*",
      "allowedRoles": ["authenticated"]
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": ["administrator", "customers_contoso"]
    },
    {
      "route": "/login",
      "rewrite": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": 404
    },
    {
      "route": "/logout",
      "redirect": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "rewrite": "/calendar.html"
    },
    {
      "route": "/specials",
      "redirect": "/deals",
      "statusCode": 301
    }
  ],
  "navigationFallback": {
    "rewrite": "index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  },
  "responseOverrides": {
    "400": {
      "rewrite": "/invalid-invitation-error.html"
    },
    "401": {
      "redirect": "/login",
      "statusCode": 302
    },
    "403": {
      "rewrite": "/custom-forbidden-page.html"
    },
    "404": {
      "rewrite": "/404.html"
    }
  },
  "globalHeaders": {
    "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
  },
  "mimeTypes": {
    ".json": "text/json"
  }
}
```

En fonction de la configuration ci-dessus, passez en revue les scénarios suivants.

| Demande...                                                    | a pour résultat…                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| _/profile_                                                        | Les utilisateurs authentifiés reçoivent le fichier _/profile/index.html_. Les utilisateurs non authentifiés sont redirigés vers _/login_.                                                                                                                                                                                                                                                                                                                              |
| _/admin/_                                                         | Les utilisateurs authentifiés ayant le rôle _administrator_ reçoivent le fichier _/admin/index.html_. Les utilisateurs authentifiés qui n’ont pas le rôle _administrator_ reçoivent une erreur `403`<sup>1</sup>. Les utilisateurs non authentifiés sont redirigés vers _/login_.                                                                                                                                                                                                          |
| _/logo.png_                                                       | Délivre l’image avec une règle de cache personnalisée dont l’âge maximal est un peu plus de 182 jours (15 770 000 secondes).                                                                                                                                                                                                                                                                                                                                   |
| _/api/admin_                                                      | Les demandes `GET` des utilisateurs authentifiés ayant le rôle _registeredusers_ sont envoyées à l’API. Les utilisateurs authentifiés qui n’ont pas le rôle _registeredusers_ et les utilisateurs non authentifiés reçoivent une erreur `401`.<br/><br/>Les demandes `POST`, `PUT`, `PATCH` et `DELETE` des utilisateurs authentifiés ayant le rôle _administrator_ sont envoyées à l’API. Les utilisateurs authentifiés qui n’ont pas le rôle _administrator_ et les utilisateurs non authentifiés reçoivent une erreur `401`. |
| _/customers/contoso_                                              | Les utilisateurs authentifiés qui appartiennent aux rôles _administrator_ ou _customers_contoso_ reçoivent le fichier _/customers/contoso/index.html_. Les utilisateurs authentifiés qui n’ont pas le rôle _administrator_ ou _customers_contoso_ reçoivent une erreur `403` <sup>1</sup>. Les utilisateurs non authentifiés sont redirigés vers _/login_.                                                                                                                            |
| _/login_                                                          | Les utilisateurs non authentifiés sont invités à s’authentifier auprès de GitHub.                                                                                                                                                                                                                                                                                                                                                                             |
| _/.auth/login/twitter_                                            | Comme l’autorisation avec Twitter est désactivée par la règle d’acheminement, une erreur `404` est renvoyée, ce qui revient à remettre _/index.html_ avec un code d’état `200`.                                                                                                                                                                                                                                                                                     |
| _/logout_                                                         | Les utilisateurs sont déconnectés de tous les fournisseurs d’authentification.                                                                                                                                                                                                                                                                                                                                                                                          |
| _/calendar/2021/01_                                               | Le navigateur reçoit le fichier _/calendar.html_.                                                                                                                                                                                                                                                                                                                                                                                              |
| _/specials_                                                       | Le navigateur est redirigé de façon permanente vers _/deals_.                                                                                                                                                                                                                                                                                                                                                                                            |
| _/data.json_                                                      | Le fichier remis avec le type MIME `text/json`.                                                                                                                                                                                                                                                                                                                                                                                               |
| _/about_, ou tout dossier qui correspond aux modèles de routage côté client | Le fichier _/index.html_ est remis avec un code d’état `200`.                                                                                                                                                                                                                                                                                                                                                                                    |
| Un fichier inexistant dans le dossier _/images/_                     | Une erreur `404`.                                                                                                                                                                                                                                                                                                                                                                                                                                |

<sup>1</sup> Vous pouvez fournir une page d’erreur personnalisée en utilisant une [règle de remplacement de la réponse](#response-overrides).

## <a name="restrictions"></a>Restrictions

Les restrictions suivantes existent pour le fichier _staticwebapps.config.json_.

- La taille maximale des fichiers est de 100 ko.
- Maximum de 50 rôles distincts.

Consultez [l’article sur les quotas](quotas.md) pour connaître les restrictions et les limitations générales.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configuration de l’authentification et de l’autorisation](authentication-authorization.md)
