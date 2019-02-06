---
title: Vue d’ensemble du processus de conception Project Acoustics
titlesuffix: Azure Cognitive Services
description: Ce document explique comment exprimer votre intention de conception dans les trois phases du flux de travail Project Acoustics.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: cf38b2096e958a7484e5161277a608ec2cb88224
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470483"
---
# <a name="design-process-overview"></a>Vue d’ensemble du processus de conception
Vous pouvez exprimer votre intention de conception dans les trois phases du flux de travail Project Acoustics : configuration de la scène pré-baking, placement de la source audio et conception post-baking. Le processus nécessite moins de balisage associé au placement des volumes de réverbération, tout en laissant au concepteur le contrôle sur l’apparence sonore d’une scène.

## <a name="pre-bake-design"></a>Conception pré-baking
Le processus de configuration de la scène pré-baking produit la scène et les métadonnées qui sont utilisées pour la simulation des ondes sonores, qui inclut la sélection des éléments de la scène qui participeront à la simulation pour fournir la réverbération, les réflexions et les occlusions. Les métadonnées de la scène sont la sélection des matériaux acoustiques pour chaque élément de la scène. Les matériaux acoustiques contrôlent la quantité d’énergie sonore reflétée par chaque surface.

Le coefficient d’absorption par défaut pour toutes les surfaces est 0,04, ce qui correspond à une réflexion très élevée. Vous pouvez obtenir des effets esthétiques et de jeu en ajustant les coefficients d’absorption de matières différentes tout au long de la scène, qui sont particulièrement importants pour les auditeurs quand ils entendent les transitions d’une zone de la scène à une autre. Par exemple, effectuer une transition d’une pièce sombre à réverbération vers une scène claire en extérieur sans réverbération améliore l’impact de la transition. Pour obtenir cet effet, paramétrez les coefficients d’absorption sur les matières de la scène en extérieur avec des valeurs plus élevées.

La durée de réverbération d’une matière donnée dans une pièce est inversement proportionnelle à son coefficient d’absorption, la plupart des matières ayant des valeurs d’absorption comprises entre 0,01 et 0,20. Les matières avec des coefficients d’absorption en dehors de cette plage sont très absorbantes.

![Graphe de durée de réverbération](media/ReverbTimeGraph.png)

La [procédure pas à pas dans l’interface utilisateur de baking](bake-ui-walkthrough.md) décrit en détail les contrôles pré-baking.

## <a name="sound-source-placement"></a>Placement de la source audio
L’affichage des points de sonde et voxels au moment de l’exécution peut aider à déboguer les problèmes liés au blocage des sources sonores à l’intérieur de la géométrie voxelisée. Pour activer/désactiver l’affichage des points de sonde et de la grille de voxels, cliquez sur la case à cocher correspondante dans le menu Gizmos, dans le coin supérieur droit de la vue de la scène. Si la source sonore est à l’intérieur d’un voxel mural, déplacez-la dans un voxel d’air.

![Menu Gizmos](media/GizmosMenu.png)  

L’affichage de voxel peut aider à déterminer si une transformation est appliquée aux composants visuels du jeu. Si c’est le cas, appliquez la même transformation au GameObject qui héberge **Acoustics Manager**.

### <a name="voxel-size-discrepancies"></a>Écarts de taille de voxel
Vous pouvez remarquer que la taille des voxels utilisés pour illustrer les maillages de scène qui interviennent dans le baking d’acoustique diffèrent dans les affichages de runtime et de moment de la conception. Cette différence n’affecte ni la qualité ni la granularité de la fréquence de simulation sélectionnée, mais est plutôt un sous-produit de l’utilisation par le runtime de la scène voxelizée. Au niveau du runtime, les voxels de simulation sont « affinés » pour prendre en charge l’interpolation entre emplacements source. Cela permet également le positionnement au moment de la conception de sources sonores plus près des maillages de scène que ne le permet la taille de voxel de simulation, car les sources à l’intérieur d’un voxel qui contiennent un maillage traité de façon acoustique ne produisent aucun son.

Voici deux images présentant la différence entre voxels de conception (pré-baking) et voxels de runtime (post-baking), telles que visualisées par le plug-in Unity :

Voxels au moment de la conception :

![VoxelsDesignTime](media/VoxelsDesignTime.png)

