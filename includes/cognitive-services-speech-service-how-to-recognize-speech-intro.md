---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 3394fc2f6395799a252b645635d982b4573296c6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47000705"
---
<!-- N.B. no header, no intents here, language-agnostic -->

Le [SDK Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) Cognitive Services fournit une méthode très simple pour utiliser des fonctionnalités de **reconnaissance vocale** complètes dans votre application.

1. Créez une configuration de reconnaissance vocale et indiquez une clé d’abonnement au service Speech (ou un jeton d’autorisation) ainsi qu’une [région](~/articles/cognitive-services/speech-service/regions.md) en tant que paramètres. Changez la configuration en fonction des besoins. Par exemple, fournissez un point de terminaison personnalisé pour spécifier un point de terminaison de service non standard, ou sélectionnez la langue d’entrée parlée ou le format de sortie.

1. Créez un module de reconnaissance vocale à partir de la configuration de reconnaissance vocale. Fournissez une configuration audio si vous souhaitez procéder à une reconnaissance depuis une source autre que votre microphone par défaut (par exemple, un flux audio ou un fichier audio).

1. Liez les événements pour un fonctionnement asynchrone, si vous le souhaitez. Le module de reconnaissance appelle ensuite vos gestionnaires d’événements lorsqu’il trouve des résultats intermédiaires et finaux. Dans le cas contraire, votre application reçoit uniquement un résultat de transcription final.

1. Démarrez la reconnaissance. Pour une reconnaissance ponctuelle, (reconnaissance de commandes ou de requêtes), utilisez la méthode `RecognizeOnceAsync()`. Cette méthode renvoie le premier énoncé reconnu. Pour une reconnaissance plus longue, comme les transcriptions, utilisez la méthode `StartContinuousRecognitionAsync()`. Liez les événements pour obtenir des résultats de reconnaissance asynchrones.

Consultez les extraits de code suivants pour les scénarios de reconnaissance vocale utilisant le SDK Speech.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
