---
title: Utiliser la reconnaissance vocale | Microsoft Docs
description: En savoir plus sur l’utilisation de la reconnaissance vocale du service Speech
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 878a31992415b1f8688afcfb186fcd94ce2567b4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2018
ms.locfileid: "35378925"
---
# <a name="use-speech-to-text-in-the-speech-service"></a>Utiliser la « reconnaissance vocale » du service Speech

Vous pouvez utiliser la **reconnaissance vocale** dans vos applications de deux manières différentes.

| Méthode | Description |
|-|-|
| [Foundation](speech-sdk.md) | Méthode la plus simple pour les développeurs C/C++, C# et Java* |
| [REST](rest-apis.md) | Reconnaître les énoncés courts à l’aide d’une requête HTTP POST | 

\* *Le Kit de développement logiciel (SDK) Java fait partie du [Kit de développement logiciel (SDK) Speech Devices](speech-devices-sdk.md).*

## <a name="using-the-sdk"></a>Utilisation du kit de développement logiciel

Le [Kit de développement logiciel (SDK) Speech](speech-sdk.md) fournit la méthode d’utilisation de la **reconnaissance vocale** la plus simple dans votre application avec des fonctionnalités complètes.

1. Créez une fabrique vocale en indiquant une clé d’abonnement au service Speech ou un jeton d’autorisation. À ce stade, vous pouvez également configurer des options, telles que la langue de reconnaissance ou un point de terminaison personnalisé pour vos propres modèles de reconnaissance vocale.

2. Obtenez un module de reconnaissance auprès de la fabrique. Trois types de modules de reconnaissance différents sont disponibles. Chaque type de module de reconnaissance peut utiliser le microphone par valeur de votre appareil, un flux audio ou l’audio d’un fichier.

    Module de reconnaissance | Fonction
    -|-
    Module de reconnaissance vocale|Fournit la transcription texte de la voix
    Module de reconnaissance de l’intention|Dérive l’intention de l’orateur via [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) après la reconnaissance\*
    Module de reconnaissance de traduction|Traduit le texte transcrit dans une autre langue (consultez [Traduction vocale](how-to-translate-speech.md))

    \* *Pour la reconnaissance de l’intention, vous devez utiliser une clé d’abonnement LUIS distincte lors de la création d’une fabrique vocale pour le module de reconnaissance de l’intention.*
    
4. Liez les événements pour un fonctionnement asynchrone, si vous le souhaitez. Le module de reconnaissance appelle ensuite vos gestionnaires d’événements lorsqu’il trouve des résultats intermédiaires et finaux. Sinon, votre application recevra un résultat de transcription final.

5. Démarrez la reconnaissance.

### <a name="sdk-samples"></a>Exemples du Kit de développement logiciel (SDK)

Pour obtenir la dernière série d’exemples, consultez le [dépôt GitHub d’exemples pour le kit SDK Speech de Cognitive Services](https://aka.ms/csspeech/samples).

## <a name="using-the-rest-api"></a>Utilisation de l’API REST

L’API REST est la méthode la plus simple pour reconnaître la voix si vous n’utilisez pas un langage pris en charge par le Kit de développement logiciel (SDK). Vous effectuez une requête HTTP POST sur le point de terminaison de service, en transmettant l’intégralité de l’énoncé dans le corps de la requête. Vous recevez une réponse contenant le texte reconnu.

> [!NOTE]
> Les énoncés sont limités à 15 secondes maximum lors de l’utilisation de l’API REST.


Pour plus d’informations sur l’API REST **Reconnaissance vocale**, consultez l’article sur les [API REST](rest-apis.md#speech-to-text). Pour le voir en action, téléchargez les [exemples API REST](https://github.com/Azure-Samples/SpeechToText-REST) à partir de GitHub.

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir votre abonnement d’essai gratuit du service Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Reconnaissance vocale dans C#](quickstart-csharp-windows.md)
