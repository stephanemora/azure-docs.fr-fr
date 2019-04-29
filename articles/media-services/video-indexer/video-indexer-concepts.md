---
title: Concepts de Video Indexer
titlesuffix: Azure Media Services
description: Cette rubrique décrit certains concepts du service Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: c7bbe8c6b2ad51ed7272cd215552807c7cea3aee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60559821"
---
# <a name="video-indexer-concepts"></a>Concepts de Video Indexer
 
Cet article décrit certains concepts du service Video Indexer.
    
## <a name="summarized-insights"></a>Insights résumés

Les insights résumés contiennent une vue agrégée des données : visages, rubriques, émotions. Par exemple, au lieu de passer au travers des milliers d’intervalles de temps et de vérifier les visages qu’ils contiennent, les insights résumés contiennent tous les visages, ainsi que les intervalles de temps au cours desquels ils apparaissent et le pourcentage du temps pendant lequel ils sont affichés.

## <a name="time-range-vs-adjusted-time-range"></a>Intervalle de temps et intervalle de temps ajusté

TimeRange est l’intervalle de temps dans la vidéo d’origine. AdjustedTimeRange est l’intervalle de temps par rapport à la playlist actuelle. Dans la mesure où vous pouvez créer une playlist à partir de différentes lignes de plusieurs vidéos, vous pouvez prendre une vidéo d’une heure et en tirer une seule ligne, par exemple, 10:00-10:15. Dans ce cas, vous aurez une playlist avec une ligne, où l’intervalle de temps est 10:00-10:15, mais le paramètre adjustedTimeRange sera 00:00-00:15.
 
## <a name="blocks"></a>Blocs

Les blocs sont là pour simplifier le parcours des données. Par exemple, un bloc peut être défini par un changement d’intervenant ou une pause prolongée.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la prise en main, consultez [Comment s’inscrire et charger votre première vidéo](video-indexer-get-started.md).

## <a name="see-also"></a>Voir aussi

[Présentation de Video Indexer](video-indexer-overview.md)
