---
title: Tutoriel de conception Project Acoustics dans Unity
titlesuffix: Azure Cognitive Services
description: Ce tutoriel décrit le workflow de conception pour Project Acoustics dans Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fd00e4105ce4edae9d014df2a83c5ae3aaf778da
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854262"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Tutoriel de conception Project Acoustics dans Unity
Ce tutoriel décrit les outils et le workflow de conception pour Project Acoustics dans Unity.

Configuration requise :
* Unity 2018.2+ pour Windows
* Une scène Unity avec une ressource acoustique ayant fait l’objet d’un baking

Pour ce tutoriel, vous pouvez obtenir une scène Unity avec une ressource acoustique ayant fait l’objet d’un baking de deux façons :
* [Ajoutez Project Acoustics à votre projet Unity](unity-integration.md), puis [obtenez un compte Azure Batch](create-azure-account.md), puis [effectuez un bake de votre scène Unity](unity-baking.md)
* Vous pouvez aussi utiliser l’[exemple de contenu Project Acoustics Unity](unity-quickstart.md).

## <a name="review-design-process-concepts"></a>Passer en revue les concepts du processus de conception
Project Acoustics utilise des méthodes de traitement numérique des signaux (DSP) audio courantes pour traiter vos sources, et vous permet de contrôler des propriétés acoustiques communes, notamment l’occlusion, le mélange sec/humide et la longueur de la queue de réverbération (RT60). Le [concept principal du processus de conception de Project Acoustics](design-process.md) est qu’au lieu de définir ces propriétés directement, vous contrôlez la façon dont les résultats de la simulation sont utilisés pour piloter ces propriétés. Les paramètres par défaut pour chaque contrôle représentent physiquement des effets acoustiques précis.

## <a name="design-acoustics-for-each-source"></a>Concevoir des effets acoustiques pour chaque source
Project Acoustics fournit plusieurs contrôles de conception d’effets acoustiques spécifiques à la source. Ceci vous permet de contrôler la combinaison dans une scène en accentuant certaines sources et en en désaccentuant d’autres.

### <a name="adjust-distance-based-attenuation"></a>Ajuster l’atténuation basée sur la distance
Le DSP audio fourni par le plug-in spatialiseur **Project Acoustics** Unity respecte l’atténuation basée sur la distance par source intégrée à l’éditeur Unity. Les contrôles pour l’atténuation basée sur la distance sont dans le composant **Audio Source** qui se trouve dans le panneau **Inspector** des sources sonores, sous **3D Sound Settings** :

![Capture d’écran du panneau des options d’atténuation de la distance dans Unity](media/distance-attenuation.png)

Acoustics effectue un calcul dans une zone « région de simulation » centrée autour de l’emplacement du joueur. Si une source sonore est éloignée du joueur, en dehors de cette région de simulation, seule la géométrie au sein de la zone affecte la propagation du son (par exemple, à l’origine d’occlusion) qui fonctionne relativement bien lorsque les dispositifs d’occlusion sont à proximité du joueur. Toutefois, dans les cas où le joueur se trouve dans un espace ouvert tandis que les dispositifs d’occlusion sont proches de la source sonore distante, le son peut être « désocclu » artificiellement. La solution de contournement que nous suggérons consiste à s’assurer que, dans de tels cas, l’atténuation du son chute à entre 0 et environ 45 m, distance horizontale par défaut entre le joueur et la périphérie de la zone.

