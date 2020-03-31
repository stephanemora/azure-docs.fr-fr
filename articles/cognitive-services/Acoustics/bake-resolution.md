---
title: Résolution de bake Project Acoustics
titlesuffix: Azure Cognitive Services
description: Cette vue d’ensemble conceptuelle décrit la différence entre les résolutions grossières et fines tout en effectuant des bakes acoustiques.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d8eb3b2cbaf7b4e842d8338eefde756f6d381111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854351"
---
# <a name="project-acoustics-bake-resolution"></a>Résolution de bake Project Acoustics
Cette vue d’ensemble conceptuelle décrit la différence entre les résolutions grossières et fines tout en effectuant des bakes acoustiques. Vous choisissez ce paramètre lors de l’étape Sondes du flux de travail de bake.

## <a name="coarse-vs-fine-resolution"></a><a name="Coarse-vs-Fine-Resolution"></a>Résolution grossière ou fine

La seule différence entre les paramètres de résolution grossière et fine est la fréquence à laquelle la simulation est effectuée. La résolution fine utilise une fréquence deux fois plus élevée que la résolution grossière. Cela a plusieurs implications sur la simulation acoustique :

* La longueur d’onde pour la résolution grossière est deux fois plus longue que pour la résolution fine. Par conséquent, les voxels sont deux fois plus grands.
* La durée nécessaire à la simulation est directement liée à la taille des voxels. Ainsi, un baking avec une résolution grossière est environ 16 fois plus rapide qu’avec une résolution fine.
* Les ouvertures (par exemple les portes ou les fenêtres) plus petites que la taille de voxel ne peuvent pas être simulées. Le paramètre de résolution grossière peut entraîner la non-simulation de certaines de ces petites ouvertures. Par conséquent, le son ne les traversera pas au moment de l’exécution. Vous pouvez vérifier si cela se produit en affichant les voxels.
* La fréquence de simulation inférieure provoque moins de diffraction autour des angles et des bords.
* Les sources sonores ne peuvent pas se trouver à l’intérieur de voxels « remplis » (c’est-à dire des voxels qui contiennent de la géométrie). De ce fait, aucuns on n’est émis. Il est plus difficile de placer les sources sonores afin qu’elles ne se trouvent pas à l’intérieur des voxels plus grands obtenus avec une résolution grossière que dans le cas où un paramètre de résolution fine est utilisé.
* Les plus grands voxels empiéteront plus sur les ouvertures, comme indiqué ci-dessous. La première image a été créée avec une résolution grossière, tandis que la deuxième est la même ouverture avec la résolution fine. Comme indiqué par les marquages rouges, il y a beaucoup moins d’intrusion dans l’ouverture avec une valeur fine. La ligne bleue est la porte telle que définie par la géométrie, tandis que la ligne rouge est l’ouverture acoustique effective définie par la taille de voxel. L’impact de cet empiètement dans une situation donnée dépend entièrement de l’alignement des voxels avec la géométrie de l’ouverture, qui est déterminé par la taille et les emplacements de vos objets dans la scène.

![Capture d’écran de voxels grossiers remplissant l’embrasure d’une porte dans Unreal](media/unreal-coarse-bake.png)

![Capture d’écran de voxels fins remplissant l’embrasure d’une porte dans Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Étapes suivantes

Tester vous-même les paramètres de résolution grossière et fine à l’aide de nos plug-ins [Unreal](unreal-baking.md) ou [Unity](unity-baking.md).
