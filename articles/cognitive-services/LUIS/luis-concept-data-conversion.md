---
title: Concepts de conversion de données dans LUIS - Language Understanding
titleSuffix: Azure Cognitive Services
description: Découvrez comment les énoncés peuvent être modifiés avant les prédictions de Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 3862a0dbd94b5764cf506b05201d8dc60430fc7d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038967"
---
# <a name="data-conversion-concepts-in-luis"></a>Concepts de conversion des données dans LUIS
LUIS utilise le service de reconnaissance vocale de Cognitive Services pour convertir les énoncés d’énoncés parlés en énoncés textuels avant la prédiction. 

## <a name="speech-to-intent-conversion-concepts"></a>Concepts de conversion de sortie orale en intention
La conversion de la reconnaissance vocale dans LUIS vous permet d’envoyer des énoncés parlés d’envoi à un point de terminaison et de recevoir une réponse de prédiction LUIS. Le processus est une intégration du service de [reconnaissance vocale](https://docs.microsoft.com/azure/cognitive-services/Speech) avec LUIS. 

### <a name="key-requirements"></a>Conditions clés
Vous n’avez pas besoin de créer une clé d’**API Reconnaissance vocale Bing** pour cette intégration. Une clé **Language Understanding** créée dans le portail Azure fonctionne pour cette intégration. N’utilisez pas la clé de démarrage LUIS, elle ne fonctionnera pas pour cette intégration.

### <a name="new-endpoint"></a>Nouveau point de terminaison 
Cette intégration crée un nouveau point de terminaison et un modèle de [tarification](luis-boundaries.md#key-limits). Le point de terminaison, via le [Kit de développement logiciel (SDK) de reconnaissance vocale](https://github.com/Azure-Samples/cognitive-services-speech-sdk), est capable de recevoir les énoncés parlés et textuels, ce qui vous permet de l’utiliser en tant que point de terminaison unique. 

### <a name="quota-usage"></a>Rapport d’utilisation des quotas
Voir les [limites de clés](luis-boundaries.md#key-limits) pour plus d’informations. 

### <a name="data-retention"></a>Conservation des données
Les données envoyées au point de terminaison via le Kit de développement logiciel (SDK) de reconnaissance vocale, qu’il s’agisse de contenu parlé ou textuel, sont utilisées seulement pour optimiser votre modèle vocal. Il n’est pas utilisé au-delà de votre modèle pour améliorer la reconnaissance vocale ou LUIS dans une capacité générale. Lorsque l’application LUIS est supprimée, les données conservées le sont également.

<!-- TBD: Machine translation conversion concepts -->

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Utiliser la reconnaissance vocale](luis-tutorial-speech-to-intent.md)

