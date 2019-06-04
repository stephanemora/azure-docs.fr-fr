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
ms.openlocfilehash: a148c849d0935978f049e01dd254c4c18800ee3b
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496983"
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
> [Extraction de données](luis-concept-data-extraction.md)

