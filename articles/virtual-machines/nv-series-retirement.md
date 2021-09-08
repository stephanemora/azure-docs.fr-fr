---
title: Mise hors service de la série NV
description: Mise hors service de la série NV à partir du 1er septembre 2021
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: fcd0d460837195817018882d92b94b0012a14a6f
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122534771"
---
# <a name="migrate-your-nv-and-nv_promo-series-virtual-machines-by-august-31-2022"></a>Migrer vos machines virtuelles de la série NV et NV_Promo le 31 août 2022
Étant donné que nous continuons à intégrer des instances de machines virtuelles modernes et optimisées à Azure à l’aide des dernières innovations en matière de technologies des centres de données, nous avons planifié intelligemment la mise hors service du matériel vieillissant.
En tenant compte de cela, nous mettons nos tailles de Machines virtuelles de la série NV Azure hors service le 01 septembre 2022.

## <a name="how-does-the-nv-series-migration-affect-me"></a>En quoi la migration de la série NV m’impacte-t-elle ?  

Après le 1er septembre 2022, les machines virtuelles de taille NV et NV_Promo restantes de votre abonnement seront définies sur un état désalloué. Ces machines virtuelles seront arrêtées et supprimées de l’hôte. Ces machines virtuelles ne seront plus facturées dans l’État désalloué. 

La mise hors service de la taille de machine virtuelle actuelle n’affecte que les tailles de machine virtuelle dans la [série NV](nv-series.md). Cela n’affecte en rien les machines virtuelles des séries [NVv3](nvv3-series.md) et [NVv4](nvv4-series.md). 

## <a name="what-actions-should-i-take"></a>Que dois-je faire ?  

Vous devrez redimensionner ou désallouer vos machines virtuelles NV. Nous vous recommandons de déplacer vos charges de travail de visualisation/graphique GPU vers une autre [taille de Machine virtuelle accélérée par GPU](sizes-gpu.md).

[En savoir plus](nv-series-migration-guide.md) sur la migration de vos charges de travail vers d’autres tailles de Machines virtuelles Azure GPU. 

Si vous avez d’autres questions, contactez-nous par le biais du service client.
