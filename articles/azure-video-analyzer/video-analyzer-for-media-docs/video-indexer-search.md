---
title: Rechercher des moments précis dans les vidéos avec Azure Video Analyzer for Media (anciennement Video Indexer)
titleSuffix: Azure Video Analyzer for Media
description: Découvrez comment rechercher des moments précis dans les vidéos avec Azure Video Analyzer for Media (anciennement Video Indexer).
services: azure-video-analyzer
author: Juliako
manager: femila
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 11/23/2019
ms.author: juliako
ms.openlocfilehash: 27d25793dfec24a5eac847fae89bc0a01a32e909
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112115302"
---
# <a name="search-for-exact-moments-in-videos-with-video-analyzer-for-media"></a>Rechercher des moments précis dans des vidéos avec Video Analyzer for Media

Cette rubrique vous montre comment utiliser le site web Azure Video Analyzer for Media (anciennement Video Indexer) pour rechercher des moments précis dans les vidéos.

1. Accédez au site web [Video Analyzer for Media](https://www.videoindexer.ai/) et connectez-vous.
1. Spécifiez les mots clés de recherche, et la recherche sera effectuée parmi toutes les vidéos de la bibliothèque de votre compte. 

    Vous pouvez filtrer votre recherche en sélectionnant **Filtres**. Dans l’exemple ci-dessous, nous effectuons une recherche sur « Microsoft » qui s’affiche sous forme de texte à l’écran uniquement (OCR).

    :::image type="content" source="./media/video-indexer-search/filter.png" alt-text="Filtrer, texte uniquement":::
1. Appuyez sur **Search** pour afficher le résultat.

    :::image type="content" source="./media/video-indexer-search/results.png" alt-text="Résultats de recherche de vidéos":::

    Si vous sélectionnez l’un des résultats, le lecteur vous amène à ce moments précis de la vidéo.
1. Affichez et recherchez les insights résumés de la vidéo en cliquant sur **Lecture** dans la vidéo ou en sélectionnant l’un des résultats de la recherche d’origine. 

    Vous pouvez afficher, rechercher et modifier les **insights**. Lorsque vous sélectionnez l’un des insights, le lecteur vous amène à ce moments précis de la vidéo.  

    :::image type="content" source="./media/video-indexer-search/insights.png" alt-text="Afficher, rechercher et modifier les insights de la vidéo":::

    Si vous incorporez la vidéo par le biais de widgets Video Analyzer for Media, vous pouvez obtenir la vue Lecteur/Insights et la synchronisation dans votre application. Pour plus d’informations, consultez [Incorporer des widgets Video Analyzer for Media dans votre application](video-indexer-embed-widgets.md).
1. Vous pouvez afficher, rechercher et modifier les transcriptions en cliquant sur l’onglet **Chronologie**. 

    :::image type="content" source="./media/video-indexer-search/timeline.png" alt-text="Afficher, rechercher et modifier les transcriptions de la vidéo":::

    Pour modifier le texte, sélectionnez **Modifier** en haut à droite et modifiez le texte comme vous le souhaitez. 

    Vous pouvez également traduire et télécharger les transcriptions en sélectionnant l’option appropriée dans l’angle supérieur droit. 

## <a name="embed-download-create-projects"></a>Incorporer, télécharger, créer des projets

Vous pouvez incorporer votre vidéo en sélectionnant **</> Incorporer** sous votre vidéo. Pour plus d’informations, consultez [Incorporer des widgets visuels dans votre application](video-indexer-embed-widgets.md).

Vous pouvez télécharger la vidéo source, les insights de la vidéo et les transcriptions en cliquant sur **Télécharger** sous votre vidéo.

Vous pouvez créer un clip basé sur votre vidéo avec des lignes et des moments spécifiques en cliquant sur **Ouvrir dans l’éditeur**, en modifiant la vidéo, puis en enregistrant le projet. Pour plus d’informations, consultez [Ajouter des clips vidéo à votre projet](use-editor-create-project.md).

:::image type="content" source="./media/video-indexer-search/embed-download-create-projects.png" alt-text="Incorporer, télécharger, créer des projets de la vidéo":::

## <a name="next-steps"></a>Étapes suivantes

[Traiter du contenu avec l’API REST Video Analyzer for Media](video-indexer-use-apis.md)
