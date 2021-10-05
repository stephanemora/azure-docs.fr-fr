---
title: Mise hors service de la série ND
description: Mise hors service de la série ND avant le 31 août 2022
author: sherrywangms
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 09/01/2021
ms.author: sherrywang
ms.openlocfilehash: 7111af4f275f97ff35e4a0cf9846eb6b68a807f9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700961"
---
# <a name="migrate-your-nd-series-virtual-machines-by-august-31-2022"></a>Migrer vos machines virtuelles de la série ND jusqu’au 31 août 2022
Étant donné que nous continuons à intégrer des instances de machines virtuelles modernes et optimisées à Azure à l’aide des dernières innovations en matière de technologies des centres de données, nous avons planifié intelligemment la mise hors service du matériel vieillissant. En tenant compte de cela, nous avons mis hors service les tailles de machines virtuelles GPU ND, alimentées par des GPU NVIDIA Tesla P40 le 31 août 2022. 

## <a name="how-does-the-nd-series-migration-affect-me"></a>En quoi la migration de la série ND m’impacte-t-elle ?  

Après le 31 août 2022, les machines virtuelles de taille ND restantes de votre abonnement seront définies sur un état libéré. Ces machines virtuelles seront arrêtées et supprimées de l’hôte. Ces machines virtuelles ne seront plus facturées dans l’État désalloué. 

La mise hors service de la taille de machines virtuelles n’affecte que les tailles de machines virtuelles dans la [série ND](nd-series.md). Cela n’affecte en rien les machines virtuelles des séries [NCv3](ncv3-series.md),[NC T4 v3](nct4-v3-series.md) et [ND v2](ndv2-series.md). 

## <a name="what-actions-should-i-take"></a>Que dois-je faire ?  
Vous devrez redimensionner ou libérer vos machines virtuelles NC. Nous vous recommandons de déplacer vos charges de travail GPU vers une autre taille de machine virtuelle GPU. En savoir plus sur la migration de vos charges de travail vers une autre [taille de machine virtuelle accélérée par GPU](sizes-gpu.md).

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus](n-series-migration.md) sur la migration de vos charges de travail vers d’autres tailles de Machines virtuelles Azure GPU. 

Si vous avez d’autres questions, contactez-nous par le biais du service client.
