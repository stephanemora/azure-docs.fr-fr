---
title: Scènes, captures et images clés Azure Video Analyzer for Media (anciennement Video Indexer)
titleSuffix: Azure Video Analyzer for Media
description: Cette rubrique fournit une vue d’ensemble des scènes, captures et images clés d’Azure Video Analyzer for Media (anciennement Video Indexer).
services: azure-video-analyzer
author: Juliako
manager: femila
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 80494f98d789e5d04454065ff5cbf8ba97fd62f1
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112119188"
---
# <a name="scenes-shots-and-keyframes"></a>Scènes, captures et images clés

Azure Video Analyzer for Media (anciennement Video Indexer) prend en charge la segmentation de vidéos en unités temporelles basées sur des propriétés sémantiques et structurelles. Cette fonctionnalité permet aux clients de facilement parcourir, gérer et modifier leur contenu vidéo en fonction des niveaux de détail. Par exemple, en fonction des scènes, des captures et des images clés, décrites dans cette rubrique.   

![Scènes, captures et images clés](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Détection de scène  
 
Video Analyzer for Media détermine quand une scène change dans la vidéo via des signaux visuels. Une scène représente un événement unique et est composée d’une série de captures consécutives, qui sont sémantiquement liées. Une miniature de la scène constitue la première image clé de sa capture sous-jacente. Video Analyzer for Media segmente une vidéo en scènes en fonction de la cohérence des couleurs sur des captures consécutives et récupère le début et la fin de chaque scène. La détection de scène est considérée comme étant une tâche ardue, car elle implique de quantifier des aspects sémantiques d’une vidéo.

> [!NOTE]
> S’applique aux vidéos qui contiennent au moins 3 scènes.

## <a name="shot-detection"></a>Détection de plan

Video Analyzer for Media détermine quand une capture change dans la vidéo selon des signaux visuels, en effectuant le suivi des transitions brusques et progressives du jeu de couleurs des images adjacentes. Les métadonnées de la capture incluent un temps de début et de fin, ainsi que la liste des images clés inclues dans cette capture. Les captures sont des images consécutives prises de la même caméra et en même temps.

## <a name="keyframe-detection"></a>Détection d’une image clé

Video Analyzer for Media sélectionne la ou les image(s) qui représentent le mieux chaque capture. Les images clés sont des images représentatives sélectionnées à partir de la vidéo complète, en fonction des propriétés esthétiques (par exemple, contraste et stabilité). Video Analyzer for Media récupère une liste d’ID d’images clés faisant partie des métadonnées de la capture, en fonction des clients qui peuvent extraire l’image clé en tant qu’image haute résolution.  

### <a name="extracting-keyframes"></a>Extraction d’images clés

Pour extraire des images clés haute résolution pour votre vidéo, vous devez d’abord charger et indexer la vidéo.

![Images clés](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-analyzer-for-media-website"></a>Avec le site web Azure Video Analyzer for Media

Pour extraire des images clés à l’aide du site web Video Analyzer for Media, chargez et indexez votre vidéo. Une fois la tâche d’indexation terminée, cliquez sur le bouton **Télécharger** et sélectionnez **Artefacts (ZIP)** . Cela permet de télécharger le dossier d’artefacts sur votre ordinateur. 

![Capture d’écran montrant la liste déroulante « Télécharger » avec l’option « Artefacts » sélectionnée.](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Décompressez et ouvrez le dossier. Dans le dossier *_KeyframeThumbnail*, vous trouverez toutes les images clés qui ont été extraites de votre vidéo. 

#### <a name="with-the-video-analyzer-for-media-api"></a>Avec l’API Azure Video Analyzer for Media

Pour récupérer des images clés à l’aide de l’API Video Indexer, chargez et indexez votre vidéo à l’aide de l’appel [Charger la vidéo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video). Une fois la tâche d’indexation terminée, appelez [Obtenir l’index vidéo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Index). Vous obtiendrez ainsi toutes les informations que Video Indexer a extraites de votre contenu dans un fichier JSON.  

Vous obtiendrez une liste d’ID d’images clés dans les métadonnées de chaque plan. 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

Vous devrez ensuite exécuter chacun de ces ID d’images clés sur l’appel [Obtenir des miniatures](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail). Chacune des images clés sera alors téléchargée sur votre ordinateur. 

## <a name="editorial-shot-type-detection"></a>Détection du type de plan éditorial

Les images clés sont associées à des captures dans la sortie JSON. 

Le type de plan associé à un plan individuel dans les insights JSON représente son type éditorial. Vous pouvez trouver ces caractéristiques de type de plan utiles lors de la modification de vidéos dans des clips ou bandes-annonces, ou lors de la recherche d’un style spécifique d’image clé à des fins artistiques. Les différents types sont déterminés en fonction de l’analyse de la première image clé de chaque plan. Les plans sont identifiés par l’échelle, la taille et l’emplacement des visages apparaissant dans leur première image clé. 

La taille et l’échelle du plan sont déterminées en fonction de la distance entre l’appareil photo et les visages apparaissant dans l’image. À l’aide de ces propriétés, Video Analyzer for Media détecte les types de captures suivantes :

* Large : montre le corps tout entier d’une personne.
* Moyen : montre le haut du corps et le visage d’une personne.
* Gros plan : montre principalement le visage d’une personne.
* Très gros plan : montre le visage d’une personne qui remplit l’écran. 

Les types de plans peuvent également être déterminés par l’emplacement des personnages par rapport au centre de l’image. Cette propriété définit les types de captures suivants dans Video Analyzer for Media :

* Visage à gauche : une personne apparaît sur la gauche de l’image.
* Visage au centre : une personne apparaît dans la partie centrale de l’image.
* Visage à droite : une personne apparaît sur la droite de l’image.
* Extérieur : une personne apparaît dans un décor extérieur.
* Intérieur : une personne apparaît dans un décor intérieur.

Caractéristiques supplémentaires :

* Deux plans : montre les visages de taille moyenne de deux personnes.
* Plusieurs visages : plus de deux personnes.


## <a name="next-steps"></a>Étapes suivantes

[Examiner la sortie de Video Analyzer for Media générée par l’API](video-indexer-output-json-v2.md#scenes)
