---
title: Projet acoustique Bake résolution
titlesuffix: Azure Cognitive Services
description: Cette vue d’ensemble conceptuelle décrit la différence entre les résolutions approximative et fines tout en intégrant acoustique.
services: cognitive-services
author: KyleStorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 04/05/2019
ms.author: KyleStorck
ms.openlocfilehash: 7dbf63ba39c5dcdebb363cfc37a45f0216a07497
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61335445"
---
# <a name="project-acoustics-bake-resolution"></a>Projet acoustique Bake résolution
Cette vue d’ensemble conceptuelle décrit la différence entre les résolutions approximative et fines tout en intégrant acoustique. Vous choisissez ce paramètre lors de l’étape de sondes du flux de travail cuisson.

## <a name="Coarse-vs-Fine-Resolution"></a>Résolution grossière ou fine

La seule différence entre les paramètres de résolution grossière et fine est la fréquence à laquelle la simulation est effectuée. La résolution fine utilise une fréquence deux fois plus élevée que la résolution grossière. Cela a plusieurs implications sur la simulation acoustique :

* La longueur d’onde pour la résolution grossière est deux fois plus longue que pour la résolution fine. Par conséquent, les voxels sont deux fois plus grands.
* La durée nécessaire à la simulation est directement liée à la taille des voxels. Ainsi, un baking avec une résolution grossière est environ 16 fois plus rapide qu’avec une résolution fine.
* Portails (par exemple, les portes ou windows) inférieures à la taille voxel ne peuvent pas être simulés. Le paramètre grossière peut entraîner certaines de ces portails plus petits ne pas simuler ; Par conséquent, ils ne sont pas transmettre sons à travers lors de l’exécution. Vous pouvez vérifier si cela se produit en affichant les voxels.
* La fréquence de simulation inférieure provoque moins de diffraction autour des angles et des bords.
* Sources audio ne peut pas se trouver à l’intérieur de voxels « rempli » (c'est-à-dire voxels qui contiennent la géométrie). Ainsi, aucun son. Il est plus difficile placer les sources audio afin qu’ils ne soient pas à l’intérieur de la plus grande voxels de grossier plutôt que lors de l’utilisation de la valeur du paramètre fine.
* Les plus grands voxels empiéteront plus sur les ouvertures, comme indiqué ci-dessous. La première image a été créée avec une résolution grossière, tandis que la deuxième est la même ouverture avec la résolution fine. Comme indiqué par les marquages rouges, il y a beaucoup moins d’intrusion dans l’ouverture avec une valeur fine. La ligne bleue est la porte telle que définie par la géométrie, tandis que la ligne rouge est l’ouverture acoustique effective définie par la taille de voxel. L’impact de cet empiètement dans une situation donnée dépend entièrement de l’alignement des voxels avec la géométrie de l’ouverture, qui est déterminé par la taille et les emplacements de vos objets dans la scène.

![Capture d’écran de voxels grossiers remplissant l’embrasure d’une porte dans Unreal](media/unreal-coarse-bake.png)

![Capture d’écran de voxels fins remplissant l’embrasure d’une porte dans Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Étapes suivantes

Tester les paramètres de résolution approximative et fine vous-même à l’aide de notre [Unreal](unreal-baking.md) ou [Unity](unity-baking.md) plug-ins.