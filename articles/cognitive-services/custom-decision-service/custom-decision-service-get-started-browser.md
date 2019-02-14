---
title: Appeler une API à partir d’un navigateur – Service Décision personnalisée
titlesuffix: Azure Cognitive Services
description: Comment optimiser une page web en adressant des appels d’API directement à partir d’un navigateur au Service Décision personnalisée.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.openlocfilehash: 2b356e2f0fe9235d49dffa7417cd3894059f9caf
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867560"
---
# <a name="call-api-from-a-browser"></a>Appeler des API à partir d’un navigateur

Cet article vous aide à appeler les API du service Décision personnalisée Azure directement depuis un navigateur.

Commencez par [inscrire votre application](custom-decision-service-get-started-register.md).

Allons-y. Votre application est modélisée comme ayant une première page, qui mène à plusieurs pages d’article. La première page utilise le service Décision personnalisée pour spécifier l’ordre de ses pages d’article. Dans l’en-tête HTML de la première page, insérez le code suivant :

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

L’argument `data` contient le classement des URL à afficher. Pour plus d’informations, voir la documentation de référence sur les [API](custom-decision-service-api-reference.md).

Pour gérer un utilisateur, cliquez sur le premier article, puis appelez le code suivant sur la première page :

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Ici, `data` est l’argument de la fonction `callback()`. Vous trouverez un exemple d’implémentation de ce [didacticiel](custom-decision-service-tutorial-news.md#use-the-apis).

Enfin, vous devez fournir l’API Ensemble d’actions qui renvoie la liste des articles (actions) que le service Décision personnalisée doit prendre en compte. Implémentez cette API en tant que flux RSS, comme illustré ici :

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

Ici, chaque élément `<item>` de niveau supérieur décrit un article. Le `<link>` est obligatoire. Il est utilisé en tant qu’ID d’action par le service Décision personnalisée. Spécifiez `<date>` (au format RSS standard) si vous avez plus de 15 articles. Les 15 articles les plus récents sont utilisés. Le `<title>` est facultatif. Il est utilisé pour créer des fonctionnalités de texte pour l’article.

## <a name="next-steps"></a>Étapes suivantes

* Suivez un [didacticiel](custom-decision-service-tutorial-news.md) pour obtenir un exemple plus détaillé.
* Pour en savoir plus sur les fonctionnalités fournies, voir la documentation de référence sur les [API](custom-decision-service-api-reference.md).
