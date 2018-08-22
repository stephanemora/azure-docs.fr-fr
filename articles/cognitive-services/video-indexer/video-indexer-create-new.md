---
title: Utiliser Azure Video Indexer pour créer des insights vidéo à partir de vidéos existantes | Microsoft Docs
description: Cette rubrique vous montre comment créer et publier des insights vidéo à partir d’autres vidéos.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: 161a47f72a0f8038a515c09f25ec2a8e8a520547
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390838"
---
# <a name="create-highlights-from-existing-videos"></a>Créer des points importants à partir de vidéos existantes

Cette rubrique vous montre comment créer et publier des insights vidéo à partir d’autres vidéos.

1. Connectez-vous à votre compte [Video Indexer](https://api-portal.videoindexer.ai/).
2. Recherchez une vidéo à partir de laquelle vous souhaitez créer vos insights vidéo.
3. Appuyez sur **Lecture**.

    La page affiche les insights résumés de la vidéo. 

    ![Insights](./media/video-indexer-create-new/video-indexer-summarized-insights.png)

3. Appuyez sur le bouton **Modifier**.

    Cette page affiche la répartition complète d’une vidéo. La répartition est divisée en blocs. Les blocs sont là pour simplifier le parcours des données. Par exemple, un bloc peut être défini par un changement d’intervenant ou une pause prolongée. Vous pouvez créer votre propre playlist contenant uniquement les lignes souhaitées. Pour afficher uniquement certaines parties de la vidéo source, vous pouvez filtrer par rubriques/mots clés, sentiments, personnes, intervenants. Vous pouvez choisir d’afficher uniquement la transcription ou la reconnaissance optique des caractères (OCR) de la vidéo.    

    ![Insights](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)

4. Créez votre playlist.

    Pour ajouter ou supprimer des lignes de votre playlist, appuyez sur **+**/**-**.

5. Affichez un aperçu de votre playlist.

    Une fois que vous avez terminé la création de la playlist, appuyez sur **Aperçu**.
6. Publiez la playlist.

    Après avoir affiché un aperçu de la playlist, vous pouvez la publier.

    Une fois la playlist publiée, elle est ajoutée à la liste de vos insights vidéo.


## <a name="next-steps"></a>Étapes suivantes 

Une fois votre nouvelle playlist créée, vous pouvez continuer de la traiter, comme décrit dans l’une des rubriques suivantes : 

- [Traiter du contenu avec l’API REST de Video Indexer](video-indexer-use-apis.md)
- [Incorporer des widgets visuels dans votre application](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Voir aussi

[Présentation de Video Indexer](video-indexer-overview.md) 
