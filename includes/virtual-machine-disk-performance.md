---
title: Fichier include
description: Fichier include
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 9f5a1010959658e75dcc809b2ee1d6d9222af056
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91539995"
---
Cet article vous aide à clarifier les performances des disques et la procédure lorsque vous combinez des machines virtuelles Azure et des disques Azure. Il explique également comment vous pouvez diagnostiquer les goulots d’étranglement des E/S de disque et les modifications que vous pouvez apporter pour optimiser les performances.

## <a name="how-does-disk-performance-work"></a>Fonctionnement des performances des disques
Les machines virtuelles Azure ont des limites de performances d’E/S par seconde et de débit qui dépendent du type et de la taille des machines virtuelles. Les disques de système d’exploitation et les disques de données, qui peuvent être attachés à des machines virtuelles, ont leurs propres limites d’E/S par seconde et de débit. Lorsque votre application s’exécutant sur vos machines virtuelles demande davantage d’E/S par seconde ou de débit que ce qui est alloué pour la machine virtuelle ou les disques attachés, les performances de votre application sont limitées. Dans ce cas, l’application présente des performances non optimales, ce qui peut entraîner des conséquences négatives comme une latence accrue. Passons en revue quelques exemples pour consolider cela. Pour que ces exemples soient faciles à suivre, nous nous pencherons uniquement sur les E/S par seconde, mais la même logique s’applique également au débit.

## <a name="disk-io-capping"></a>Limitation d’E/S disque
Configuration :
- Standard_D8s_v3 
    - E/S par seconde non mises en cache : 12 800
- Disque de système d’exploitation E30
    - IOPS : 500 
- 2 disques de données E30
    - IOPS : 500

![Limitation au niveau du disque](media/vm-disk-performance/disk-level-throttling.jpg)

L’application en cours d’exécution sur la machine virtuelle effectue une requête qui requiert 10 000 E/S par seconde sur la machine virtuelle. Toutes ces opérations sont autorisées par la machine virtuelle, car la machine virtuelle Standard_D8s_v3 peut exécuter jusqu’à 12 800 E/S par seconde. Ces requêtes de 10 000 E/S par seconde sont ensuite divisées en trois requêtes différentes sur les différents disques. 1 000 E/S par seconde sont demandées au disque du système d’exploitation et 4 500 E/S par seconde sont demandées à chaque disque de données. Étant donné que tous les disques attachés sont des disques E30 et peuvent uniquement gérer 500 E/S par seconde, ils répondent avec 500 E/S par seconde chacun. Les performances de l’application sont ensuite limitées par les disques attachés et ne peuvent traiter que 1 500 E/S par seconde. Cela peut fonctionner avec des performances de pointe à 10 000 E/S par seconde si des disques plus performants sont utilisés, comme des disques SSD Premium P30.

## <a name="virtual-machine-io-capping"></a>Limitation des E/S de machine virtuelle
Configuration :
- Standard_D8s_v3 
    - E/S par seconde non mises en cache : 12 800
- Disque de système d’exploitation P30
    - IOPS : 5 000 
- 2 Disques de données P30 
    - IOPS : 5 000

![Limitation au niveau des machines virtuelles](media/vm-disk-performance/vm-level-throttling.jpg)

L’application en cours d’exécution sur la machine virtuelle effectue une requête qui requiert 15 000 E/S par seconde. Malheureusement, la machine virtuelle Standard_D8s_v3 est configurée pour gérer au plus 12 800 E/S par seconde. Ainsi, l’application est limitée par les contraintes de la machine virtuelle et doit ensuite allouer les 12 800 E/S par seconde affectées. Ces requêtes de 12 800 E/S par seconde sont ensuite divisées en trois requêtes différentes sur les différents disques. 4 267 E/S par seconde sont demandées au disque du système d’exploitation et 4 266 E/S par seconde sont demandées à chaque disque de données. Étant donné que tous les disques attachés sont des disques P30 qui peuvent gérer 5 000 E/S par seconde, ils répondent avec leurs montants demandés.