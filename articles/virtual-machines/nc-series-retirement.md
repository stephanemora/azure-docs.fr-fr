---
title: Mise hors service de la série NC
description: Mise hors service de la série NC avant le 31 août 2022
author: sherrywangms
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 09/01/2021
ms.author: sherrywang
ms.openlocfilehash: e95c32903f601a883b62a06eb1b1901c15042dbb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700963"
---
# <a name="migrate-your-nc-and-nc_promo-series-virtual-machines-by-august-31-2022"></a>Migrer vos machines virtuelles de la série NV et NV_Promo d’ici le 31 août 2022
Étant donné que nous continuons à intégrer des instances de machines virtuelles modernes et optimisées à Azure à l’aide des dernières innovations en matière de technologies des centres de données, nous avons soigneusement planifié la mise hors service du matériel vieillissant. En tenant compte de cela, nous allons mettre hors service les tailles de machines virtuelles GPU NC (v1), alimentées par des GPU NVIDIA Tesla K80 le 31 août 2022. 

## <a name="how-does-the-nc-series-migration-affect-me"></a>En quoi la migration de la série NC m’impacte-t-elle ?  

Après le 31 août 2022, les machines virtuelles de taille NC restantes de votre abonnement seront définies sur un état désalloué. Ces machines virtuelles seront arrêtées et supprimées de l’hôte. Ces machines virtuelles ne seront plus facturées dans l’État désalloué. 

La mise hors service de la taille de machines virtuelles n’affecte que les tailles de machines virtuelles de la [série NC](nc-series.md). Cela n’affecte en rien les machines virtuelles plus récentes des séries [NCv3](ncv3-series.md), [NC T4 v3](nct4-v3-series.md) et [ND v2](ndv2-series.md). 


## <a name="what-actions-should-i-take"></a>Que dois-je faire ?  
Vous devrez redimensionner ou libérer vos machines virtuelles NC. Nous vous recommandons de déplacer vos charges de travail GPU vers une autre taille de machine virtuelle GPU. Apprenez-en davantage sur la migration de vos charges de travail vers une autre [taille de machine virtuelle accélérée par GPU](sizes-gpu.md).

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus](n-series-migration.md) sur la migration de vos charges de travail vers d’autres tailles de Machines virtuelles Azure GPU. 

Si vous avez d’autres questions, contactez-nous par le biais du service client.
