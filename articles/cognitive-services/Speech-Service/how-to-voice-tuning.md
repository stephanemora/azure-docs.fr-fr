---
title: Ajuster la sortie de synthèse vocale - Services de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Activer la journalisation dans le Speech SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 2d56aba6d62d70c482ed1489a09276eeaa11f956
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025843"
---
# <a name="fine-tune-text-to-speech-output"></a>Ajuster la sortie de synthèse vocale

Les Services de reconnaissance vocale Azure permettent de régler la vitesse, la prononciation, le volume, la hauteur et la contour de synthèse vocale à l’aide de la sortie [Markup langage SSML (Speech Synthesis)](speech-synthesis-markup.md). SSML est un langage de balisage basé sur XML qui utilise des balises pour informer la fonctionnalité est nécessaire de régler le service. Le SSML message est ensuite envoyé dans le corps de chaque demande au service de synthèse vocale. Pour simplifier le processus de personnalisation, les Services de reconnaissance vocale offrent désormais une [voix réglage](https://aka.ms/voicetuning) sorties d’outil qui vous permet d’inspecter et ajuster visuellement synthèse vocale en temps réel.

L’outil de réglage de la voix prend en charge de Microsoft [standard](language-support.md#standard-voices), [neuronal](language-support.md#text-to-speech), et [voix personnalisées](how-to-customize-voice-font.md).

## <a name="get-started-with-the-voice-tuning-tool"></a>Bien démarrer avec l’outil de réglage de la voix

Avant de commencer le réglage de la sortie avec l’outil de réglage de la voix de synthèse vocale, vous devez effectuer ces étapes :

1. Créer un [Microsoft compte gratuit](https://account.microsoft.com/account) si vous n’en avez pas déjà.
2. Créer un [compte Azure gratuit](https://azure.microsoft.com/en-us/free/) si vous n’en avez pas déjà. Cliquez sur **démarrer gratuitement**et créez un compte Azure à l’aide de votre compte Microsoft.

3. Créer un abonnement de Services de reconnaissance vocale dans le portail Azure. Obtenir des instructions détaillées pour [comment créer une ressource de reconnaissance vocale](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) sont disponibles.
   >[!NOTE]
   >Lorsque vous créez une ressource de reconnaissance vocale dans le portail Azure, les informations d’emplacement Azure doivent correspondre à la région de voix de synthèse vocale. Voix de synthèse neuronal prend en charge un ensemble de sub des emplacements Azure. Pour obtenir une liste complète de prise en charge, consultez [régions](regions.md#text-to-speech).

   >[!NOTE]
   >Vous devez disposer d’un F0 ou une clé de S0 créée dans le portail Azure avant de pouvoir utiliser le service. Voix Tuning **ne** prennent en charge la [clé de version d’évaluation gratuite de 30 jours](https://review.docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial).

4. Se connecter à la [voix réglage](https://aka.ms/voicetuning) portail et vous connecter à votre abonnement aux Services de reconnaissance vocale. Choisissez un seul abonnement de Services de reconnaissance vocale, puis créez un projet.
5. Sélectionnez **nouveau réglage**. Exécutez ensuite les opérations qui suivent :

   * Recherchez et sélectionnez **tous les abonnements**.  
   * Sélectionnez **Connecter un abonnement existant**.  
     ![Se connecter à un abonnement existant](./media/custom-voice/custom-voice-connect-subscription.png).
   * Entrez votre clé d’abonnement Azure de Services de reconnaissance vocale à partir du portail Azure, puis sélectionnez **ajouter**. Vos clés d’abonnement sont disponibles dans le portail Azure à partir du volet de gestion des ressources.  Vous pouvez également obtenir les clés à partir de la liste des abonnements dans le portail de réglage de la voix.
   * Si vous avez plusieurs abonnements de Services de reconnaissance vocale que vous projetez d’utiliser, répétez ces étapes pour chaque abonnement.

## <a name="customize-the-text-to-speech-output"></a>Personnaliser la sortie de synthèse vocale

Maintenant que vous avez créé des comptes et votre abonnement lié, vous pouvez démarrer le réglage de la sortie de synthèse vocale.

1. Choisissez une voix.
2. Entrez le texte que vous souhaitez modifier.
3. Avant de commencer à apporter des modifications, lire l’audio pour faire une idée de la sortie.
4. Sélectionnez la word/phrase que vous souhaitez affiner et commencez à expérimenter avec différentes fonctions de base SSML.

>[!TIP]
> Pour obtenir des informations détaillées sur l’ajustement SSML et le réglage de la sortie de la voix, consultez [Markup langage SSML (Speech Synthesis)](speech-synthesis-markup.md).

## <a name="limitations"></a>Limites

Réglage de voix neuronaux est légèrement différente de celle de paramétrage pour la voix Standard et personnalisés.

* Intonation n’est pas pris en charge pour la voix neuronal.
* Les fonctionnalités de tonalité et volume fonctionnent uniquement avec des phrases complètes. Ces fonctionnalités ne sont pas disponibles au niveau du mot.
* Taux, certaines voix neuronaux peut être paramétrées en fonction de mots, tandis que d’autres nécessitent que vous permet de sélectionner des phrases entières.

> [!TIP]
> L’outil de réglage de la voix fournit des informations contextuelles sur les fonctionnalités et le paramétrage.

## <a name="next-steps"></a>Étapes suivantes
* [Créer une ressource de reconnaissance vocale dans Azure](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [Démarrer l’optimisation de la voix](https://speech.microsoft.com/app.html#/VoiceTuning)
* [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md)
