---
title: Conversion de données
titleSuffix: Language Understanding - Azure Cognitive Services
description: Découvrez comment les énoncés peuvent être modifiés avant les prédictions de Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: diberry
ms.openlocfilehash: bdf2d87e558726937fa4221a0d95179216c66051
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813817"
---
# <a name="convert-data-format-of-utterances"></a>Convertir le format de données des énoncés
LUIS utilise le service de reconnaissance vocale de Cognitive Services pour convertir les énoncés d’énoncés parlés en énoncés textuels avant la prédiction. 

## <a name="speech-to-intent-conversion-concepts"></a>Concepts de conversion de sortie orale en intention
La conversion de la reconnaissance vocale dans LUIS vous permet d’envoyer des énoncés parlés d’envoi à un point de terminaison et de recevoir une réponse de prédiction LUIS. Le processus est une intégration du service de [reconnaissance vocale](https://docs.microsoft.com/azure/cognitive-services/Speech) avec LUIS. En savoir plus sur la conversion de sortie orale en intention avec un [tutoriel](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Conditions clés
Vous n’avez pas besoin de créer une clé d’**API Reconnaissance vocale Bing** pour cette intégration. Une clé **Language Understanding** créée dans le portail Azure fonctionne pour cette intégration. N’utilisez pas la clé de démarrage LUIS.

### <a name="pricing-tier"></a>Niveau de tarification
Cette intégration utilise un modèle [tarifaire](luis-boundaries.md#key-limits) différent de celui des niveaux tarifaires habituels de Language Understanding. 

### <a name="quota-usage"></a>Rapport d’utilisation des quotas
Voir les [limites de clés](luis-boundaries.md#key-limits) pour plus d’informations. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Utiliser la reconnaissance vocale](luis-tutorial-speech-to-intent.md)

