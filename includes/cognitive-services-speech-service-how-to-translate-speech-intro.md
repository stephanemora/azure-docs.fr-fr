---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: ab4795325f733d15403b53f027daa4fb8c824cc6
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47185155"
---
<!-- N.B. no header, language-agnostic -->

Le [kit de développement logiciel (SDK) Microsoft Cognitive Services Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) fournit la façon la plus simple d’utiliser la **traduction vocale** dans votre application.
Le kit de développement logiciel (SDK) offre toutes les fonctionnalités du service. Le processus de base pour effectuer la traduction vocale comprend les étapes suivantes :

1. Créez une configuration de traduction vocale et indiquez une clé d’abonnement au service Speech (ou un jeton d’autorisation) ainsi qu’une [région](~/articles/cognitive-services/speech-service/regions.md) comme paramètres. Changez la configuration en fonction des besoins. Par exemple, vous pouvez configurer les langues source et cible, et spécifier si vous souhaitez une sortie vocale ou texte.

1. Créez un module de reconnaissance de traduction à partir de la configuration de traduction vocale. Fournissez une configuration audio si vous souhaitez procéder à une reconnaissance depuis une source autre que votre microphone par défaut (par exemple, un flux audio ou un fichier audio).

1. Liez les événements pour un fonctionnement asynchrone, si vous le souhaitez. Le module de reconnaissance appelle ensuite vos gestionnaires d’événements lorsqu’il détient des résultats intermédiaires et finaux, ainsi qu’un événement de synthèse pour la sortie audio facultative. Dans le cas contraire, votre application reçoit uniquement un résultat de transcription final.

1. Démarrez la reconnaissance. Pour une traduction ponctuelle, utilisez la méthode `RecognizeOnceAsync()`, qui retourne le premier énoncé reconnu. Pour une traduction longues, utilisez la méthode `StartContinuousRecognitionAsync()` et liez les événements afin d’obtenir des résultats de reconnaissance asynchrone.

Consultez les extraits de code suivant pour les scénarios de traduction vocale utilisant le kit de développement logiciel Speech.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
