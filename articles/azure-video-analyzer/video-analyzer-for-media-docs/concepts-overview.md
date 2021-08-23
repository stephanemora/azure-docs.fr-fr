---
title: Concepts d’Azure Video Analyzer for Media (anciennement Video Indexer) – Azure
titleSuffix: Azure Video Analyzer for Media (formerly Video Indexer)
description: Cet article présente brièvement la terminologie et les concepts d’Azure Video Analyzer for Media (anciennement Video Indexer).
services: azure-video-analyzer
author: Juliako
manager: femila
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 01/19/2021
ms.author: juliako
ms.openlocfilehash: 9b7f6729e437a35001e5fa59d977c3c60321ef3a
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112123354"
---
# <a name="video-analyzer-for-media-concepts"></a>Concepts de Video Analyzer for Media

Cet article présente brièvement la terminologie et les concepts d’Azure Video Analyzer for Media (anciennement Video Indexer).

## <a name="audiovideocombined-insights"></a>Insights audio/vidéo/combinés

Quand vous chargez vos vidéos sur Video Analyzer for Media, ce service analyse les éléments audio-visuels en exécutant différents modèles d’IA. À mesure que Video Analyzer for Media analyse votre vidéo, les insights sont extraits par les modèles IA. Pour plus d’informations, consultez [Vue d’ensemble](video-indexer-overview.md).

## <a name="confidence-scores"></a>Scores de confiance 

Le score de confiance indique la confiance dans un insight. Il s’agit d’un nombre compris entre 0 et 1. Plus le score et élevé,  plus la réponse est fiable. Par exemple, 

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
```

## <a name="content-moderation"></a>Modération du contenu

Utilisez des modèles de modération de contenu textuel et visuel afin de protéger vos utilisateurs contre le contenu inapproprié et de vous assurer que le contenu que vous publiez correspond aux valeurs de votre organisation. Vous pouvez bloquer automatiquement certaines vidéos ou alerter vos utilisateurs au sujet du contenu. Pour plus d’informations, consultez [Insights : modération du contenu visuel et textuel](video-indexer-output-json-v2.md#visualcontentmoderation). 

## <a name="blocks"></a>Blocs   

Les blocs sont là pour simplifier le parcours des données. Par exemple, un bloc peut être défini par un changement d’intervenant ou une pause prolongée.  

## <a name="project-and-editor"></a>Projet et éditeur

Le site web [Video Analyzer for Media](https://www.videoindexer.ai/) vous permet d’utiliser des insights détaillés sur votre vidéo pour trouver le contenu multimédia adapté, rechercher les passages qui vous intéressent et utiliser les résultats dans le but de créer un tout nouveau projet. Une fois créé, le projet peut être restitué et téléchargé depuis Video Analyzer for Media, puis utilisé dans vos propres applications d’édition ou dans vos flux de travail en aval.

Voici quelques scénarios pour lesquels l’utilisation de cette fonctionnalité peut être pratique : 

* Création de moments forts du film pour les bandes-annonces.
* Utilisation d’anciens clips vidéos dans des bulletins d’informations.
* Création de contenus plus courts pour les réseaux sociaux.

Pour plus d’informations, consultez [Création de projets avec l’éditeur](use-editor-create-project.md).

## <a name="keyframes"></a>Images clés

Video Analyzer for Media sélectionne la ou les image(s) qui représentent le mieux chaque capture. Les images clés sont des images représentatives sélectionnées à partir de la vidéo complète, en fonction des propriétés esthétiques (par exemple, contraste et stabilité). Pour plus d’informations, consultez [Scènes, plans et images clés](scenes-shots-keyframes.md).

## <a name="time-range-vs-adjusted-time-range"></a>Intervalle de temps et intervalle de temps ajusté   

TimeRange est l’intervalle de temps dans la vidéo d’origine. AdjustedTimeRange est l’intervalle de temps par rapport à la playlist actuelle. Dans la mesure où vous pouvez créer une playlist à partir de différentes lignes de plusieurs vidéos, vous pouvez prendre une vidéo d’une heure et en tirer une seule ligne, par exemple, 10:00-10:15. Dans ce cas, vous aurez une playlist avec une ligne, où l’intervalle de temps est 10:00-10:15, mais le paramètre adjustedTimeRange sera 00:00-00:15. 

## <a name="widgets"></a>Widgets

Video Analyzer for Media prend en charge l’incorporation de widgets dans vos applications. Pour plus d’informations, consultez [Incorporer des widgets Video Analyzer for Media dans vos applications](video-indexer-embed-widgets.md).

## <a name="summarized-insights"></a>Insights résumés  

Les insights résumés contiennent une vue agrégée des données : visages, rubriques, émotions. Par exemple, au lieu de passer au travers des milliers d’intervalles de temps et de vérifier les visages qu’ils contiennent, les insights résumés contiennent tous les visages, ainsi que les intervalles de temps au cours desquels ils apparaissent et le pourcentage du temps pendant lequel ils sont affichés.  

## <a name="next-steps"></a>Étapes suivantes

- [vue d’ensemble](video-indexer-overview.md)
- [Insights](video-indexer-output-json-v2.md)
