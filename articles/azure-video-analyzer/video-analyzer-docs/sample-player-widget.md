---
title: Exemple de widget de lecteur d’Azure Video Analyzer
description: Cet article de référence fournit un bref aperçu de l’exemple d’application du widget de lecteur de Video Analyzer
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 08/13/2021
ms.openlocfilehash: abea3bdd1098dbb32be35d632c7a8b0ad51fc49e
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058488"
---
# <a name="azure-video-analyzer-player-widget-sample"></a>Exemple de widget de lecteur d’Azure Video Analyzer

Cet exemple d’application montre l’intégration du widget de lecteur de Video Analyzer avec des fonctionnalités de lecture vidéo, de dessin de zone et de génération de clip vidéo.

* Cloner le [dépôt des exemples C# AVA](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp)
* Suivez les instructions du [fichier Lisez-moi dans le dossier **src/Video-Player**](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp/blob/main/src/video-player/README.md).

## <a name="get-started"></a>Bien démarrer
Une fois que vous avez suivi les instructions du fichier Lisez-moi et que l’application est en cours d’exécution sur http://localhost:3000/
1. Cliquez sur « lire les vidéos »
2. Sélectionnez une vidéo dans la liste déroulante de toutes les vidéos de votre compte Video Analyzer

## <a name="video-player"></a>Lecteur vidéo
La page Lecteur vidéo présente le lecteur standard, tel qu’il apparaît dans le portail.  
![Capture d’écran du lecteur vidéo](./media/sample-player-widget/widget-video-player.png)

## <a name="zone-drawer"></a>Dessinateur de zone
Le dessinateur de zone vous permet de créer des zones en dessinant des polygones et des lignes sur votre vidéo. Vous pouvez également enregistrer ces zones et lignes pour recevoir les coordonnées de vos zones et lignes respectives.  
  
**Exemple :**
```json
  {
    "@type": "#Microsoft.VideoAnalyzer.NamedLineString",
    "name": "Line 1",
    "line": [
      {
        "x": 0.6987951807228916,
        "y": 0.4430992736077482
      },
      {
        "x": 0.6987951807228916,
        "y": 0.7046004842615012
      }
    ]
  }{
    "@type": "#Microsoft.VideoAnalyzer.NamedPolygonString",
    "name": "Zone 2",
    "polygon": [
      {
        "x": 0.10575635876840696,
        "y": 0.7481840193704601
      },
      {
        "x": 0.16599732262382866,
        "y": 0.7796610169491526
      },
      {
        "x": 0.07764390896921017,
        "y": 0.9007263922518159
      },
      {
        "x": 0.024096385542168676,
        "y": 0.8547215496368039
      }
    ]
  }
```
Vous pouvez copier ces coordonnées à l’aide du bouton **Copier dans le Presse-papiers**. Vous pouvez renommer et supprimer des zones et des lignes à l’aide des trois points situés à côté de chaque zone et ligne.  
![Capture d’écran du dessinateur de zone.](./media/sample-player-widget/widget-zone-drawer.png)

## <a name="video-clips"></a>Clips vidéo
Les clips vidéo vous permettent de sélectionner une date et une heure de début, ainsi qu’une date et une heure de fin. Vous pouvez générer le clip vidéo à l’aide du bouton *Ajouter*.
Vous pouvez sélectionner n’importe lequel de vos clips générés dans la liste déroulante, où chaque clip est intitulé à l’aide des dates et heures de début et de fin.
(Exemple : format) Votre clip vidéo peut ensuite être affiché dans un format de lecteur vidéo classique.  
![Capture d’écran de clips vidéo.](./media/sample-player-widget/widget-video-clips.png)

## <a name="next-steps"></a>Étapes suivantes
Essayez de créer votre propre widget de lecteur Video Analyzer personnalisé en suivant ces [instructions](./player-widget.md).

