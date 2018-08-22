---
title: Utiliser Azure Video Indexer pour rechercher des instants précis dans les vidéos | Microsoft Docs
description: Cette rubrique montre comment rechercher des instants précis dans les vidéos.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 1cffa067d8028adab4dbcc82c529f77d980ce6be
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397541"
---
# <a name="find-exact-moments-within-videos"></a>Rechercher des instants précis dans les vidéos

Cette rubrique vous montre les options de recherche vous permettant de rechercher des instants précis dans les vidéos.

1. Connectez-vous à votre compte [Video Indexer](https://api-portal.videoindexer.ai/).
2. Rechercher parmi toutes les vidéos de votre compte.

    Dans l’exemple suivant, nous avons effectué une recherche parmi toutes les vidéos créées par Channel 9 avec Scott Hanselman.

    ![Recherche](./media/video-indexer-search/video-indexer-search01.png)
    
3. Rechercher les insights résumés de la vidéo.

    Vous pouvez ensuite effectuer une recherche dans une vidéo en cliquant sur **Lire** sur la vidéo. Ensuite, vous pouvez faire une recherche dans la vidéo en sélectionnant l’onglet **Recherche**. Par exemple, nous avons recherché tous les endroits où le texte « protection de l’identité » est utilisé. 

    ![Recherche](./media/video-indexer-search/video-indexer-search02.png)

    Si vous cliquez sur l’un des résultats, le lecteur vous amène à ce moment de la vidéo. Vous pouvez obtenir la vue et la synchronisation du lecteur/des insights dans votre application. Pour plus d’informations, consultez [Incorporer des widgets Video Indexer dans votre application](video-indexer-embed-widgets.md). 

    
4. Recherchez la répartition détaillée de la vidéo.

    Si vous souhaitez créer votre propre répartition à partir de la vidéo que vous avez trouvée, appuyez sur le bouton **Modifier**. Cette page affiche la répartition complète d’une vidéo. Vous pouvez faire une recherche au sein de la répartition afin d’afficher uniquement les lignes qui vous intéressent. Pour plus d’informations, voir [Afficher et modifier les insights de Video Indexer](video-indexer-view-edit.md).

    Dans cet exemple, nous avons recherché le texte « protection de l’identité ». Nous avons également appliqué des filtres supplémentaires, comme indiqué dans l’écran ci-dessous.

    ![Recherche](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>Étapes suivantes 

Une fois que vous trouvez la vidéo que vous souhaitez utiliser, vous pouvez continuer de la traiter, comme décrit dans l’une des rubriques suivantes : 

- [Créer de nouveaux insights vidéo basés sur une vidéo existante](video-indexer-create-new.md)
- [Traiter du contenu avec l’API REST de Video Indexer](video-indexer-use-apis.md)
- [Incorporer des widgets visuels dans votre application](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Voir aussi

[Présentation de Video Indexer](video-indexer-overview.md)
