---
title: Rechercher des instants précis dans les vidéos - Video Indexer
titlesuffix: Azure Media Services
description: Cette rubrique montre comment rechercher des instants précis dans les vidéos à l’aide de Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 93376059dac2a8336e581269d8289d893779b291
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799092"
---
# <a name="find-exact-moments-within-videos"></a>Rechercher des instants précis dans les vidéos

Cette rubrique vous montre les options de recherche vous permettant de rechercher des instants précis dans les vidéos.

1. Accédez au site web [Video Indexer](https://www.videoindexer.ai/) et connectez-vous.
2. Rechercher parmi toutes les vidéos de votre compte.

    Dans l’exemple suivant, nous recherchons toutes les vidéos portant sur la sécurité et dans lesquelles Satya apparaît,

    ![Recherche](./media/video-indexer-search/video-indexer-search01.png)
3. Rechercher les insights résumés de la vidéo.

    Vous pouvez ensuite effectuer une recherche dans une vidéo en cliquant sur **Lire** sur la vidéo. Ensuite, vous pouvez faire une recherche dans la vidéo en sélectionnant l’onglet **Recherche**. 

    Dans l’exemple suivant, nous recherchons le terme « sécurisé » dans la vidéo sélectionnée.

    ![Recherche](./media/video-indexer-search/video-indexer-search02.png)

    Si vous cliquez sur l’un des résultats, le lecteur vous amène à ce moment de la vidéo. Vous pouvez obtenir la vue et la synchronisation du lecteur/des insights dans votre application. Pour plus d’informations, consultez [Incorporer des widgets Video Indexer dans votre application](video-indexer-embed-widgets.md). 
4. Recherchez la répartition détaillée de la vidéo.
    
    Si vous souhaitez créer votre propre clip à partir de la vidéo que vous avez trouvée, appuyez sur le bouton **Modifier**. Cette page montre vidéo, ainsi que ses insights sous forme de filtres. Pour plus d’informations, voir [Afficher et modifier les insights de Video Indexer](video-indexer-view-edit.md). 

    Dans la vidéo, vous pouvez effectuer une recherche afin de n’afficher que les lignes qui vous intéressent et utiliser les informations sur le côté pour filtrer les parties que vous souhaitez afficher. Lorsque vous avez terminé, vous pouvez afficher un aperçu de votre clip et appuyer sur **Publier** pour créer le nouveau clip qui apparaît dans votre galerie.
    
    Dans l’exemple suivant, nous avons recherché pour le texte « réalité mixte ». Nous avons également appliqué des filtres supplémentaires, comme indiqué dans l’écran ci-dessous.
    
    ![Recherche](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>Étapes suivantes 

Une fois que vous trouvez la vidéo que vous souhaitez utiliser, vous pouvez continuer de la traiter, comme décrit dans l’une des rubriques suivantes : 

- [Utiliser les informations détaillées de vos vidéos](use-editor-create-project.md)
- [Traiter du contenu avec l’API REST de Video Indexer](video-indexer-use-apis.md)
- [Incorporer des widgets visuels dans votre application](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Voir aussi

[Présentation de Video Indexer](video-indexer-overview.md)
