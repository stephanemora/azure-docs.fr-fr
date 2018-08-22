---
title: Concepts de Azure Video Indexer | Microsoft Docs
description: Cette rubrique décrit certains concepts du service Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 224c8b05027f51fb99c8d58be34c3604032c0f77
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399493"
---
# <a name="video-indexer-concepts"></a>Concepts de Video Indexer
 
Cette rubrique décrit certains concepts du service Video Indexer.
    
## <a name="summarized-insights"></a>Insights résumés

Les insights résumés contiennent une vue agrégée des données : visages, mots clés, sentiments. Par exemple, au lieu de passer au travers des milliers d’intervalles de temps et de vérifier les visages qu’ils contiennent, les insights résumés contiennent tous les visages, ainsi que les intervalles de temps au cours desquels ils apparaissent et le pourcentage du temps pendant lequel ils sont affichés.

## <a name="topicskeywords"></a>Rubriques/mots clés

Les rubriques/mots clés sont dans la liste des expressions clés extraites du texte par Video Indexer. Par exemple, une vidéo de Scott Guthrie peut contenir les rubriques/mots clés suivants : sécurité, Azure, Microsoft Cloud, chiffre d’affaires.

## <a name="sentiments"></a>Sentiments

Quand Video Indexer analyse les transcriptions, il détecte également les sentiments. Par exemple, « c’est un événement passionnant » est un sentiment positif.

## <a name="time-range-vs-adjusted-time-range"></a>Intervalle de temps et intervalle de temps ajusté

TimeRange est l’intervalle de temps dans la vidéo d’origine. AdjustedTimeRange est l’intervalle de temps par rapport à la playlist actuelle. Dans la mesure où vous pouvez créer une playlist à partir de différentes lignes de plusieurs vidéos, vous pouvez prendre une vidéo d’une heure et en tirer une seule ligne, par exemple, 10:00-10:15. Dans ce cas, vous aurez une playlist avec une ligne, où l’intervalle de temps est 10:00-10:15, mais le paramètre adjustedTimeRange sera 00:00-00:15.
 
## <a name="blocks"></a>Blocs

Les blocs sont là pour simplifier le parcours des données. Par exemple, un bloc peut être défini par un changement d’intervenant ou une pause prolongée.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la prise en main, consultez [Comment s’inscrire et charger votre première vidéo](video-indexer-get-started.md).

## <a name="see-also"></a>Voir aussi

[Présentation de Video Indexer](video-indexer-overview.md)
