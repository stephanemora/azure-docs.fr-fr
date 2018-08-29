---
title: Vue d’ensemble du processus de conception pour Acoustics - Cognitive Services
description: Ce document explique comment exprimer votre intention de conception dans les trois phases du flux de travail Project Acoustics.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 3b35f78d66a6ae66b0a56818f5d4be455ce00de5
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181278"
---
# <a name="design-process-overview"></a>Vue d’ensemble du processus de conception
Vous pouvez exprimer votre intention de conception dans les trois phases du flux de travail Project Acoustics : conception pré-baking, placement de la source audio et conception post-baking. Le processus nécessite moins de balisage associé au placement des volumes de réverbération, tout en laissant au concepteur le contrôle sur l’apparence sonore d’une scène.

## <a name="pre-bake-design"></a>Conception pré-baking
Le processus de conception pré-baking produit la scène et les métadonnées qui sont utilisées pour la simulation des ondes sonores, qui inclut la sélection des éléments de la scène qui participeront à la simulation pour fournir la réverbération, les réflexions et les occlusions. Les métadonnées de la scène sont la sélection des matériaux acoustiques pour chaque élément de la scène. Les matériaux acoustiques contrôlent la quantité d’énergie sonore reflétée par chaque surface.

Le coefficient d’absorption par défaut pour toutes les surfaces est 0,04, ce qui correspond à une réflexion très élevée. Vous pouvez obtenir des effets esthétiques et de jeu en ajustant les coefficients d’absorption de matières différentes tout au long de la scène, qui sont particulièrement importants pour les auditeurs quand ils entendent les transitions d’une zone de la scène à une autre. Par exemple, effectuer une transition d’une pièce sombre à réverbération vers une scène claire en extérieur sans réverbération améliore l’impact de la transition. Pour obtenir cet effet, paramétrez les coefficients d’absorption sur les matières de la scène en extérieur avec des valeurs plus élevées.

La durée de réverbération d’une matière donnée dans une pièce est inversement proportionnelle à son coefficient d’absorption, la plupart des matières ayant des valeurs d’absorption comprises entre 0,01 et 0,20. Les matières avec des coefficients d’absorption en dehors de cette plage sont très absorbantes.

![Graphe de durée de réverbération](media/ReverbTimeGraph.png)

La [procédure pas à pas dans l’interface utilisateur de baking](bake-ui-walkthrough.md) décrit en détail les contrôles pré-baking.

## <a name="sound-source-placement"></a>Placement de la source audio
L’affichage des points de sonde et voxels au moment de l’exécution peut aider à déboguer les problèmes liés au blocage des sources sonores à l’intérieur de la géométrie voxelisée. Pour activer/désactiver l’affichage des points de sonde et de la grille de voxels, cliquez sur la case à cocher correspondante dans le menu Gizmos, dans le coin supérieur droit de la vue de la scène. Si la source sonore est à l’intérieur d’un voxel mural, déplacez-la dans un voxel d’air.

![Menu Gizmos](media/GizmosMenu.png)  

L’affichage de voxel peut aider à déterminer si une transformation est appliquée aux composants visuels du jeu. Si c’est le cas, appliquez la même transformation au GameObject qui héberge **Acoustics Manager**.

## <a name="post-bake-design"></a>Conception post-baking
Les résultats du baking sont stockés dans le fichier ACE en tant que paramètres d’occlusion et de réverbération pour toutes les paires d’emplacements source-auditeur tout au long de la scène. Ce résultat physiquement précis peut être utilisé tel quel pour votre projet, et constitue un excellent point de départ pour la conception. Le processus de conception post-baking spécifie les règles de transformation des paramètres de résultat de baking au moment de l’exécution.

### <a name="distance-based-attenuation"></a>Atténuation basée sur la distance
Le DSP audio fourni par le plug-in spatialiseur **Microsoft Acoustics** Unity spatial respecte l’atténuation par source basée sur la distance intégrée à l’éditeur Unity. Les contrôles pour l’atténuation basée sur la distance sont dans le composant **Audio Source** qui se trouve dans le panneau **Inspector** des sources sonores, sous **3D Sound Settings** :

![Atténuation de la distance](media/distanceattenuation.png)

### <a name="tuning-scene-parameters"></a>Réglage des paramètres de la scène
Pour ajuster les paramètres pour toutes les sources, cliquez sur le bandeau de canal dans l’**Audio Mixer** de Unity et ajustez les paramètres sur l’effet **Acoustics Mixer**.

![Personnalisation du Mixer](media/MixerParameters.png)

### <a name="tuning-source-parameters"></a>Réglage des paramètres de la source
L’attachement du script **AcousticsSourceCustomization** à une source autorise le réglage des paramètres pour cette source. Pour attacher le script, cliquez sur **Add Component** en bas du panneau **Inspector** et accédez à **Scripts > Acoustics Source Customization**. Le script a trois paramètres :

![Personnalisation de la source](media/SourceCustomization.png)

* **Reverb Power Adjust** : ajuste la puissance de réverbération, en dB. Les valeurs positives donnent plus de réverbération à un son, tandis que les valeurs négatives rendent un son plus sec.
* **Decay Time Scale** : ajuste un multiplicateur pour la durée d’atténuation. Par exemple, si le résultat du baking spécifie une durée d’atténuation de 750 millisecondes, mais que cette valeur est définie sur 1,5, la durée d’atténuation appliquée à la source est de 1,125 milliseconde.
* **Enable Acoustics** : contrôle si l’acoustique est appliquée à cette source. Quand cette option est désactivée, la source est spatialisée avec des HRTF, mais sans acoustique, ce qui signifie sans obstruction, occlusion et paramètres de réverbération dynamique tels que niveau et durée d’atténuation. La réverbération est tout de même appliquée avec un niveau et une durée d’atténuation fixes.

Différentes sources peuvent nécessiter des paramètres différents pour obtenir certains effets esthétiques ou de jeu. Le dialogue est un exemple possible. L’oreille humaine est plus habituée à la réverbération dans la parole, alors que le dialogue doit souvent être intelligible pour le jeu. Vous pouvez en tenir compte sans rendre le dialogue non-diégétique en ajustant la puissance de réverbération vers le bas.
