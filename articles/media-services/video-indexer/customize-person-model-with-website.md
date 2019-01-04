---
title: Utiliser le site web Video Indexer pour personnaliser un modèle de personne - Azure
titlesuffix: Azure Media Services
description: Cet article explique comment personnaliser un modèle de personne avec le site web Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/03/2018
ms.author: anzaman
ms.openlocfilehash: 321db75b1a286e7de1e38ed3b382ee7dc9fe5902
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283745"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Personnaliser un modèle de personne avec le site web Video Indexer

Video Indexer prend en charge la détection de visage et la reconnaissance de célébrités dans du contenu vidéo. La fonctionnalité de reconnaissance de célébrités couvre environ un million de visages en fonction de la source de données couramment demandée comme IMDB, Wikipedia et les principaux influenceurs LinkedIn. Les visages qui ne sont pas reconnus par la fonctionnalité de reconnaissance de célébrités sont détectés ; toutefois, ils sont laissés sans nom. Après avoir chargé votre vidéo sur Video Indexer et obtenu des résultats, vous pouvez revenir en arrière et nommer les visages qui n’ont pas été reconnus. Une fois que vous étiquetez un visage avec un nom, le visage et le nom sont ajoutés au modèle de personne de votre compte. Video Indexer peut alors reconnaître ce visage dans vos vidéos, futures et anciennes.

Vous pouvez utiliser le site web Video Indexer pour modifier des visages détectés dans une vidéo, comme décrit dans cette rubrique. Vous pouvez également utiliser l’API, comme décrit dans [Personnaliser le modèle de personne à l’aide des API](customize-person-model-with-api.md).

## <a name="edit-a-face"></a>Modifier un visage

> [!NOTE]
> Les noms étant uniques pour les modèles de personne, si vous affectez le même nom à deux visages, Video Indexer voit les visages comme représentant la même personne et les rapproche quand vous réindexez votre vidéo. Si vous voyez que Video Indexer a détecté plusieurs occurrences différentes du même visage, affectez-leur le même nom et réindexez votre vidéo.
> Vous pouvez mettre à jour un visage que Video Indexer a reconnu comme une célébrité avec un nouveau nom. Le nouveau nom affecté est prioritaire sur la reconnaissance de célébrités intégrée.

1. Accédez au site web [Video Indexer](https://www.videoindexer.ai/) et connectez-vous.
2. Recherchez une vidéo que vous souhaitez afficher et modifier dans votre compte.
3. Pour modifier un visage dans votre vidéo, accédez à l’onglet **Insights**, puis cliquez sur l’icône de crayon en haut à droite de la fenêtre.

    ![Personnaliser le modèle de personne](./media/customize-face-model/customize-face-model.png)

4. Cliquez sur l’un des visages détectés et modifiez le nom de la zone Unknown #X (Inconnu n°X) (ou le nom précédemment affecté au visage).
5. Après avoir tapé le nouveau nom, cliquez sur l’icône de coche en regard du nouveau nom. Cela permet d’enregistrer le nouveau nom ainsi que de reconnaître et de nommer toutes les occurrences de ce visage dans les autres vidéos actuelles et les vidéos que vous chargerez par la suite. La reconnaissance du visage dans vos autres vidéos actuelles peut prendre un certain temps pour entrer en vigueur, car il s’agit d’un processus de traitement par lots. 

    ![Enregistrer la mise à jour](./media/customize-face-model/save-update.png)

## <a name="delete-a-face"></a>Supprimer un visage

Pour supprimer un visage détecté dans votre vidéo, accédez au volet **Insights**, puis cliquez sur l’icône de crayon en haut à droite du volet. Cliquez sur l’option **Supprimer** sous le nom du visage. Ce visage détecté est supprimé de la vidéo. Le visage sera toujours détecté dans les autres vidéos dans lesquelles il apparaît, mais vous pouvez aussi le supprimer de ces vidéos une fois qu’elles ont été indexées. Le visage va également continuer d’exister dans le modèle de personne de votre compte si vous l’avez nommé avant de le supprimer.

## <a name="next-steps"></a>Étapes suivantes

[Personnaliser le modèle de personne à l’aide des API](customize-person-model-with-api.md)