![Capture d’écran du panneau d’options SpeakerMode dans Unity](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>Ajuster l’occlusion et la transmission
L’association du script **AcousticsAdjust** à une source permet le réglage des paramètres pour cette source. Pour associer le script, cliquez sur **Add Component** (Ajouter un composant) en bas du panneau **Inspector** (Inspecteur), puis accédez à **Scripts > Acoustics Adjust** (Réglages acoustiques). Le script comporte six contrôles :

![Capture d’écran du script AcousticsAdjust Unity](media/acoustics-adjust.png)

* **Enable Acoustics** : contrôle si l’acoustique est appliquée à cette source. Lorsque l’option n’est pas sélectionnée, la source est spatialisée avec des fonctions HRTF ou des panoramiques, mais il n’y a pas d’acoustique. Cela signifie qu’il n’y a pas de paramètres de réverbération dynamique, d’occlusion ou d’obstruction, comme le niveau et la durée d’atténuation. La réverbération est tout de même appliquée avec un niveau et une durée d’atténuation fixes.
* **Occlusion** : applique un multiplicateur au niveau de décibels d’occlusion calculé par le système acoustique. Si la valeur de ce multiplicateur est supérieure à 1, l’occlusion est exagérée, tandis que des valeurs inférieures à 1 rendent l’effet de l’occlusion plus subtil, et que la valeur 0 désactive l’occlusion.
* **Transmission (dB)**  : définit l’atténuation (en décibels) provoquée par une transmission via une géométrie. Réglez ce curseur à son niveau le plus bas pour désactiver la transmission. Acoustics spatialise le son brut initial comme s’il contournait une géométrie de scène (effet de portail). Une transmission fournit une arrivée brute supplémentaire qui est spatialisée dans la direction de la ligne de mire. Notez que la courbe d’atténuation de la distance pour la source est également appliquée.

### <a name="adjust-reverberation"></a>Ajuster la réverbération
* **Wetness (dB)**  : ajuste la puissance de réverbération, exprimée en décibels, en fonction de la distance de la source. Les valeurs positives donnent plus de réverbération à un son, tandis que les valeurs négatives rendent un son plus sec. Cliquez sur le contrôle de courbe (ligne verte) pour afficher l’éditeur de courbe. Modifiez la courbe en cliquant pour ajouter des points, et en faisant glisser ces points pour former la fonction souhaitée. L’axe X indique la distance de la source, et l’axe Y l’ajustement de réverbération exprimé en décibels. Pour plus de détails sur la modification des courbes, consultez ce document intitulé [Unity Manual](https://docs.unity3d.com/Manual/EditingCurves.html) (Manuel Unité). Pour rétablir la courbe par défaut, cliquez avec le bouton droit sur **Wetness** (Effet), puis sélectionnez **Reset** (Réinitialiser).
* **Decay Time Scale** : ajuste un multiplicateur pour la durée d’atténuation. Par exemple, si le résultat du baking spécifie une durée d’atténuation de 750 millisecondes, mais que cette valeur est définie sur 1,5, la durée d’atténuation appliquée à la source est de 1,125 milliseconde.
* **Outdoorness** : ajustement supplémentaire de l’estimation du système acoustique concernant le caractère « en extérieur » de la réverbération sur une source. La définition de cette valeur sur 1 permet à une source sonore de produire un son totalement « en extérieur » ; la définition de cette valeur sur -1 permet de produire un son complètement « en intérieur ».

Le fait d’associer le script **AcousticsAdjustExperimental** à une source permet un réglage expérimental supplémentaire des paramètres pour cette source. Pour associer le script, cliquez sur **Add Component** (Ajouter un composant) en bas du panneau **Inspector**, puis accédez à **Scripts > Acoustics Source Customization** (Personnalisation de source acoustique). Il existe actuellement un seul contrôle expérimental :

![Capture d’écran du script AcousticsAdjustExperimental Unity](media/acoustics-adjust-experimental.png)

* **Perceptual Distance Warp** : applique une distorsion exponentielle en fonction de la distance, utilisée pour calculer le ratio brut-effet. Le système acoustique calcule les niveaux d’effet dans l’espace, qui varient de façon régulière en fonction de la distance et fournissent des repères pour la perception de la distance. Des valeurs de déformation supérieures à 1 accentuent cet effet en augmentant les niveaux de réverbération relatifs à la distance, ce qui rend le son « distant ». Des valeurs de déformation inférieures à 1 rendent plus subtiles les modifications de réverbération en fonction de la distance, ce qui rend le son plus « présent ».

## <a name="design-acoustics-for-all-sources"></a>Concevoir des effets acoustiques pour toutes les sources
Pour ajuster les paramètres de toutes les sources, cliquez sur le bandeau de canal dans l’**Audio Mixer** de Unity et ajustez les paramètres sur l’effet **Project Acoustics Mixer**.

![Capture d’écran du panneau de personnalisation du Mixer Project Acoustics Unity](media/mixer-parameters.png)

* **Wetness Adjust** (Ajustement de l’humidité) : ajuste la puissance de réverbération, en dB, sur toutes les sources de la scène en fonction de la distance source-auditeur. Les valeurs positives donnent plus de réverbération à un son, tandis que les valeurs négatives rendent un son plus sec.
* **RT60 Scale** (Mesure RT60) : valeur scalaire de multiplication pour le temps de réverbération.
* **Use Panning** (Utiliser panoramique) : contrôle si la sortie audio est de type panoramique binaural (0) ou multicanal (1). Les valeurs autres que 1 signifie « binaural ». La sortie de type binaural est spatialisée avec des fonctions HRTF pour une utilisation avec un casque, tandis que la sortie de type multicanal est spatialisée avec VBAP pour une utilisation avec des systèmes de haut-parleur surround multicanal. Si vous utilisez le panoramique multicanal, veillez à sélectionner le mode de haut-parleur qui correspond aux paramètres de votre appareil, disponibles sous **Paramètres du projet** > **Audio**.

## <a name="check-proper-sound-source-placement"></a>Vérifier l’emplacement correct des sources sonores
Les sources sonores placées à l’intérieur de voxels occupés ne sont pas soumises à un traitement acoustique. Comme les voxels s’étendent au-delà de la géométrie de la scène visible, il est possible de placer une source à l’intérieur d’un voxel alors qu’il apparaît non bloqué selon la géométrie visuelle. Vous pouvez visualiser les voxels de Project Acoustics en décochant la case de la grille de voxels dans le menu **Gizmos**, dans le coin supérieur droit de la vue **Scene**.

![Capture d’écran du menu Gizmos dans Unity](media/gizmos-menu.png)  

L’affichage des voxels peut aussi aider à déterminer si une transformation est appliquée aux composants visuels du jeu. Si c’est le cas, appliquez la même transformation au GameObject qui héberge **Acoustics Manager**.

### <a name="bake-time-vs-run-time-voxels"></a>Voxels au moment du baking et au moment de l’exécution
Il est possible de visualiser les voxels dans la fenêtre de l’éditeur au moment de la conception du jeu et dans la fenêtre du jeu au moment de l’exécution. La taille des voxels est différente dans ces deux vues. La raison en est que l’interpolation des effets acoustiques à l’exécution utilise une grille de voxels plus fine, qui permet d’obtenir des résultats d’interpolation plus lisses. L’emplacement des sources sonores doit être vérifié avec les voxels au moment de l’exécution.

Voxels au moment de la conception :

![Capture d’écran de voxels Project Acoustics au moment de la conception](media/voxels-design-time.png)

Voxels de runtime :

![Capture d’écran de voxels Project Acoustics au moment de l’exécution](media/voxels-runtime.png)

## <a name="next-steps"></a>Étapes suivantes
* Explorer des études de cas mettant en évidence les concepts qui sous-tendent le [processus de conception](design-process.md)