Voxels de runtime :

![VoxelsRuntime](media/VoxelsRuntime.png)

La décision quant à savoir si le maillage du voxel représente ou non avec précision les maillages de scène architecturaux/structurels doit être prise à l’aide des voxels en mode conception, pas de la visualisation de runtime des voxels affinés.

## <a name="post-bake-design"></a>Conception post-baking
Les résultats du baking sont stockés dans le fichier ACE en tant que paramètres d’occlusion et de réverbération pour toutes les paires d’emplacements source-auditeur tout au long de la scène. Ce résultat physiquement précis peut être utilisé tel quel pour votre projet, et constitue un excellent point de départ pour la conception. Le processus de conception post-baking spécifie les règles de transformation des paramètres de résultat de baking au moment de l’exécution.

### <a name="distance-based-attenuation"></a>Atténuation basée sur la distance
Le DSP audio fourni par le plug-in spatialiseur **Microsoft Acoustics** Unity spatial respecte l’atténuation par source basée sur la distance intégrée à l’éditeur Unity. Les contrôles pour l’atténuation basée sur la distance sont dans le composant **Audio Source** qui se trouve dans le panneau **Inspector** des sources sonores, sous **3D Sound Settings** :

![Atténuation de la distance](media/distanceattenuation.png)

Acoustics effectue un calcul dans une zone « région de simulation » centrée autour de l’emplacement du joueur. Si une source sonore est éloignée du joueur, en dehors de cette région de simulation, seule la géométrie au sein de la zone affecte la propagation du son (par exemple, à l’origine d’occlusion) qui fonctionne relativement bien lorsque les dispositifs d’occlusion sont à proximité du joueur. Toutefois, dans les cas où le joueur se trouve dans un espace ouvert tandis que les dispositifs d’occlusion sont proches de la source sonore distante, le son peut être « désocclu » artificiellement. La solution de contournement que nous suggérons consiste à s’assurer que, dans de tels cas, l’atténuation du son chute à entre 0 et environ 45 m, distance horizontale par défaut entre le joueur et la périphérie de la zone.

### <a name="tuning-scene-parameters"></a>Réglage des paramètres de la scène
Pour ajuster les paramètres de toutes les sources, cliquez sur le bandeau de canal dans l’**Audio Mixer** de Unity et ajustez les paramètres sur l’effet **Project Acoustics Mixer**.

![Personnalisation du Mixer](media/MixerParameters.png)

* **Wetness Adjust** (Ajustement de l’humidité) : ajuste la puissance de réverbération, en dB, sur toutes les sources de la scène en fonction de la distance source-auditeur. Les valeurs positives donnent plus de réverbération à un son, tandis que les valeurs négatives rendent un son plus sec.
* **RT60 Scale** (Mesure RT60) : valeur scalaire de multiplication pour le temps de réverbération.
* **Use Panning** (Utiliser panoramique) : contrôle si la sortie audio est de type panoramique binaural (0) ou multicanal (1). Les valeurs autres que 1 signifie « binaural ». La sortie de type binaural est spatialisée avec des fonctions HRTF pour une utilisation avec un casque, tandis que la sortie de type multicanal est spatialisée avec VBAP pour une utilisation avec des systèmes de haut-parleur surround multicanal. Si vous utilisez le panoramique multicanal, veillez à sélectionner le mode de haut-parleur qui correspond aux paramètres de votre appareil, disponibles sous **Paramètres du projet** > **Audio**.

![SpeakerMode](media/SpeakerMode.png)

### <a name="tuning-source-parameters"></a>Réglage des paramètres de la source
L’association du script **AcousticsAdjust** à une source permet le réglage des paramètres pour cette source. Pour associer le script, cliquez sur **Add Component** (Ajouter un composant) en bas du panneau **Inspector** (Inspecteur), puis accédez à **Scripts > Acoustics Adjust** (Réglages acoustiques). Le script comporte six contrôles :

![AcousticsAdjust](media/AcousticsAdjust.png)

