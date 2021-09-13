---
title: Bonnes pratiques relatives aux SKU de machines virtuelles Azure pour Azure NetApp Files | Microsoft Docs
description: Décrit les bonnes pratiques Azure NetApp Files concernant les références SKU de machines virtuelles Azure, y compris les différences entre et au sein des références SKU.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: b-juche
ms.openlocfilehash: 6644a52ee094ce0076293b37fa79db28568c9c67
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113233462"
---
# <a name="azure-virtual-machine-skus-best-practices-for-azure-netapp-files"></a>Bonnes pratiques relatives aux SKU de machines virtuelles Azure pour Azure NetApp Files

Cet article décrit les bonnes pratiques Azure NetApp Files concernant les références SKU de machines virtuelles Azure, y compris les différences entre et au sein des références SKU.   

## <a name="sku-selection-considerations"></a>Éléments à prendre en considération pour la sélection des références SKU

Les performances de stockage impliquent plus que la vitesse de stockage elle-même. La vitesse et l’architecture du processeur comptent pour beaucoup dans l’expérience globale à partir d’un nœud de calcul particulier. Dans le cadre du processus de sélection d’une référence SKU donnée, vous devez prendre en compte les facteurs suivants :

* AMD ou Intel : par exemple, SAS utilise une bibliothèque Math Kernel Library conçue spécifiquement pour les processeurs Intel.  Dans ce cas, les références SKU Intel sont préférées à la référence SKU AMD.
* Les types de machine F2, E_v3 et D_v3 sont chacun basés sur plusieurs circuits microprogrammés.  Dans le cadre de l’utilisation d’hôtes dédiés Azure, vous pouvez sélectionner des modèles spécifiques (Broadwell, Cascade Lake ou Skylake lors de la sélection du type E par exemple). Dans le cas contraire, la sélection du circuit microprogrammé est non déterministe.  Si vous déployez un cluster HPC et privilégiez une expérience cohérente dans l’inventaire, vous pouvez envisager d’utiliser des hôtes dédiés Azure uniques ou d’utiliser des références SKU à un seul circuit microprogrammé telles que E_v4 ou D_v4.
* La variabilité des performances avec le stockage NAS (Network-Attached Storage) a été observée lors des tests avec les références SKU Intel Broadwell et les références SKU AMD EPYC™ 7551. Deux problèmes ont été observés :
    * Lorsque l’interface réseau accélérée est mappée de façon inappropriée à un nœud NUMA non optimal, les performances de lecture diminuent considérablement.   Bien que le mappage de l’interface de l’accélération réseau à un nœud NUMA spécifique soit bénéfique sur les références SKU plus récentes, il doit être considéré comme indispensable sur les références SKU avec ces circuits microprogrammés (Lv2|E_v3|D_v3).
    * Les machines virtuelles s’exécutant sur le Lv2, ou E_v3 ou D_v3 s’exécutant sur un circuit microprogrammé Broadwell sont plus exposées aux conflits de ressources que lorsqu’elles s’exécutent sur d’autres références SKU.  Lorsque vous testez avec plusieurs machines virtuelles qui s’exécutent sur un seul hôte dédié Azure, l’exécution de la charge de travail de stockage basée sur le réseau à partir d’une machine virtuelle peut réduire les performances des charges de travail de stockage basées sur le réseau exécutées à partir d’une deuxième machine virtuelle. La diminution est plus prononcée quand l’une des machines virtuelles sur le nœud dispose d’un nœud NUMA/d’interface d’accélération réseau qui n’a pas été mappé de manière optimale.  Gardez à l’esprit que les types E_v3 et D_V3 peuvent se retrouver sur Haswell, Broadwell, Cascade Lake ou Skylake. 

Pour obtenir des performances optimales lors de la sélection de machines virtuelles, sélectionnez des références SKU avec un seul type de circuit microprogrammé : les références SKU plus récentes sont préférées aux modèles plus anciens, le cas échéant.  Hormis en utilisant un hôte dédié, notez qu’il est peu probable de prédire correctement sur quel type de matériel les types E_v3 et D_v3 se retrouveront.  En utilisant la référence SKU E_v3 ou D_v3 :

