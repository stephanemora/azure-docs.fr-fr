---
title: Tailles des machines virtuelles Azure – Mémoire | Microsoft Docs
description: Répertorie les différentes tailles de mémoire optimisée disponibles pour les machines virtuelles dans Azure. Répertorie des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau, ainsi que sur le débit de stockage et la bande passante réseau pour les tailles disponibles dans cette série.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: Isolation de machine virtuelle, machine virtuelle isolée, isolation, isolée
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 26a44cdf3e57508cebb070669a8c1f473a59df8a
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492542"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Tailles de machine virtuelle à mémoire optimisée

Les tailles de machine virtuelle à mémoire optimisée offrent un ration mémoire/processeur supérieur pour les serveurs de base de données relationnelle, les caches moyens à grands et l’analytique en mémoire. Cet article fournit des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau ainsi que sur la bande passante réseau et le débit de stockage pour chaque taille de ce regroupement.

- Les [séries Dv2 et DSv2](dv2-dsv2-series-memory.md), suites de la série D d’origine, comprennent un UC plus puissant. La série Dv2 est environ 35 % plus rapide que la série D. Elle s’exécute sur les processeurs Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) avec la technologie Intel Turbo Boost 2.0. La série Dv2 a les mêmes configurations de disque et de mémoire que la série D.

    Les séries Dv2 et DSv2 sont idéales pour les applications qui exigent des processeurs virtuels plus rapides, de meilleures performances de stockage temporaire, ou qui ont des exigences de mémoire plus élevées. Elles offrent une combinaison puissante pour de nombreuses applications professionnelles.

- Les [séries Eav4 et Easv4](eav4-easv4-series.md) utilisent le processeur EPYC<sup>TM</sup> 7452 2,35 GHz d’AMD dans une configuration multithread avec un cache L3 jusqu’à 256 Mo, ce qui améliore les options d’exécution de la plupart des charges de travail à mémoire optimisée. Les séries Eav4 et Easv4 ont les mêmes configurations de mémoire et de disque que les séries Ev3 et Esv3.

- Les [séries Ev3 et Esv3](ev3-esv3-series.md) disposent du processeur Intel® Xeon® 8171M 2,1 GHz (Skylake) ou Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) dans une configuration hyperthread, ce qui leur permet d’offrir ce qui se fait de mieux pour la plupart des charges de travail à usage général et d’aligner la série Ev3 sur les machines virtuelles à usage général de la plupart des autres clouds. La mémoire a été étendue (de 7 Gio/processeur virtuel à 8 Gio/processeur virtuel) et les limites de disque et de réseau ont été ajustées au niveau du cœur pour s’aligner sur la transition vers l’hyperthreading. La série Ev3 constitue la suite des tailles de machines virtuelles à mémoire haute des familles D/Dv2.

- La [série M](m-series.md) propose un nombre élevé de processeurs virtuels (jusqu’à 128 processeurs virtuels) et une grande quantité de mémoire (jusqu’à 3,8 Tio). Elle est également idéale pour les très grandes bases de données ou d’autres applications qui bénéficient d’un nombre élevé de processeurs virtuels et de grandes quantités de mémoire.

- La [série Mv2](mv2-series.md) offre le nombre de processeurs virtuels le plus élevé (jusqu’à 416 processeurs virtuels) et la plus grande mémoire (jusqu’à 8,19 Tio) parmi toutes les machines virtuelles dans le cloud. Elle est idéale pour les très grandes bases de données ou d’autres applications qui bénéficient d’un nombre élevé de processeurs virtuels et de grandes quantités de mémoire.

Le Calcul Azure propose des tailles de machines virtuelles qui sont isolées pour un type de matériel spécifique et dédiées à un seul et même client. Ces tailles de machines virtuelles conviennent mieux aux charges de travail qui nécessitent un niveau élevé d’isolation par rapport aux autres clients pour les charges de travail qui impliquent des éléments tels que les exigences réglementaires et de conformité. Les clients peuvent également choisir de subdiviser les ressources de ces machines virtuelles isolées à l’aide de la [prise en charge d’Azure pour les machines virtuelles imbriquées](https://azure.microsoft.com/blog/nested-virtualization-in-azure/). Consultez les pages des familles de machines virtuelles ci-dessous pour connaître les options de machine virtuelle isolée.

## <a name="other-sizes"></a>Autres tailles

- [Usage général](sizes-general.md)
- [Optimisé pour le calcul](sizes-compute.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.