---
title: Scénarios de reconnaissance vocale de Speech de Cognitive Services
description: Scénarios
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 07/02/2018
ms.author: v-jerkin
ms.openlocfilehash: 98e79112ba70ff578975cea9d872b4fcc41512bc
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2018
ms.locfileid: "41929697"
---
# <a name="speech-scenarios"></a>Scénarios de reconnaissance vocale

Il existe de nombreux scénarios qui sont rendus possibles par la technologie de reconnaissance vocale. Nous analysons les plus courants et mettons en avant leurs principales caractéristiques dans la documentation. Pour la plupart du contenu, le [kit de développement logiciel (SDK)](speech-sdk.md) joue un rôle central dans la mise en œuvre de ces scénarios.

La page décrit comment effectuer les tâches suivantes :
> [!div class="checklist"]
> * Créer des applications déclenchées par la voix
> * Transcrire les appels audio des centres d’appel
> * Robots vocaux

## <a name="voice-triggered-apps"></a>Applications déclenchées par la voix

De nombreux utilisateurs souhaitent activer l’entrée vocale sur leurs applications. L’entrée vocale est un excellent moyen de rendre votre application flexible, qu’il s’agisse de l’utiliser en main libre (par exemple en voiture), ou d’accélérer différentes tâches, telles que des requêtes concernant la navigation, les informations ou la météo. 

### <a name="voice-triggered-apps-with-baseline-models"></a>Applications déclenchée par la voix avec des modèles de base

Si votre application est destinée à être utilisée par le grand public dans des environnements où le bruit de fond n’est pas excessif, le plus simple et le plus rapide est de télécharger notre [kit de développement logiciel (SDK) Speech](speech-sdk.md) et de suivre les [exemples](quickstart-csharp-dotnet-windows.md) correspondants. Le kit de développement logiciel (SDK) alimenté par votre [clé d’abonnement Azure](https://azure.microsoft.com/try/cognitive-services/) permet aux développeurs de charger du contenu audio vers les modèles de reconnaissance vocale de base qui alimentent Cortana et Skype. Les modèles sont conformes à l’état actuel de la technique et sont utilisés par les produits susmentionnés. Vous êtes opérationnel en quelques minutes.

### <a name="voice-triggered-apps-with-custom-models"></a>Applications déclenchées par la voix avec modèles personnalisés

Si votre application traite un domaine spécifique, (par exemple, la chimie, la biologie ou des besoins alimentaires spécifiques), vous envisagez peut-être d’adapter un [modèle de langage](how-to-customize-language-model.md). L’adaptation d’un modèle de langage apprendra au décodeur les phrases et les mots les plus couramment utilisés par votre application. Le décodeur pourra transcrire avec plus de précision une entrée vocale avec un modèle de langage personnalisé que le modèle de base. De même, si le bruit de fond est important à l’endroit où vous comptez utiliser votre application, vous souhaiterez peut-être adapter un modèle acoustique. Parcourez la documentation pour voir les autres cas dans lesquels l’[adaptation de langage](how-to-customize-language-model.md) et l’[adaptation acoustique](how-to-customize-acoustic-models.md) offrent une valeur ajoutée, et visitez notre [portail d’adaptation](https://customspeech.ai) pour démarrer rapidement l’expérience de création de modèle. Comme les modèles de base, les modèles personnalisés sont appelés via notre [kit de développement logiciel (SDK) Speech](speech-sdk.md) et en suivant les [exemples](quickstart-csharp-dotnet-windows.md) pertinentes.

## <a name="transcribe-call-center-audio-calls"></a>Transcrire les appels audio des centres d’appel

Les centres d’appel accumulent de gros volumes de contenu audio. Ces fichiers audio contiennent des informations précieuses qui peuvent être tirées de la transcription. Il est possible de connaître la durée de l’appel, le sentiment, la satisfaction du client et la valeur générale de l’appel pour l’appelant en effectuant une transcriptions de cet appel.

Le meilleur point de départ est l’[API de transcription Batch](batch-transcription.md) avec l’[exemple](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI) associé.

Vous devrez d’abord obtenir une [clé d’abonnement Azure](https://azure.microsoft.com/try/cognitive-services/), puis vous devrez consulter la documentation de l’[API de transcription par lot](batch-transcription.md).

### <a name="transcribe-call-center-audio-calls-with-baseline-models"></a>Transcrire des appels audio de centres d’appel avec des modèles de base

Vous devez décider si vous utiliserez les modèles de base internes pour effectuer la transcription, ou bien si vous adapterez un modèle de langage et/ou un modèle acoustique. Pour utiliser des modèles de base, l’API requiert uniquement la clé API. En interne, l’API appellera le meilleur modèle pour vos données et s’adaptera.

### <a name="transcribe-call-center-audio-calls-with-custom-models"></a>Transcrire des appels audio de centres d’appel avec des modèles personnalisés

Si vous prévoyez d’utiliser un modèle personnalisé, vous devrez disposer de l’ID de ce modèle, ainsi que de la clé API. L’ID du modèle est obtenu à partir du [portail d’adaptation](https://customspeech.ai). Il ne s’agit pas de l’ID du point de terminaison que vous trouvez dans la vue « Détails des points de terminaison », mais de l’ID du modèle que vous pouvez récupérer en cliquant sur les « Détails » de ce modèle.

## <a name="voice-bots"></a>Robots vocaux

Les développeurs peuvent doter leurs applications d’une sortie vocale. Le Service Speech peut assurer la synthétisation vocale de plusieurs [langages](supported-languages.md) ainsi que fournir les [points de terminaison](rest-apis.md) permettant d’accéder à cette fonctionnalité et de l’ajouter à votre application.

Par ailleurs, pour les utilisateurs qui veulent personnaliser encore plus leurs robots et les rendre uniques, le Service Speech permet aux développeurs de personnaliser une police vocale unique. Comme les modèles de reconnaissance vocale, les polices vocales requièrent les données de l’utilisateur. Les développeurs chargent ces données dans notre [portail d’adaptation vocale](https://customspeech.ai) et commencent à créer une marque vocale unique pour votre robot. Les détails sont décrits [ici](how-to-text-to-speech.md), ainsi qu’aux pages [FAQ](faq-text-to-speech.md). 

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Démarrer avec le kit de développement logiciel (SDK) Speech](speech-sdk.md)
