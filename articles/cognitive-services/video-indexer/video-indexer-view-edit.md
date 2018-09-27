---
title: Afficher et modifier les insights Video Indexer
titlesuffix: Azure Cognitive Services
description: Cette rubrique montre comment afficher et modifier les insights Video Indexer.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: c9b229e2fb3297d724ec8de02bf54e9765689ab7
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984536"
---
# <a name="view-and-edit-video-indexer-insights"></a>Afficher et modifier les insights Video Indexer

Cette rubrique vous montre comment afficher et modifier les insights Video Indexer pour une vidéo.

1. Accédez au site web [Video Indexer](https://www.videoindexer.ai/) et connectez-vous.
2. Rechercher une vidéo à partir de laquelle vous souhaitez créer vos insights Video Indexer. Pour plus d’informations, consultez [Rechercher des instants précis dans les vidéos](video-indexer-search.md).
3. Appuyez sur **Lecture**.

    La page affiche les insights résumés de la vidéo. 

    ![Insights](./media/video-indexer-view-edit/video-indexer-summarized-insights.png)

4. Affichez les insights résumés de la vidéo. 

    Les insights résumés affichent une vue agrégée des données : visages, mots clés, sentiments. Par exemple, vous pouvez voir les visages des personnes et les plages de temps au cours desquelles chaque visage apparaît, ainsi que le pourcentage de temps pendant lequel ils apparaissent.

    Le lecteur et les insights sont synchronisés. Par exemple, si vous cliquez sur un mot clé ou la ligne de transcription, le lecteur vous amène au moment précis de la vidéo. Vous pouvez obtenir la vue et la synchronisation du lecteur/des insights dans votre application. Pour plus d’informations, consultez [Incorporer des widgets Azure Video Indexer dans votre application](video-indexer-embed-widgets.md). 

3. Modifiez les insights Video Indexer.

    Appuyez sur Modifier sous la vidéo. La page qui apparait montre la répartition complète d’une vidéo. La répartition est divisée en blocs. Les blocs sont là pour simplifier le parcours des données. Par exemple, un bloc peut être défini par un changement d’intervenant ou une pause prolongée. Vous pouvez créer votre propre playlist contenant uniquement les lignes souhaitées. Pour afficher uniquement certaines parties de la vidéo source, vous pouvez filtrer par rubriques/mots clés, sentiments, personnes, intervenants. Vous pouvez choisir d’afficher uniquement la transcription ou la reconnaissance optique des caractères (OCR) de la vidéo.  

    ![Insights](./media/video-indexer-view-edit/video-indexer-create-new-playlist.png)

## <a name="next-steps"></a>Étapes suivantes

[Découvrez comment créer vos propres insights Video Indexer à partir de certaines autres vidéos](video-indexer-create-new.md).

## <a name="see-also"></a>Voir aussi

[Présentation de Video Indexer](video-indexer-overview.md)

