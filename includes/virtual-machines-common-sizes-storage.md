---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 961f82cd4970abfdd11a30b2847a14f8ff1880b0
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454553"
---
Les tailles de machine virtuelle à stockage optimisé offrent un débit et des E/S de disque élevés, et sont idéales pour les bases de données Big Data, SQL et NoSQL. Cet article fournit des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau ainsi que sur la bande passante réseau et le débit de stockage pour chaque taille de ce regroupement. 

La série Ls offre jusqu’à 32 processeurs virtuels, grâce à la [Famille de processeurs Intel® Xeon® E5 v3](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Cette série propose les mêmes performances de processeur que celles de la série G/GS, associées à 8 Gio de mémoire par processeur virtuel.  Les machines virtuelles de la série Ls conviennent parfaitement pour les applications nécessitant une faible latence, un débit élevé et un stockage important sur disque local. 

Voici des exemples de cas d’usage : bases de données NoSQL (comme Cassandra), MongoDB, Cloudera, Redis, entreposage de données et bases de données transactionnelles volumineuses.

> [!NOTE]
> La série Ls est optimisée pour utiliser le disque temporaire attaché à la machine virtuelle, plutôt que d’utiliser des disques de données durables. Grâce au débit élevé et à l’IOPS du disque temporaire, la série Ls est parfaite pour les magasins NoSQL (tels que Apache Cassandra et MongoDB), qui répliquent les données sur plusieurs machines virtuelles pour garantir la persistance en cas de défaillance d’une machine virtuelle. La série Ls ne prend pas en charge la création d’un cache local pour augmenter l’IOPS de façon à atteindre les performances des disques de données durables.

## <a name="ls-series"></a>Série Ls

ACU : 180-240

Stockage Premium : pris en charge

Mise en cache du Stockage Premium : non pris en charge
 
| Taille          | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire max : E/S par seconde / Mbits/s | Débit de disque maximal sans mise en cache : E/S / MBps | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4    | 32   | 678   | 16    | 20,000 / 200   | 5 000 / 125        | 2 / 4,000  | 
| Standard_L8s   | 8    | 64   | 1,388 | 32   | 40,000 / 400   | 10 000 / 250       | 4 / 8,000  | 
| Standard_L16s  | 16   | 128  | 2,807 | 64   | 80 000 / 800   | 20 000 / 500       | 8 / 16 000 | 
| Standard_L32s <sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40 000 / 1 000     | 8 / 20,000 | 
 

Le débit de disque maximal possible avec des machines virtuelles de la série Ls peut être limité par le nombre, la taille et la répartition des disques attachés. Pour plus d’informations, consultez l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../articles/virtual-machines/windows/premium-storage.md).

<sup>1</sup> L’instance est isolée sur un matériel dédié à un client unique.

## <a name="size-table-definitions"></a>Définitions des tailles de tables

- La capacité de stockage est indiquée en unités de Gio ou 1 024^3 octets. Lors de la comparaison de disques mesurés en Go (1 000^3 octets) à des disques mesurés en Gio (1 024^3) n’oubliez pas que les indications de capacité en Gio peuvent sembler plus petites. Par exemple, 1 023 Gio = 1 098,4 Go
- Le débit de disque est mesuré en opérations d’entrée/sortie par seconde (IOPS) et MBps où MBps = 10^6 octets par seconde.
- Si vous souhaitez obtenir des performances optimales pour vos machines virtuelles, vous devez limiter le nombre de disques de données à 2 disques par processeur virtuel.
- La **bande passante réseau attendue** est la [bande passante agrégée maximale qui est allouée par type de machine virtuelle](../articles/virtual-network/virtual-machine-network-throughput.md) entre toutes les cartes réseau, pour toutes les destinations. Les limites supérieures ne sont pas garanties, mais servent de points de repère pour sélectionner le type de machine virtuelle adapté à l’application prévue. Les performances réseau réelles dépendent de nombreux facteurs, notamment la congestion du réseau, les charges de l’application, ainsi que les paramètres réseau. Pour plus d’informations sur l’optimisation du débit du réseau, consultez [Optimisation du débit du réseau pour Windows et Linux](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Pour atteindre la performance réseau attendue sous Linux ou Windows, il peut être nécessaire de sélectionner une version spécifique ou d’optimiser votre machine virtuelle. Pour plus d’informations, consultez [Tester de manière fiable le débit d’une machine virtuelle](../articles/virtual-network/virtual-network-bandwidth-testing.md).
