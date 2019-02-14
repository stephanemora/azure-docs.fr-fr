---
title: À propos de la synthèse vocale - Service Speech
titleSuffix: Azure Cognitive Services
description: L’API Synthèse vocale propose plus de 75 voix dans plus de 45 langues et paramètres régionaux. Pour utiliser les polices de la voix standard, vous devez uniquement spécifier le nom de la voix ainsi que quelques autres paramètres lorsque vous appelez le service Speech.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 0836ae4a9041db27cfed35dd0f1fc0df6e541aff
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859332"
---
# <a name="about-the-text-to-speech-api"></a>À propos de l’API Synthèse vocale

L’API **Synthèse vocale** (TTS) convertit un texte d’entrée en discours naturel (aussi appelé *synthèse de la parole*).

Pour générer la parole, votre application envoie des requêtes HTTP POST à l’API Synthèse vocale. Ensuite, le texte est synthétisé en parole à consonance humaine, et renvoyé sous la forme d’un fichier audio. Diverses voix et langues sont prises en charge.

Les scénarios dans lesquels la synthèse vocale est adoptée sont les suivants :

* *Amélioration de l’accessibilité :* la technologie de **synthèse vocale** permet aux propriétaires et éditeurs de contenu de répondre aux différents modes d’interaction entre les personnes et leur contenu. Personnes atteintes de troubles visuels ou de difficultés de lecture bénéficient d’un contenu audible. La sortie vocale permet également de profiter d’un contenu textuel, par exemple de journaux ou de blogs, sur des appareils mobiles lors de déplacements ou de la pratique d’activités physiques.

* *Réponse à des scénarios multitâches :* la **synthèse vocale** permet aux utilisateurs d’assimiler facilement et rapidement des informations importantes pendant qu’ils conduisent, ou quand ils ne se trouvent pas dans un environnement approprié pour la lecture. La navigation est une application courante dans ce domaine.

* *Amélioration de l’apprentissage avec plusieurs modes :* Toutes les personnes n’apprennent pas de la même façon. Des experts de l’apprentissage en ligne ont montré que le fait de fournir simultanément le texte parlé et écrit peut aider à apprendre et à retenir les informations.

* *Mise à disposition de bots ou d’assistants intuitifs :* l’aptitude à parler peut faire partie intégrante d’un chatbot intelligent ou d’un assistant virtuel. De plus en plus d’entreprises développent des agents conversationnels afin d’offrir des expériences de service client engageantes à leurs clients. La voix ajoute une dimension en permettant au robot de donner des réponses audibles (par exemple, au téléphone).

## <a name="voice-support"></a>Prise en charge de la voix

Le service de **synthèse vocale** Microsoft propose plus de 75 voix dans plus de 45 langues et paramètres régionaux. Pour utiliser ces « polices de la voix » standard, vous devez uniquement spécifier le nom de la voix ainsi que quelques autres paramètres lorsque vous appelez l’API REST du service. Pour plus d’informations sur les langues, les paramètres régionaux et les voix pris en charge, consultez [langues prises en charge](language-support.md#text-to-speech).

> [!IMPORTANT]
> Les prix varient en fonction des voix standard, personnalisées et neurales. Pour plus d’informations, voir la [tarification](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="neural-voices"></a>Voix neurales

La synthèse vocale neuronale peut être utilisée pour rendre les interactions avec les chatbots et les assistants virtuels plus naturelles et agréables, convertir des textes numériques comme les livres électroniques en livres audio et améliorer les systèmes de navigation embarqués. Grâce à la prosodie naturelle quasi humaine et à la bonne articulation des mots, la synthèse vocale neuronale réduit considérablement la fatigue d’écoute quand il s’agit d’interagir avec les systèmes d’intelligence artificielle. Pour plus d’informations sur les voix neuronales, consultez [langues prises en charge](language-support.md#text-to-speech).

### <a name="custom-voices"></a>Voix personnalisées

La personnalisation de la voix de synthèse vocale vous permet de créer une voix reconnaissable et propre à votre marque. C’est ce que l’on appelle une *police de la voix*. Pour créer votre police de la voix, effectuez un enregistrement en studio et chargez les scripts associés en tant que données d’apprentissage. Le service crée ensuite un modèle vocal unique adapté à votre enregistrement. Vous pouvez alors utiliser cette police de la voix pour synthétiser la parole. Pour plus d'informations, consultez [polices de voix personnalisée](how-to-customize-voice-font.md).

## <a name="api-capabilities"></a>Fonctionnalités de l’API

Une grande partie des fonctionnalités de l’API **Synthèse vocale**, notamment celles qui ont trait à la personnalisation, sont disponibles par le biais de REST. Le tableau suivant récapitule les fonctionnalités de chaque méthode d’accès à l’API. Pour obtenir la liste complète des fonctionnalités et des détails de l’API, consultez [Informations de référence sur Swagger](https://westus.cris.ai/swagger/ui/index).

| Cas d’utilisation | REST | Kits SDK |
|-----|-----|-----|----|
| Charger des jeux de données pour l’adaptation de la voix | OUI | Non  |
| Créer et gérer des modèles de police de la voix | OUI | Non  |
| Créer et gérer des déploiements de police de la voix | OUI | Non  |
| Créer et gérer des tests de police de la voix| OUI | Non  |
| Gérer les abonnements | OUI | Non  |

> [!NOTE]
> L’API implémente une limitation des requêtes d’API à 25 toutes les 5 secondes. Les en-têtes de messages vous informeront des limites.

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir un abonnement gratuit aux services Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Démarrage rapide : Convertir du texte par synthèse vocale, Python](quickstart-python-text-to-speech.md)
* [Démarrage rapide : Convertir du texte par synthèse vocale, .NET Core](quickstart-dotnet-text-to-speech.md)
* [Référence d’API REST](rest-apis.md)
