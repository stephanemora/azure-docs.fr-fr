---
title: À propos de la fonctionnalité Synthèse vocale du service Speech
titleSuffix: Azure Cognitive Services
description: L’API Synthèse vocale propose plus de 75 voix dans plus de 45 langues et paramètres régionaux. Pour utiliser les polices de la voix standard, vous devez uniquement spécifier le nom de la voix ainsi que quelques autres paramètres lorsque vous appelez le service Speech.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: erhopf
ms.openlocfilehash: 7f01fe5c71cdd6f4c70527fcf2553374aae9a5d8
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469916"
---
# <a name="about-the-text-to-speech-api"></a>À propos de l’API Synthèse vocale

L’API **Synthèse vocale** (TTS) du service Speech convertit un texte d’entrée en discours naturel.

Pour générer la parole, votre application envoie des requêtes HTTP POST au service Speech. Ensuite, le texte est synthétisé en parole à consonance humaine, et renvoyé sous la forme d’un fichier audio. Diverses voix et langues sont prises en charge.

Les scénarios dans lesquels la synthèse vocale est adoptée sont les suivants :

* *Amélioration de l’accessibilité :* la technologie de **synthèse vocale** permet aux propriétaires et éditeurs de contenu de répondre aux différentes façons dont les personnes interagissent avec leur contenu. Personnes atteintes de troubles visuels ou de difficultés de lecture bénéficient d’un contenu audible. La sortie vocale permet également de profiter d’un contenu textuel, par exemple de journaux ou de blogs, sur des appareils mobiles lors de déplacements ou de la pratique d’activités physiques.

* *Réponse à des scénarios multitâche :* la **synthèse vocale** permet aux utilisateurs d’assimiler facilement et rapidement des informations importantes tandis qu’ils conduisent, ou quand ils ne se trouvent pas dans un environnement approprié pour la lecture. La navigation est une application courante dans ce domaine.

* *Amélioration de l’apprentissage avec plusieurs modes :* chacun apprend à sa façon. Des experts de l’apprentissage en ligne ont montré que le fait de fournir simultanément le texte parlé et écrit peut aider à apprendre et à retenir les informations.

* *Production de robots ou assistants intuitifs :* la faculté de parole peut faire partie intégrante d’un agent conversationnel ou d’un assistant virtuel intelligents. De plus en plus d’entreprises développent des agents conversationnels afin d’offrir des expériences de service client engageantes à leurs clients. La voix ajoute une dimension en permettant au robot de donner des réponses audibles (par exemple, au téléphone).

## <a name="voice-support"></a>Prise en charge de la voix

Le service de **synthèse vocale** Microsoft propose plus de 75 voix dans plus de 45 langues et paramètres régionaux. Pour utiliser ces « polices de la voix » standard, vous devez uniquement spécifier le nom de la voix ainsi que quelques autres paramètres lorsque vous appelez l’API REST du service. Pour plus d’informations sur les voix prises en charge, voir [Langues prises en charge](language-support.md#text-to-speech).

Si vous voulez une voix unique pour votre application, vous pouvez créer des [polices de voix personnalisées](how-to-customize-voice-font.md) à partir de vos propres échantillons vocaux.

## <a name="api-capabilities"></a>Fonctionnalités de l’API

Une grande partie des fonctionnalités de l’API **Synthèse vocale**, notamment concernant la personnalisation, sont disponibles par le biais de REST. Le tableau suivant récapitule les fonctionnalités de chaque méthode d’accès à l’API. Pour obtenir la liste complète des fonctionnalités et des détails de l’API, consultez [Informations de référence sur Swagger](https://westus.cris.ai/swagger/ui/index).

| Cas d’utilisation | REST | Kits de développement logiciel (SDK) |
|-----|-----|-----|----|
| Charger des jeux de données pour l’adaptation de la voix | Oui | Non  |
| Créer et gérer des modèles de police de la voix | Oui | Non  |
| Créer et gérer des déploiements de police de la voix | Oui | Non  |
| Créer et gérer des tests de police de la voix| Oui | Non  |
| Gérer les abonnements | Oui | Non  |

> [!NOTE]
> L’API implémente une limitation des requêtes d’API à 25 toutes les 5 secondes. Les en-têtes de messages vous informeront des limites.

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Découvrir comment synthétiser une entrée vocale via l’API REST](how-to-text-to-speech.md)
