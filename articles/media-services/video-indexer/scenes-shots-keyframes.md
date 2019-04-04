---
title: Des séquences, des captures et des images clés - Azure indexeur vidéo
titlesuffix: Azure Media Services
description: Cette rubrique donne une vue d’ensemble de l’arrière-plan Video Indexer, les captures et les images clés.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: dfa41dc695cf6ab357a9cd4cdbd32454b6dd107d
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896407"
---
# <a name="scenes-shots-and-keyframes"></a>Arrière-plan, des captures et des images clés

Video Indexer prend en charge les vidéos de segmentation en unités temporelles selon les propriétés structurelles et sémantiques. Cette fonctionnalité permet aux clients facilement parcourir, gérer et modifier leur contenu vidéo en fonction des niveaux de détail. Par exemple, basée sur les coulisses, les captures et les images clés, décrites dans cette rubrique. Le **détection de scène** fonctionnalité est actuellement en version préliminaire.   

![Arrière-plan, des captures et des images clés](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)

## <a name="scene-detection-preview"></a>Détection de scène (version préliminaire)

Video Indexer détermine si une scène des modifications de vidéo selon des signaux visuels. Une scène représente un événement unique et il est composé d’une série de captures consécutifs, qui sont sémantiquement liées. Une miniature de la scène est la première image clé de sa capture sous-jacent. Indexeur vidéo segmente une vidéo en arrière-plan en fonction de cohérence de couleur sur les captures consécutifs et récupère le début et l’heure de fin de chaque scène. Détection de la scène est considéré comme une tâche difficile car elle implique la quantification des aspects sémantiques de vidéos.

> [!NOTE]
> S’applique aux vidéos qui contiennent au moins 3 scènes.

## <a name="shot-detection"></a>Détection de plan

Video Indexer détermine quand un plan change dans la vidéo selon des signaux visuels, en effectuant le suivi des transitions brusques et progressive du jeu de couleurs d’images adjacentes. Les métadonnées de la capture incluent un début et heure de fin, ainsi que la liste d’images clés inclus dans cette capture. Les captures sont des images consécutives effectuées à partir de la caméra même en même temps.

## <a name="keyframe-detection"></a>Détection de l’image clé

Sélectionne les frames qui représenter au mieux la capture. Les images clés sont les images représentatives sélectionnés à partir de la vidéo complète basée sur les propriétés esthétiques (par exemple, contraste et stableness). Video Indexer récupère une liste d’image clé ID en tant que partie des métadonnées de la capture, basée sur les clients peuvent extraire la miniature de l’image clé. 

Images clés sont associés à des captures dans la sortie JSON. 

## <a name="next-steps"></a>Étapes suivantes

[Examinez la sortie de Video Indexer produite par l’API](video-indexer-output-json-v2.md#scenes)