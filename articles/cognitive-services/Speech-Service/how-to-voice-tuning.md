---
title: Régler la sortie de synthèse vocale - services Speech
titleSuffix: Azure Cognitive Services
description: Activez la journalisation dans le SDK Speech.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 94b58279b1a9fd4d9acdb4183f59b0a8579c17fd
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606447"
---
# <a name="fine-tune-text-to-speech-output"></a>Régler la sortie de synthèse vocale

Les services Azure Speech vous permettent d'ajuster la vitesse, la prononciation, le volume, la hauteur et le contour de la synthèse vocale en utilisant [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md). SSML est un langage de balisage basé sur XML qui utilise des balises pour informer le service sur les fonctionnalités nécessitant un réglage. Le message SSML est alors envoyé dans le corps de chaque requête au service de synthèse vocale. Pour simplifier le processus de personnalisation, les services Speech offrent maintenant un outil [Voice Tuning](https://aka.ms/voicetuning) qui vous permet d'inspecter visuellement et de régler en temps réel les sorties de synthèse vocale.

L’outil Voice Tuning prend en charge les voix [standard](language-support.md#standard-voices), [neuronales](language-support.md#text-to-speech) et [personnalisées](how-to-customize-voice-font.md).

## <a name="get-started-with-the-voice-tuning-tool"></a>Prise en main de l’outil Voice Tuning

Avant de pouvoir commencer à régler la synthèse vocale à l'aide de l'outil Voice Tuning, vous devez effectuer les étapes suivantes :

1. Créez un [compte Microsoft gratuit](https://account.microsoft.com/account) si vous n’en avez pas.
2. Créez un [compte Azure gratuit](https://azure.microsoft.com/free/) si vous n’en avez pas. Cliquez sur **Démarrer gratuitement** et créez un compte Azure à l’aide de votre compte Microsoft.

3. Créez un abonnement Services Speech dans le portail Azure. Des instructions étape par étape pour [créer une ressource vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) sont disponibles.
   >[!NOTE]
   >Lorsque vous créez une ressource vocale dans le portail Azure, les informations de localisation Azure doivent correspondre à la région vocale TTS. La voix TTS neuronale prend en charge un sous-ensemble d’emplacements Azure. Pour obtenir la liste complète, consultez [Régions](regions.md#text-to-speech).

   >[!NOTE]
   >Vous devez avoir une clé F0 ou S0 créée dans le portail Azure avant de pouvoir utiliser le service. Voice Tuning ne prend **pas** en charge la [clé d'essai de 30 jours](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial).

4. Connectez-vous au portail [Voice Tuning](https://aka.ms/voicetuning) puis accédez à votre abonnement Services Speech. Choisissez un seul abonnement Services Speech, puis créez un projet.
5. Sélectionnez **Nouveau réglage**. Exécutez ensuite les opérations qui suivent :

   * Recherchez et sélectionnez **Tous les abonnements**.  
   * Sélectionnez **Connecter un abonnement existant**.  
     ![Connectez un abonnement existant](./media/custom-voice/custom-voice-connect-subscription.png).
   * Entrez votre clé d'abonnement Services Speech Azure, puis sélectionnez **Ajouter**. Vos clés d'abonnement sont disponibles dans la [page Abonnement](https://go.microsoft.com/fwlink/?linkid=2090458) du portail de personnalisation vocale. Vous pouvez également obtenir les clés à partir du volet Gestion des ressources du portail [Azure](https://portal.azure.com/).
   * Si vous envisagez d’utiliser plusieurs abonnements Services Speech, répétez ces étapes pour chaque abonnement.

## <a name="customize-the-text-to-speech-output"></a>Personnaliser la sortie de synthèse vocale

Maintenant que vous avez créé des comptes et lié votre abonnement, vous pouvez commencer à régler la synthèse vocale.

1. Choisissez une voix.
2. Saisissez le texte que vous souhaitez modifier.
3. Avant de commencer à apporter des modifications, écoutez l'audio pour vous faire une idée du résultat.
4. Sélectionnez le mot/la phrase que vous voulez régler et commencez à tester différentes fonctions basées sur SSML.

>[!TIP]
> Pour des informations détaillées sur le réglage SSML et le réglage de la sortie vocale, consultez [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md).

## <a name="limitations"></a>Limites

Le réglage de la voix neuronale est légèrement différent de celui des voix standard et personnalisées.

* L'intonation n'est pas prise en charge pour les voix neuronales.
* Les fonctions de hauteur et de volume ne fonctionnent qu'avec des phrases complètes. Les fonctionnalités suivantes ne sont pas disponibles au niveau du mot.
* Au niveau du débit, certaines voix neuronales peuvent être réglées en fonction de mots, alors que d'autres exigent la sélection de phrases complètes.

> [!TIP]
> L'outil Voice Tuning fournit des informations contextuelles sur les fonctions et le réglage.

## <a name="next-steps"></a>Étapes suivantes
* [Créer une ressource vocale dans Azure](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [Démarrer le réglage de la voix](https://speech.microsoft.com/app.html#/VoiceTuning)
* [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md)
