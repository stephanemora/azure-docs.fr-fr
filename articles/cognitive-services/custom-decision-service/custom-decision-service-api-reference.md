---
title: API - Azure Cognitive Services | Microsoft Docs
description: Guide d’API, complet et convivial, du service Décision personnalisée, API cloud d’aide à la prise de décision contextuelle qui s’affine avec l’expérience.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/11/2018
ms.author: slivkins
ms.reviewer: marcozo, alekh
ms.openlocfilehash: 403b17e33394016a07a7b33ba1bcbfe6afdcc05b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370221"
---
# <a name="api"></a>API

le service Décision personnalisée Azure fournit deux API appelées pour chaque décision : l’[API de classement ](#ranking-api) (pour le classement des actions) et l’[API de récompense](#reward-api) pour la sortie de la récompense. En outre, vous fournissez une [API d’ensemble d’actions](#action-set-api-customer-provided) pour spécifier les actions du service Décision personnalisée Azure. Cet article aborde ces trois API. Un scénario classique est utilisé ci-dessous pour montrer comment le service Décision personnalisée optimise le classement des articles.

## <a name="ranking-api"></a>API de classement

L’API de classement utilise un modèle de communication de style [JSONP](https://en.wikipedia.org/wiki/JSONP) pour optimiser la latence et ignorer la [stratégie de même origine](https://en.wikipedia.org/wiki/Same-origin_policy). Cette dernière empêche JavaScript d’extraire des données en dehors de l’origine de la page.

Insérez cet extrait de code dans l’en-tête HTML de votre page principale (où une liste personnalisée d’articles est affichée) :

```html
// define the "callback function" to render UI
<script> function callback(data) { … } </script>
// "data" provides the ranking of URLs, see example below.

// call to Ranking API
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>?<parameters>" async></script>
// action set id is the name of the corresponding RSS/Atom feed.

// same call with multiple action sets:
// <script src="https://ds.microsoft.com/api/v2/<appId>/rank/<A1>/<A2>/.../<An>?<parameters>" async></script>
```

> [!IMPORTANT]
> La fonction de rappel doit être définie avant l’appel à l’API de classement.

> [!TIP]
> Pour améliorer la latence, l’API de classement est exposée via HTTP au lieu de HTTPS, comme dans `http://ds.microsoft.com/api/v2/<appId>/rank/*`.
> Toutefois, un point de terminaison HTTPS doit être utilisé si la première page est pris en charge par HTTPS.

Lorsque les paramètres ne sont pas utilisés, la réponse HTTP de l’API de classement est une chaîne au format JSONP :

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

Le navigateur exécute ensuite cette chaîne en tant qu’appel à la fonction `callback()`.

Le paramètre de la fonction de rappel dans l’exemple précédent présente le schéma suivant :

- `ranking` indique le classement des URL à afficher.
- `eventId` est utilisé en interne par le service Décision personnalisée pour faire correspondre à ce classement avec les clics correspondants.
- `appId` permet à la fonction de rappel de faire la distinction entre plusieurs applications de service Décision personnalisée en cours d’exécution sur la même page web.
- `actionSets` répertorie chaque ensemble d’actions utilisé dans l’appel d’API de classement, ainsi que l’horodatage UTC de la dernière actualisation réussie. Le service Décision personnalisée actualise régulièrement les flux d’ensemble d’actions. Par exemple, si certains des ensembles d’actions ne sont pas à jour, la fonction de rappel devra peut-être rétablir leur classement par défaut.

> [!IMPORTANT]
> Les ensembles d’actions spécifiés sont traités et éventuellement nettoyés, pour former le classement par défaut des articles. Le classement par défaut est ensuite réorganisé et renvoyé dans la réponse HTTP. Le classement par défaut est défini ici :
>
> - Dans chaque ensemble d’actions, seuls les 15 articles les plus récents sont conservés (si plus de 15 sont renvoyés).
> - Lorsque plusieurs ensembles d’actions sont spécifiés, ils sont fusionnés dans le même ordre que dans l’appel d’API. Le classement d’origine des articles est conservé dans chaque ensemble d’action. Les doublons sont supprimés en faveur des copies plus anciennes.
> - Les `n` premiers articles sont conservés dans la liste fusionnée des articles, où `n=20` par défaut.

### <a name="ranking-api-with-parameters"></a>API de classement avec des paramètres

L’API de classement autorise ces paramètres :

- `details=1` et `details=2` insèrent des détails supplémentaires sur chaque article répertorié dans `ranking`.
- `limit=<n>` spécifie le nombre maximal d’articles dans le classement par défaut. `n` doit être compris entre `2` et `30` (sinon, la valeur est tronquée à `2` ou `30`, respectivement).
- `dnt=1` désactive les cookies de l’utilisateur.

Les paramètres peuvent être combinés dans une syntaxe de chaîne de requête standard, par exemple `details=2&dnt=1`.

> [!IMPORTANT]
> Le paramètre par défaut en Europe doit être `dnt=1` jusqu'à ce que le client accepte la bannière de cookie. Cela doit également être le paramètre par défaut pour les sites web qui ciblent les mineurs. Pour plus d’informations, consultez les [conditions d’utilisation](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804).

L’élément `details=1` insère la balise `guid` de chaque article, s’il est pris en charge par l’API d’ensemble d’actions. Réponse HTTP :

```json
callback({
   "ranking":[{"id":"url1","details":[{"guid":"123"}]},
              {"id":"url2","details":[{"guid":"456"}]},
              {"id":"url3","details":[{"guid":"789"}]}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

L’élément `details=2` ajoute plus de détails que le service Décision personnalisée peut extraire du [code de caractérisation](https://github.com/Microsoft/mwt-ds/tree/master/Crawl) des métabalises SEO de l’article :

- `title` à partir de `<meta property="og:title" content="..." />`, `<meta property="twitter:title" content="..." />` ou `<title>...</title>`
- `description` à partir de `<meta property="og:description" ... />`, `<meta property="twitter:description" content="..." />` ou `<meta property="description" content="..." />`
- `image` à partir de `<meta property="og:image" content="..." />`
- `ds_id` à partir de `<meta name=”microsoft:ds_id” content="..." />`

Réponse HTTP :

```json
callback({
   "ranking":[{"id":"url1","details":<details>},
              {"id":"url2","details":<details>},
              {"id":"url3","details":<details>}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

Élément `<details>` :

```json
[{"guid":"123"}, {"description":"some text", "ds_id":"234", "image":"ImageUrl1", "title":"some text"}]
```

## <a name="reward-api"></a>API de récompense

Le service Décision personnalisée utilise uniquement les clics sur le premier emplacement. Chaque clic est interprété comme une récompense de 1. L’absence de clic est interprétée comme une récompense de 0. Les clics sont mis en correspondance avec les classements liés à l’aide des ID d’événement, qui sont générés par l’appel de l’[API de classement](#ranking-api). Si nécessaire, les ID d’événement peuvent être transmis via des cookies de session.

Pour gérer un clic sur le premier emplacement, mettez ce code de votre page principale :

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Ici, `data` est l’argument de la fonction `callback()`, comme décrit précédemment. L’utilisation de `data` dans le code de gestion des clics requiert une attention particulière. Un exemple est présenté dans ce [didacticiel](custom-decision-service-tutorial-news.md#use-the-apis).

À des fins de test uniquement, l’API de récompense peut être appelée via [cURL](https://en.wikipedia.org/wiki/CURL) :

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

L’effet escompté est une réponse HTTP 200 (OK). Vous pouvez voir la récompense de 1 pour cet événement dans le journal (si une clé de compte de stockage Azure a été fournie sur le portail).

## <a name="action-set-api-customer-provided"></a>API d’ensemble d’actions (fournie par le client)

À un niveau élevé, l’API d’ensemble d’actions renvoie une liste d’articles (actions). Chaque article est spécifié par son URL, avec (éventuellement) son titre et sa date de publication. Vous pouvez spécifier plusieurs ensembles d’actions sur le portail. Une API d’ensemble d’actions différente doit être utilisée pour chaque ensemble d’actions, en tant qu’URL distincte.

Chaque API d’ensemble d’actions peut être implémentée de deux façons : sous la forme d’un flux RSS ou d’un flux Atom. Chaque méthode doit être conforme à la norme et renvoyer un format XML correct. Voici un exemple pour le flux RSS :

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <guid>guid (could be a your internal database id)</guid>
      <pubDate>date</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Chaque élément `<item>` de niveau supérieur décrit une action :

- `<link>` est obligatoire et utilisé comme ID d’action.
- `<date>` est ignore si la valeur est inférieure ou égale à 15 éléments ; dans le cas contraire, il est obligatoire.
  - S’il existe plus de 15 élément, seuls les 15 plus récents sont utilisés.
  - Il doit être au format standard pour les flux RSS ou Atom, respectivement :
    - [RFC 822](https://tools.ietf.org/html/rfc822) pour RSS : par exemple, `"Fri, 28 Apr 2017 18:02:06 GMT"`
    - [RFC 3339](https://tools.ietf.org/html/rfc3339) pour Atom : par exemple, `"2016-12-19T16:39:57-08:00"`
- `<title>` est facultatif et utilisé pour générer des fonctionnalités qui décrivent l’article.
- `<guid>` est facultatif et transmis via le système à la fonction de rappel (si le paramètre `?details` est spécifié dans l’appel d’API de classement).

Les autres éléments inclus dans `<item>` sont ignorés.

La version du flux Atom utilise les mêmes conventions et la même syntaxe XML.

> [!TIP]
> Si votre système utilise ses propres ID d’article, ceux-ci peuvent être transmis à la fonction de rappel à l’aide de `<guid>`.