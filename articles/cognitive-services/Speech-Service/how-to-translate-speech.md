---
title: Utiliser la traduction vocale à l’aide des services Speech | Microsoft Docs
description: Découvrez comment utiliser la traduction vocale du service Speech.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 7f39f284998489574049d82c44b3d3a0a3797adb
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2018
ms.locfileid: "35378981"
---
# <a name="translate-speech-using-speech-service"></a>Traduction vocale à l’aide du service Speech

Le [kit de développement logiciel (SDK) Speech](speech-sdk.md) constitue la façon la plus simple d’utiliser la traduction vocale dans votre application. Le kit de développement logiciel (SDK) offre toutes les fonctionnalités du service. Le processus de base pour effectuer la traduction vocale comprend les étapes suivantes :

1. Créez une fabrique vocale et indiquez une clé d’abonnement au service Speech ou un jeton d’autorisation. Vous pouvez également configurer les langues source et cible, et spécifier si vous souhaitez une sortie vocale ou texte.

2. Obtenez un module de reconnaissance auprès de la fabrique. Pour la traduction, sélectionnez un module de reconnaissance de traduction. (Les autres modules de reconnaissance sont destinés à la *reconnaissance vocale*.) Il existe différentes versions du module de reconnaissance de traduction en fonction de la source audio que vous utilisez.

4. Liez des événements pour un fonctionnement asynchrone, si vous le souhaitez. Le module de reconnaissance appelle vos gestionnaires d’événements lorsqu’il trouve des résultats intermédiaires et finaux. Sinon, votre application reçoit un résultat de traduction final.

5. Lancez la reconnaissance et la traduction.

# <a name="sdk-samples"></a>Exemples du kit de développement logiciel (SDK)

Pour obtenir la dernière série d’exemples, consultez le [dépôt GitHub d’exemples pour le kit SDK de reconnaissance vocale Cognitive Services](https://aka.ms/csspeech/samples).

# <a name="next-steps"></a>Étapes suivantes

- [Obtenir votre abonnement d’essai gratuit du service Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Reconnaissance vocale dans C#](quickstart-csharp-windows.md)
