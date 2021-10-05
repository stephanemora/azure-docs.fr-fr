---
title: Mise hors service de la série NV
description: Mise hors service de la série NV à partir du 1er septembre 2021
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: 666b681d33c41482bcf33947c38be33aeee29783
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436443"
---
# <a name="migrate-your-nv-and-nv_promo-series-virtual-machines-by-august-31-2022"></a>Migrer vos machines virtuelles de la série NV et NV_Promo le 31 août 2022

Nous continuons à apporter des instances de machines virtuelles (VM) modernes et optimisées à Azure en utilisant les dernières innovations en matière de technologies de centres de données. Tout en innovant, nous planifions également de manière réfléchie la mise au rebut de notre matériel vieillissant. C'est dans ce contexte que nous retirons nos tailles de VM Azure de la série NV le 1er septembre 2022.

## <a name="how-does-the-nv-series-migration-affect-me"></a>En quoi la migration de la série NV m’impacte-t-elle ?

Après le 1er septembre 12022, les machines virtuelles de taille NV et NV_Promo restantes de votre abonnement seront définies sur un état désalloué. Ces machines virtuelles seront arrêtées et supprimées de l’hôte. Ces machines virtuelles ne seront plus facturées dans l’état désalloué. 

La mise hors service de la taille de machine virtuelle actuelle n’affecte que les tailles de machine virtuelle dans la [série NV](nv-series.md). Ce retrait n’affecte pas les machines virtuelles de la série [NVv3](nvv3-series.md) et [NVv4](nvv4-series.md) . 

## <a name="what-actions-should-i-take"></a>Que dois-je faire ?

Vous devez redimensionner ou libérer vos machines virtuelles NV. Nous vous recommandons de déplacer vos visualisations GPU ou vos charges de travail graphiques vers une autre [taille de VM accélérée par le GPU](sizes-gpu.md).

[En savoir plus](nv-series-migration-guide.md) sur la migration de vos charges de travail vers d’autres tailles de Machines virtuelles Azure GPU. 

Si vous avez d’autres questions, contactez-nous par le biais du service client.
