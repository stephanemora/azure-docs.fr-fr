---
title: Appeler une API à partir d’une application - Azure Cognitive Services | Microsoft Docs
description: Comment bien commencer avec le Service Décision personnalisée Azure si vous appelez les API à partir d’une application de smartphone.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/10/2018
ms.author: slivkins
ms.reviewer: marcozo, alekh
ms.openlocfilehash: 2d02b0deaaa701dd0b4818638827c04e2c946558
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370716"
---
# <a name="call-api-from-an-app"></a>Appeler des API à partir d’une application

Faites appel aux API du Service Décision personnalisée Azure à partir d’une application de smartphone. Cet article explique comment prendre en main certaines options de base.

Commencez par [Inscrire votre application](custom-decision-service-get-started-register.md).

Vous pouvez faire deux appels d’API à partir de votre application de smartphone au Service Décision personnalisée : un appel à l’API Classement pour obtenir une liste classée de votre contenu, et un appel à l’API Récompense pour signaler une récompense. Voici les exemples d’appels dans [cURL](https://en.wikipedia.org/wiki/CURL).

Pour plus d’informations, voir la documentation de référence sur les [API](custom-decision-service-api-reference.md).

Commencez avec l’appel à l’API Classement. Créez le fichier `<request.json>`, qui a l’ID de l’ensemble d’actions. Cet ID est le nom du flux RSS ou Atom correspondant que vous avez entré sur le portail :

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

Vous pouvez spécifier plusieurs ensembles d’actions comme suit :

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

Ce fichier JSON est ensuite envoyé dans le cadre de la demande de classement :

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

Ici, `<appId>` est le nom de votre application inscrite sur le portail. Vous devriez recevoir un jeu ordonné d’éléments de contenu, vous pouvez rendre dans votre application. Voici un exemple de renvoi :

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

La première partie de la valeur renvoyée contient une liste d’actions ordonnées, spécifiées par leur ID d’action. Pour un article, l’ID d’action est une URL. La demande globale a également un `<eventId>` unique, créé par le système.

Ultérieurement, vous pourrez spécifier si vous avez observé un clic sur le premier élément de contenu à partir de cet événement, qui est `<actionId3>`. Vous pouvez ensuite signaler une récompense sur ce `<eventId>` au Service Décision personnalisée via l’API Récompense, avec une autre demande telle que :

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

Enfin, vous devez fournir l’API Ensemble d’actions qui renvoie la liste des articles (actions) que le Service Décision personnalisée doit prendre en compte. Implémentez cette API en tant que flux RSS, comme illustré ici :

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <pubDate>Thu, 27 Apr 2017 16:30:52 GMT</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Ici, chaque élément `<item>` de niveau supérieur décrit un article. Le `<link>` est obligatoire. Il est utilisé en tant qu’ID d’action par le Service Décision personnalisée. Spécifiez `<date>` (au format RSS standard) si vous avez plus de 15 articles. Les 15 articles les plus récents sont utilisés. Le `<title>` est facultatif. Il est utilisé pour créer des fonctionnalités de texte pour l’article.

## <a name="next-steps"></a>Étapes suivantes

* Suivez un [didacticiel](custom-decision-service-tutorial-news.md) pour obtenir un exemple plus détaillé.
* Pour en savoir plus sur les fonctionnalités fournies, voir la documentation de référence sur les [API](custom-decision-service-api-reference.md).