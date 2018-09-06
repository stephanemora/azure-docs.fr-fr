---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: ee50a104a75d3cd5ff958bd49a1ff7010c5d5083
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144400"
---
<!-- N.B. no header, language-agnostic -->

Le [kit de développement logiciel (SDK) Microsoft Cognitive Services Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) fournit la façon la plus simple d’utiliser la **traduction vocale** dans votre application.
Le kit de développement logiciel (SDK) offre toutes les fonctionnalités du service. Le processus de base pour effectuer la traduction vocale comprend les étapes suivantes :

1. Créez une fabrique vocale et indiquez une clé d’abonnement au service Speech ou un jeton d’autorisation, et une [région](~/articles/cognitive-services/speech-service/regions.md) comme paramètres.
   
1. Créez un module de reconnaissance de traduction à partir de la fabrique vocale. Vous pouvez configurer les langues source et cible, et spécifier si vous souhaitez une sortie vocale ou texte. Il existe différentes versions du module de reconnaissance de traduction en fonction de la source audio que vous utilisez.

1. Liez les événements pour un fonctionnement asynchrone, si vous le souhaitez. Le module de reconnaissance appelle ensuite vos gestionnaires d’événements lorsqu’il détient des résultats intermédiaires et finaux, ainsi qu’un événement de synthèse pour la sortie audio facultative. Sinon, votre application reçoit uniquement un résultat de transcription final.

1. Démarrez la reconnaissance. Pour une traduction ponctuelle, utilisez la méthode `RecognizeAsync()`, qui retourne le premier énoncé reconnu. Pour une traduction longues, utilisez la méthode `StartContinuousRecognitionAsync()` et liez les événements afin d’obtenir des résultats de reconnaissance asynchrone.

Consultez les extraits de code suivant pour les scénarios de traduction vocale utilisant le kit de développement logiciel Speech.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
