---
title: Icône Analyse des sentiments - LUIS
titleSuffix: Azure Cognitive Services
description: Si l’analyse des sentiments est configurée, la réponse JSON de LUIS l’intègre.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/22/2019
ms.author: diberry
ms.openlocfilehash: dee07859da97f76d9a2db3ccbb71ae74d8ff4846
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270419"
---
# <a name="sentiment-analysis"></a>analyse de sentiments
Si l’analyse des sentiments est configurée, la réponse JSON de LUIS l’intègre. Pour plus d’informations sur l’analyse des sentiments, consultez la documentation [Analyse de texte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).


## <a name="resolution-for-sentiment"></a>Résolution des sentiments

Les données de sentiment correspondent à un score compris entre 1 et 0 indiquant le sentiment, positif (plus proche de 1) ou négatif (plus proche de 0), des données.

#### <a name="english-language"></a>[Langue anglaise](#tab/english)

Lorsque la culture est `en-us`, la réponse est :

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Autres langues](#tab/other-languages)

Pour toutes les autres cultures, la réponse est :

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

