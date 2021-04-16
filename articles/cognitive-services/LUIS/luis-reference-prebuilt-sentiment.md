---
title: Icône Analyse des sentiments - LUIS
titleSuffix: Azure Cognitive Services
description: Si l’analyse des sentiments est configurée, la réponse JSON de LUIS l’intègre.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/06/2021
ms.openlocfilehash: 7524644b34a6fd479c08b9ce6418c547c836add5
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554023"
---
# <a name="sentiment-analysis"></a>analyse de sentiments
Si l’analyse des sentiments est configurée, la réponse JSON de LUIS l’intègre. Pour plus d’informations sur l’analyse des sentiments, consultez la documentation [Analyse de texte](../text-analytics/index.yml).

LUIS utilise Analyse de texte v2. 

L’analyse des sentiments est configurée lors de la publication de votre application. Pour plus d’informations, consultez [Comment publier une application](./luis-how-to-publish-app.md).

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
