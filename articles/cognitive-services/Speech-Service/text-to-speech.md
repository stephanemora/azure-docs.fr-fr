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
ms.openlocfilehash: 1edc2587ef8680f60082bf6271b73d30184f331b
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521242"
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
| Convertir le texte par synthèse vocale. | Non  | Oui |
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

| Démarrage rapide | Plateforme | Informations de référence sur l'API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.JS](quickstart-nodejs-text-to-speech.md) | Window, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Window, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>Exemple de code

Exemple de code pour la synthèse vocale est disponible sur GitHub. Ces exemples couvrent la conversion du texte dans des langages de programmation populaires.

* [Exemples de synthèse vocale (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Documents de référence

* [Kit de développement logiciel (SDK) de reconnaissance vocale](speech-sdk-reference.md)
* [SDK Speech Devices](speech-devices-sdk.md)
* [API REST : Reconnaissance vocale](rest-speech-to-text.md)
* [API REST : Synthèse vocale](rest-text-to-speech.md)
* [API REST : Transcription et personnalisation par lot](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir un abonnement gratuit aux services Speech](get-started.md)
* [Créer des polices de la voix personnalisées](how-to-customize-voice-font.md)
