---
title: Reconnaissance vocale - Service Speech
titleSuffix: Azure Cognitive Services
description: La fonctionnalité de reconnaissance vocale permet de transcrire en temps réel des flux audio en texte que vos applications, outils ou appareils peuvent utiliser, afficher et manipuler sous forme d’entrée de commande. Ce service fonctionne de façon continue avec les fonctionnalités de synthèse vocale et de traduction vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 49bfa4a0dbf0adc498d545a2908c20f0ffa35b4b
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075730"
---
# <a name="what-is-speech-to-text"></a>Qu’est-ce que la reconnaissance vocale ?

La reconnaissance vocale d’Azure Speech Services, également appelée conversion de parole en texte, permet de transcrire en temps réel des flux audio en texte que vos applications, outils ou appareils peuvent utiliser, afficher et manipuler sous forme d’entrée de commande. Ce service repose sur la même technologie de reconnaissance que celle utilisée par Microsoft pour Cortana et les produits Office et fonctionne de façon continue avec la traduction et la synthèse vocale. Vous trouverez une liste complète des langues compatibles avec la reconnaissance vocale dans [Langues prises en charge](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text).

Par défaut, le service de reconnaissance vocale utilise le modèle de langage universel. Ce modèle a été entraîné avec des données qui sont la propriété de Microsoft et est déployé dans le cloud. Il est particulièrement adapté aux scénarios de conversation et de dictée. Si vous utilisez la reconnaissance vocale pour la reconnaissance et la transcription dans un environnement unique, vous pouvez créer et former des modèles de prononciation, de langue et acoustiques personnalisés pour prendre en compte un bruit ambiant ou le vocabulaire spécifique d’un secteur.

Vous pouvez facilement capturer du contenu audio à partir d’un microphone, lire un flux ou accéder à des fichiers audio dans un espace de stockage avec le kit SDK Speech et les API REST. Le SDK Speech prend en charge le contenu audio monocanal WAV/PCM 16 bits, 16 kHz/8 kHz pour la reconnaissance vocale. D’autres formats audio sont pris en charge en utilisant le [point de terminaison REST de reconnaissance vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) ou le [service de transcription Batch](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

## <a name="core-features"></a>Fonctionnalités de base

Les fonctionnalités disponibles via le SDK Speech et les API REST sont les suivantes :

| Cas d’utilisation | Kit SDK | REST |
|--------- | --- | ---- |
| Transcription d’énoncés courts (< 15 secondes). Prend uniquement en charge le résultat final de la transcription. | OUI | OUI |
| Transcription continue d’énoncés longs et de streaming audio (> 15 secondes). Prend en charge les résultats de transcription intermédiaires et finaux. | OUI | Non |
| Dérivation d’intentions à partir des résultats de la reconnaissance avec [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | OUI | Non\* |
| Transcription par lots (Batch) et asynchrone des fichiers audio. | Non  | Oui\*\* |
| Création et gestion de modèles vocaux. | Non | Oui\*\* |
| Création et gestion de déploiements de modèles personnalisés. | Non  | Oui\*\* |
| Création de tests de précision pour mesurer la précision du modèle de référence par rapport aux modèles personnalisés. | Non  | Oui\*\* |
| Gestion des abonnements. | Non  | Oui\*\* |

\*_Les intentions et entités de LUIS peuvent être dérivées à l’aide d’un abonnement LUIS distinct. Avec cet abonnement, le SDK peut appeler LUIS à votre place et fournir des résultats d’entité et d’intention. L’API REST vous permet d’appeler LUIS vous-même pour dériver des intentions et des entités avec votre abonnement LUIS._

\*\*_Ces services sont disponibles avec le point de terminaison cris.ai. Voir [Informations de référence sur Swagger](https://westus.cris.ai/swagger/ui/index)._

## <a name="get-started-with-speech-to-text"></a>Commencer avec la reconnaissance vocale

Nous proposons des démarrages rapides pour la plupart des langages de programmation populaires, conçus pour que votre code soit opérationnel en moins de 10 minutes. [Ce tableau](https://aka.ms/csspeech#5-minute-quickstarts) comprend la liste complète des guides de démarrage rapide pour le SDK Speech organisés par plateforme et langage. Vous trouverez également des informations de référence sur l’API [ici](https://aka.ms/csspeech#reference).

Si vous préférez utiliser le service REST de reconnaissance vocale, consultez [API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Tutoriels et exemple de code

Une fois que vous avez eu l’occasion d’utiliser les Services Speech, essayez notre didacticiel qui vous apprend à reconnaître les intentions du discours à l’aide du Kit de développement logiciel (SDK) Speech et de LUIS.

- [Tutoriel : Effectuer une reconnaissance des intentions du discours à l’aide du Kit de développement logiciel (SDK) Speech et de LUIS, C#](how-to-recognize-intents-from-speech-csharp.md)

Un exemple de code pour le SDK Speech est disponible sur GitHub. Ces exemples couvrent des scénarios courants tels que la lecture du signal audio d’un fichier ou d’un flux, la reconnaissance continue et ponctuelle, et l’utilisation de modèles personnalisés.

- [Exemples de reconnaissance vocale (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Exemples de transcription par lot (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Personnalisation

En plus du modèle de base standard utilisé par les services vocaux, vous pouvez personnaliser des modèles en fonction de vos besoins avec des données disponibles afin de surmonter certains obstacles à la reconnaissance vocale, tels que le style d’élocution, le vocabulaire et le bruit de fond (voir [Custom Speech](how-to-custom-speech.md)).

> [!NOTE]
> Les options de personnalisation varient selon la langue/paramètres régionaux (voir [Langues prises en charge](supported-languages.md)).

## <a name="migration-guides"></a>Guides de migration

> [!WARNING]
> La reconnaissance vocale Bing a été mise hors service le 15 octobre 2019.

Si vos applications, outils ou produits utilisent les API Reconnaissance vocale Bing ou Custom Speech, nous avons créé des guides pour vous aider à migrer vers Speech Services.

- [Migrer de la reconnaissance vocale Bing vers Speech Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
- [Migrer de Custom Speech vers Speech Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Documents de référence

- [Kit de développement logiciel (SDK) de reconnaissance vocale](https://aka.ms/csspeech)
- [SDK Speech Devices](speech-devices-sdk.md)
- [API REST : Reconnaissance vocale](rest-speech-to-text.md)
- [API REST : Synthèse vocale](rest-text-to-speech.md)
- [API REST : Transcription et personnalisation par lot](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir une clé d’abonnement aux services Speech gratuitement](get-started.md)
- [Obtenir le kit SDK Speech](speech-sdk.md)
