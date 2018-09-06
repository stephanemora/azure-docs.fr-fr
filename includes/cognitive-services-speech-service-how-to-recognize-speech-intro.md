---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 1103e5a217ca4972cc1c983a7ad0d07b0797e9e9
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43129323"
---
<!-- N.B. no header, no intents here, language-agnostic -->

Le [SDK Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) Cognitive Services fournit une méthode très simple pour utiliser des fonctionnalités de **reconnaissance vocale** complètes dans votre application.

1. Créez une fabrique vocale et indiquez une clé d’abonnement au service Speech (ou un jeton d’autorisation) ainsi qu’une [région](~/articles/cognitive-services/speech-service/regions.md) en tant que paramètres. Vous pouvez également fournir un point de terminaison personnalisé pour spécifier un point de terminaison de service non standard.

1. Obtenez un module de reconnaissance vocale à partir de la fabrique vocale. Vous pouvez configurer la langue d’entrée et le format de sortie. Un module de reconnaissance peut utiliser le microphone par défaut de votre appareil, un flux audio ou un fichier audio.

1. Liez les événements pour un fonctionnement asynchrone, si vous le souhaitez. Le module de reconnaissance appelle ensuite vos gestionnaires d’événements lorsqu’il trouve des résultats intermédiaires et finaux. Dans le cas contraire, votre application reçoit uniquement un résultat de transcription final.

1. Démarrez la reconnaissance. Pour une reconnaissance ponctuelle, (reconnaissance de commandes ou de requêtes), utilisez la méthode `RecognizeAsync()`. Cette méthode renvoie le premier énoncé reconnu. Pour une reconnaissance plus longue, comme les transcriptions, utilisez la méthode `StartContinuousRecognitionAsync()`. Liez les événements pour obtenir des résultats de reconnaissance asynchrones.

Consultez les extraits de code suivants pour les scénarios de reconnaissance vocale utilisant le SDK Speech.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