* Quand une machine virtuelle est désactivée, désallouée, puis réactivée, la machine virtuelle est susceptible de changer les hôtes et ainsi les modèles matériels.
* Lorsque des applications sont déployées sur plusieurs machines virtuelles, attendez-vous à ce que les machines virtuelles s’exécutent sur du matériel hétérogène.

## <a name="differences-within-and-between-skus"></a>Différences entre et au sein des références SKU
 
Le tableau suivant met en évidence les différences entre et au sein des références SKU.  Notez, par exemple, que le circuit microprogrammé des types E_v3 et D_v3 sous-jacents varie entre Broadwell, Cascade Lake et Skylake, ainsi que dans le cas du type D_v3.  

|     Famille    |     Version    |   Description     |     Fréquence (GHz)    |
|-|-|-|-|
|     E    |     V3    |     Intel® Xeon® E5-2673   v4 (Broadwell)    |     2,3 (3,6)    |
|     E    |     V3    |     Intel® Xeon®   Platinum 8272CL (Cascade Lake)    |     2,6 (3,7)    |
|     E    |     V3    |     Intel® Xeon® Platinum   8171M (Skylake)    |     2,1 (3,8)    |
|     E    |     V4    |     Intel® Xeon®   Platinum 8272CL (Cascade Lake)    |     2,6 (3,7)    |
|     Ea    |     V4    |     AMD EPYC™ 7452    |     2,35 (3,35)    |
|     D    |     V3    |     Intel® Xeon®   E5-2673 v4 (Broadwell)    |     2,3 (3,6)    |
|     D    |     V3    |     Intel® Xeon® E5-2673   v3 (Haswell)    |     2,3 (2,3)    |
|     D    |     V3    |     Intel® Xeon®   Platinum 8272CL (Cascade Lake)    |     2,6 (3,7)    |
|     D    |     V3    |     Intel® Xeon® Platinum   8171M (Skylake)    |     2,1 (3,8)    |
|     D    |     V4    |     Intel® Xeon® Platinum   8272CL (Cascade Lake)    |     2,6 (3,7)    |
|     Da    |     V4    |     AMD EPYC™ 7452    |     2,35 (3,35)    |
|     L    |     V2    |     AMD EPYC™   7551    |     2,0 (3,2)    |
|     F    |     1    |     Intel Xeon® E5-2673 v3 (Haswell)     |     2,3 (2,3)    |
|     F    |     2    |     Intel® Xeon®   Platinum 8168M (Cascade Lake)    |     2,7 (3,7)    |
|     F    |     2    |     Gen 2 Intel® Xeon® Platinum 8272CL (Skylake)    |     2,1 (3,8)   |

Lors de la préparation d’un environnement SAS GRID multinœud pour la production, vous remarquerez peut-être une variance d’une heure et quinze minutes renouvelable entre les exécutions d’analyse sans autre différence que le matériel sous-jacent.  

|     Référence SKU et plateforme matérielle    |     Durée d’exécution des tâches    |
|-|-|
|     E32-8_v3 (Broadwell)    |     5 heures et demi    |
|     E32-8_v3 (Cascade   Lake)    |     4,25 heures    |

Dans les deux jeux de tests, une référence SKU E32-8_v3 a été sélectionnée et RHEL 8.3 a été utilisé avec l’option de montage `nconnect=8`.

## <a name="best-practices"></a>Meilleures pratiques 

* Dans la mesure du possible, sélectionnez la référence SKU E_v4, D_v4 ou plus récente plutôt que les références SKU E_v3 ou D_v3.  
* Dans la mesure du possible, sélectionnez la référence SKU Ed_v4, Dd_v4 ou plus récente plutôt que la référence SKU L2.

## <a name="next-steps"></a>Étapes suivantes  

* [Bonnes pratiques d’E/S directes Linux pour Azure NetApp Files](performance-linux-direct-io.md)
* [Bonnes pratiques en matière de cache du système de fichiers Linux pour Azure NetApp Files](performance-linux-filesystem-cache.md)
* [Meilleures pratiques Linux concernant les options de montage NFS pour Azure NetApp Files](performance-linux-mount-options.md)
* [Bonnes pratiques pour la concurrence Linux](performance-linux-concurrency-session-slots.md)
* [Bonnes pratiques pour la lecture anticipée NFS Linux](performance-linux-nfs-read-ahead.md)
* [Test d’évaluation des performances pour Linux](performance-benchmarks-linux.md) 
