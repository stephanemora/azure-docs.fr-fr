---
title: Modération de vidéo avec révision manuelle - Content Moderator
titleSuffix: Azure Cognitive Services
description: Utilisez la modération de vidéo assistée par ordinateur et l'outil de révision pour modérer les contenus inappropriés
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 0c031a890efc7fad7e5d9caefce3b0e66c515d90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81404253"
---
# <a name="video-moderation-with-human-review"></a>Modération de vidéo avec révision manuelle

Utilisez la [modération de vidéo](video-moderation-api.md) assistée par ordinateur et l'[outil de révision](Review-Tool-User-Guide/human-in-the-loop.md) de Content Moderator pour modérer les vidéos et les transcriptions explicites (contenu destiné aux adultes) ou provocantes (contenu suggestif), et permettre à votre entreprise de bénéficier des meilleurs résultats.

## <a name="video-trained-classifier-preview"></a>Classifieur entraîné à l’aide de vidéos (préversion)

La classification de vidéos assistée par ordinateur est obtenue grâce à des modèles entraînés à l’aide d’images ou de vidéos. Contrairement aux classifieurs entraînés à l’aide d’images, l’apprentissage du classifieur vidéo Microsoft pour le contenu explicite ou suggestif est effectué à l’aide de vidéos. Cette méthode offre des correspondances de meilleure qualité.

## <a name="shot-detection"></a>Détection de plan

Lorsque les résultats détaillés de la classification sont obtenus, l’outil offre une plus grande souplesse dans l’analyse des vidéos grâce à une intelligence vidéo supplémentaire. Plutôt que d’isoler simplement des images, le service de modération des vidéos Microsoft fournit des informations au niveau des plans. Vous pouvez désormais analyser vos vidéos au niveau des plans et des images.

## <a name="key-frame-detection"></a>Détection de l’image clé

Au lieu d’isoler des images à intervalles réguliers, le service de modération des vidéos identifie et isole des images qui sont potentiellement complètes et de bonne qualité. La fonctionnalité génère efficacement des images afin d’analyser le contenu explicite et suggestif au niveau de l’image.

L’extrait suivant illustre une réponse partielle comportant des plans potentiels, des images clés et des scores de contenu explicite ou suggestif :

```json
"fragments":[  
  {  
    "start":0,
    "duration":18000
  },
  {  
    "start":18000,
    "duration":3600,
    "interval":3600,
    "events":[  
      [  
        {  
          "reviewRecommended":false,
          "adultScore":0.00001,
          "racyScore":0.03077,
          "index":5,
          "timestamp":18000,
          "shotIndex":0
        }
      ]
    ]
  },
  {  
    "start":18386372,
    "duration":119149,
    "interval":119149,
    "events":[  
      [  
        {  
          "reviewRecommended":true,
          "adultScore":0.00000,
          "racyScore":0.91902,
          "index":5085,
          "timestamp":18386372,
          "shotIndex":62
        }
      ]
    ]
```

## <a name="visualization-for-human-reviews"></a>Visualisation pour la révision manuelle

Dans certains cas plus nuancés, les entreprises requièrent l’intervention humaine pour examiner la vidéo, les images et les balises assignées par ordinateur. Les modérateurs qui vérifient les vidéos et les images disposent d’une vue complète des résultats, ils peuvent modifier les balises et soumettre leur décision.

![vue par défaut de l’outil de révision vidéo](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Affichage du lecteur pour examen au niveau de la vidéo

Il est possible de prendre une décision binaire au niveau de la vidéo grâce à un lecteur vidéo qui montre les images à caractère potentiellement explicite ou suggestif. Les réviseurs peuvent parcourir la vidéo manuellement et utiliser différentes options de défilement pour examiner les scènes. Ils confirment leur décision en activant ou désactivant les balises.

![vue du lecteur de l’outil de révision vidéo](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Affichage d’images pour examen détaillé

Il est possible d’examiner une vidéo en détail, image par image, grâce à un affichage par image. Les réviseurs humains passent en revue et sélectionnent une ou plusieurs images, puis activent ou désactivent les balises pour confirmer leur décision. Une étape facultative supplémentaire permet de censurer les images ou le contenu sensible.

![vue des images de l’outil de révision vidéo](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Modération de la transcription

Bien souvent, les vidéos sont accompagnées d’une voix off qui doit être également modérée afin d’identifier les propos sensibles. Vous pouvez utiliser le service Azure Media Indexer pour convertir l’audio en texte, puis l’API de révision Content Moderator pour transmettre la transcription à l’outil de révision pour la modération de texte.

![vue de la transcription de l’outil de révision vidéo](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Étapes suivantes

- Consultez le [démarrage rapide pour la modération des vidéos](video-moderation-api.md).
- Découvrez comment générer des [revues de vidéos](video-reviews-quickstart-dotnet.md) pour vos réviseurs à partir de résultats modérés.
- Ajoutez des [revues de la transcription de la vidéo](video-transcript-reviews-quickstart-dotnet.md) à vos révisions de vidéos.
- Consultez le tutoriel détaillé sur le développement d’une [solution complète de modération de vidéos](video-transcript-moderation-review-tutorial-dotnet.md).