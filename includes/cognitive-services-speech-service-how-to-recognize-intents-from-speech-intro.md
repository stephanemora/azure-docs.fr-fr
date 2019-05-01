---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 80bf9247bbb07fa61b7153e321b1991b82a9d616
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60805496"
---
<!-- N.B. no header, language-agnostic -->

Le [SDK Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) de Microsoft Cognitive Services offre un moyen pour reconnaître **les intentions dans une entrée vocale**. Il est pris en charge par le service [Language Understanding Intelligent Service (LUIS)](https://www.luis.ai/home) de Cognitive Services.

1. Créez une configuration vocale avec une clé d’abonnement LUIS et spécifiez une [région](~/articles/cognitive-services/speech-service/regions.md#intent-recognition) dans les paramètres. La clé d’abonnement LUIS est appelée **clé du point de terminaison** dans la documentation du service. Vous ne pouvez pas utiliser la clé de création LUIS. (Consultez les notes qui se trouvent un peu plus loin dans cette section.)

1. Créez un module de reconnaissance d’intention à partir de la configuration de reconnaissance vocale. Fournissez une configuration audio si vous voulez effectuer une reconnaissance à partir d’une source autre que votre microphone par défaut (par exemple, un flux audio ou un fichier audio).

1. Obtenez le modèle de compréhension linguistique qui est basé sur votre **AppId**. Ajoutez les intentions souhaitées.

1. Liez les événements pour un fonctionnement asynchrone, si vous le souhaitez. Le module de reconnaissance appelle ensuite vos gestionnaires d’événements lorsqu’il trouve des résultats intermédiaires et finaux (notamment des intentions). Si vous n’associez pas les événements, votre application reçoit uniquement un résultat de transcription final.

1. Démarrez la reconnaissance de l’intention. Pour une reconnaissance ponctuelle, (reconnaissance de commandes ou de requêtes), utilisez la méthode `RecognizeOnceAsync()`. Cette méthode renvoie le premier énoncé reconnu. Pour une reconnaissance plus longue, utilisez la méthode `StartContinuousRecognitionAsync()`. Liez les événements pour obtenir des résultats de reconnaissance asynchrones.

Consultez les extraits de code suivants pour les scénarios de reconnaissance de l’intention utilisant le SDK Speech. Remplacez les valeurs de l’exemple par votre propre clé d’abonnement LUIS (clé du point de terminaison), la [région de votre abonnement](~/articles/cognitive-services/speech-service/regions.md#intent-recognition) et l’**AppId** de votre modèle d’intention.

> [!NOTE]
> Contrairement à d’autres services pris en charge par le SDK Speech, la reconnaissance de l’intention requiert une clé d’abonnement spécifique (clé du point de terminaison LUIS). Pour plus d’informations sur la technologie de reconnaissance de l’intention, consultez le [site Web LUIS](https://www.luis.ai). Pour plus d’informations l’acquisition de la **clé du point de terminaison**, consultez l’article [Créer une clé du point de terminaison LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-how-to-azure-subscription).
