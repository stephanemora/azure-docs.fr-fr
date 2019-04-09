---
title: Synthèse vocale avec Speech Azure Services
titleSuffix: Azure Cognitive Services
description: Synthèse vocale à partir des Services de reconnaissance vocale Azure est un service basé sur REST qui permet à vos applications, outils ou les appareils convertir du texte dans la synthèse vocale quasi humaine naturelle. Choisissez parmi les voix standards et neurones ou créer votre propre voix personnalisée propres à votre produit ou une marque. + de 75 voix standard est disponibles dans plus de 45 langues et paramètres régionaux et voix neuronal 5 est disponibles dans les paramètres régionaux et langues 4.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 52f74bb3cb9e460fc5c572079355f47b4b0bf0a3
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59010445"
---
# <a name="what-is-text-to-speech"></a>Quelle est la synthèse vocale ?

Synthèse vocale à partir des Services de reconnaissance vocale Azure est un service basé sur REST qui permet à vos applications, outils ou les appareils convertir du texte dans la synthèse vocale quasi humaine naturelle. Choisissez parmi les voix standards et neurones ou créer votre propre voix personnalisée propres à votre produit ou une marque. + de 75 voix standard est disponibles dans plus de 45 langues et paramètres régionaux et voix neuronal 5 est disponibles dans les paramètres régionaux et langues 4. Pour obtenir la liste complète, consultez [langues prises en charge](language-support.md#text-to-speech).

Technologie vocale permet aux créateurs de contenu interagir avec leurs utilisateurs de différentes façons. Synthèse vocale peut améliorer l’accessibilité en fournissant aux utilisateurs avec une option permettant d’interagir avec le contenu de façon audible. Si l’utilisateur a une déficience visuelle, un handicap learning, ou nécessite des informations de navigation tout en conduisant, synthèse vocale peut améliorer une expérience existante. Synthèse vocale est également un module complémentaire de précieux pour les robots de voix et assistants virtuels.

### <a name="neural-voices"></a>Voix neurales

Les voix neuronales peuvent être utilisées pour rendre les interactions avec les chatbots et les assistants virtuels plus naturelles et agréables, convertir des textes numériques comme les livres électroniques en livres audio et améliorer les systèmes de navigation embarqués. Prosodie naturelle quasi humaine et claire articulation de mots, voix neuronal réduire considérablement fatigue écoute lorsque vous interagissez avec les systèmes d’intelligence artificielle. Pour plus d’informations sur les voix neuronales, consultez [langues prises en charge](language-support.md#text-to-speech).

### <a name="custom-voices"></a>Voix personnalisées

Personnalisation de la voix vous permet de créer une voix unique de genre reconnaissable pour votre marque. Pour créer votre police vocaux personnalisés, vous effectuer un enregistrement de studio et chargez les scripts associés en tant que les données d’apprentissage. Le service crée ensuite un modèle vocal unique adapté à votre enregistrement. Vous pouvez utiliser cette police vocaux personnalisés pour la synthèse vocale. Pour plus d’informations, consultez [voix personnalisées](how-to-customize-voice-font.md).

## <a name="core-features"></a>Fonctionnalités de base

Ce tableau répertorie les principales fonctionnalités pour la synthèse vocale :

| Cas d’utilisation | Kit SDK  | REST |
|----------|-----|------|
| Convertir le texte par synthèse vocale. | Oui | Oui |
| Télécharger des jeux de données pour l’adaptation de la voix. | Non  | Oui\* |
| Créer et gérer des modèles de polices de voix. | Non  | Oui\* |
| Créer et gérer les déploiements de police de voix. | Non  | Oui\* |
| Créer et gérer des tests de police de voix. | Non  | Oui\* |
| Gérer les abonnements. | Non  | Oui\* |

\* *Ces services sont disponibles à l’aide du point de terminaison cris.ai. Consultez [Swagger référence](https://westus.cris.ai/swagger/ui/index).*

> [!NOTE]
> Le point de terminaison de synthèse vocale implémente la limitation qui limite les requêtes à 25 par 5 secondes. Lorsque la limitation se produit, vous serez averti par les en-têtes de message.

## <a name="get-started-with-text-to-speech"></a>Bien démarrer avec le texte par synthèse vocale

Nous proposons des guides de démarrage rapide conçu pour que vous exécutez le code en moins de 10 minutes. Cette table comprend une liste de synthèse vocale Démarrages rapides organisés par langage.

| Guide de démarrage rapide (REST) | Plateforme | Informations de référence sur l'API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Parcourir](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Window, macOS, Linux | [Parcourir](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Window, macOS, Linux | [Parcourir](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

| Guide de démarrage rapide (SDK) | Plateforme | Informations de référence sur l'API |
|------------|----------|---------------|
| [C#, .NET framework](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Parcourir](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Parcourir](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Parcourir](https://aka.ms/csspeech/cppref) |

## <a name="sample-code"></a>Exemple de code

Exemple de code pour la synthèse vocale est disponible sur GitHub. Ces exemples couvrent la conversion du texte dans des langages de programmation populaires.

* [Exemples de synthèse vocale (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
* [Exemples de synthèse vocale (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="reference-docs"></a>Documents de référence

* [Kit de développement logiciel (SDK) de reconnaissance vocale](speech-sdk-reference.md)
* [Kit de développement logiciel (SDK) Speech Devices](speech-devices-sdk.md)
* [API REST : Reconnaissance vocale](rest-speech-to-text.md)
* [API REST : Synthèse vocale](rest-text-to-speech.md)
* [API REST : Personnalisation et la transcription de lot](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir un abonnement de Services de reconnaissance vocale gratuit](get-started.md)
* [Créer des polices de la voix personnalisée](how-to-customize-voice-font.md)
