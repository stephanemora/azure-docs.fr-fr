---
title: À propos de la synthèse vocale | Microsoft Docs
description: Vue d’ensemble des fonctionnalités de synthèse vocale.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: e3543c2056934372a2795bd50f340432ceec3b6d
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257944"
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

Le service de **synthèse vocale** Microsoft propose plus de 75 voix dans plus de 45 langues et paramètres régionaux. Pour utiliser ces « polices de la voix » standard, vous devez uniquement spécifier le nom de la voix ainsi que quelques autres paramètres lorsque vous appelez l’API REST du service. Pour plus d’informations sur les voix prises en charge, voir [Langues prises en charge](https://docs.microsoft.com/azure/cognitive-services/speech-service/supported-languages#text-to-speech). 

Si vous voulez une voix unique pour votre application, vous pouvez créer des [polices de voix personnalisées](how-to-customize-voice-font.md) à partir de vos propres échantillons vocaux.

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Découvrir comment utiliser la reconnaissance vocale en C#](quickstart-csharp-dotnet-windows.md)