* **Enable Acoustics** : contrôle si l’acoustique est appliquée à cette source. Lorsque l’option n’est pas sélectionnée, la source est spatialisée avec des fonctions HRTF ou des panoramiques, mais il n’y a pas d’acoustique. Cela signifie qu’il n’y a pas de paramètres de réverbération dynamique, d’occlusion ou d’obstruction tel que le niveau et la durée de perte. La réverbération est tout de même appliquée avec un niveau et une durée d’atténuation fixes.
* **Occlusion** : applique un multiplicateur au niveau de décibels d’occlusion calculé par le système acoustique. Si la valeur de ce multiplicateur est supérieure à 1, l’occlusion est exagérée, tandis que des valeurs inférieures à 1 rendent l’effet de l’occlusion plus subtil, et que la valeur 0 désactive l’occlusion.
* **Transmission (dB)**  : définit l’atténuation (en décibels) provoquée par une transmission via une géométrie. Réglez ce curseur à son niveau le plus bas pour désactiver la transmission. Acoustics spatialise le son brut initial comme s’il contournait une géométrie de scène (effet de portail). Une transmission fournit une arrivée brute supplémentaire qui est spatialisée dans la direction de la ligne de mire. Notez que la courbe d’atténuation de la distance pour la source est également appliquée.
* **Wetness (dB)**  : ajuste la puissance de réverbération, exprimée en décibels, en fonction de la distance de la source. Les valeurs positives donnent plus de réverbération à un son, tandis que les valeurs négatives rendent un son plus sec. Cliquez sur le contrôle de courbe (ligne verte) pour afficher l’éditeur de courbe. Modifiez la courbe en cliquant pour ajouter des points, et en faisant glisser ces points pour former la fonction souhaitée. L’axe X indique la distance de la source, et l’axe Y l’ajustement de réverbération exprimé en décibels. Pour plus de détails sur la modification des courbes, consultez ce document intitulé [Unity Manual](https://docs.unity3d.com/Manual/EditingCurves.html) (Manuel Unité). Pour rétablir la courbe par défaut, cliquez avec le bouton droit sur **Wetness** (Effet), puis sélectionnez **Reset** (Réinitialiser).
* **Decay Time Scale** : ajuste un multiplicateur pour la durée d’atténuation. Par exemple, si le résultat du baking spécifie une durée d’atténuation de 750 millisecondes, mais que cette valeur est définie sur 1,5, la durée d’atténuation appliquée à la source est de 1,125 milliseconde.
* **Outdoorness** : ajustement supplémentaire de l’estimation du système acoustique concernant le caractère « extérieur » du son se réverbérant sur une source. Définir cette valeur sur 1 permet à une source sonore de produire un son totalement extérieur ; définir cette valeur sur -1 permet de produire un son intérieur.

Différentes sources peuvent nécessiter des paramètres différents pour obtenir certains effets esthétiques ou de jeu. Le dialogue est un exemple possible. L’oreille humaine est plus habituée à la réverbération dans la parole, alors que le dialogue doit souvent être intelligible pour le jeu. Vous pouvez refléter cela sans rendre le dialogue non diégétique en faisant coulisser le curseur **Wetness** (Effet) vers le bas, en ajustant le paramètre **Perceptual Distance Warp** (Distorsion de perception due à la distance) décrit ci-dessous, en ajoutant une **Transmission** pour certains sons bruts se propageant à travers des murs, et/ou en réduisant l’**occlusion** à partir de 1 afin que davantage de son soit acheminé via les portails.

Le fait d’associer le script **AcousticsAdjustExperimental** à une source permet un réglage expérimental supplémentaire des paramètres pour cette source. Pour associer le script, cliquez sur **Add Component** (Ajouter un composant) en bas du panneau **Inspector**, puis accédez à **Scripts > Acoustics Source Customization** (Personnalisation de source acoustique). Il existe actuellement un seul contrôle expérimental :

![AcousticsAdjustExperimental](media/AcousticsAdjustExperimental.png)

* **Perceptual Distance Warp** : applique une distorsion exponentielle en fonction de la distance, utilisée pour calculer le ratio brut-effet. Le système acoustique calcule les niveaux d’effet dans l’espace, qui varient de façon régulière en fonction de la distance et fournissent des repères pour la perception de la distance. Des valeurs de déformation supérieures à 1 accentuent cet effet en augmentant les niveaux de réverbération relatifs à la distance, ce qui rend le son « distant ». Des valeurs de déformation inférieures à 1 rendent plus subtiles les modifications de réverbération en fonction de la distance, ce qui rend le son plus « présent ».

