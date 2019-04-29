---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
origin.date: 09/24/2018
ms.date: 04/01/2019
ms.author: v-biyu
ms.openlocfilehash: 31482cc7850c574b952c454021af729da324ba15
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769938"
---
<!-- N.B. no header, no intents here, language-agnostic -->

Le [SDK Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) Cognitive Services fournit une méthode très simple pour utiliser des fonctionnalités de **reconnaissance vocale** complètes dans votre application.

1. Créez une configuration de reconnaissance vocale et indiquez une clé d’abonnement au service Speech (ou un jeton d’autorisation) ainsi qu’une [région](~/articles/cognitive-services/speech-service/regions.md) en tant que paramètres. Changez la configuration en fonction des besoins. Par exemple, fournissez un point de terminaison personnalisé pour spécifier un point de terminaison de service non standard, ou sélectionnez la langue d’entrée parlée ou le format de sortie.

1. Créez un module de reconnaissance vocale à partir de la configuration de reconnaissance vocale. Fournissez une configuration audio si vous voulez effectuer une reconnaissance à partir d’une source autre que votre microphone par défaut (par exemple, un flux audio ou un fichier audio).

1. Liez les événements pour un fonctionnement asynchrone, si vous le souhaitez. Le module de reconnaissance appelle ensuite vos gestionnaires d’événements lorsqu’il trouve des résultats intermédiaires et finaux. Dans le cas contraire, votre application reçoit uniquement un résultat de transcription final.

1. Démarrez la reconnaissance. Pour une reconnaissance ponctuelle, (reconnaissance de commandes ou de requêtes), utilisez la méthode `RecognizeOnceAsync()` (ou l’équivalent dans un autre langage). Cette méthode renvoie le premier énoncé reconnu. Pour une reconnaissance plus longue, comme les transcriptions, utilisez la méthode `StartContinuousRecognitionAsync()` (ou l’équivalent dans un autre langage). Liez les événements pour obtenir des résultats de reconnaissance asynchrones.

Consultez les extraits de code suivants pour les scénarios de reconnaissance vocale utilisant le SDK Speech.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
