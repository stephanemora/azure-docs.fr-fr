---
title: Mise hors service de la série HB
description: Mise hors service de la série NV à partir du 1er septembre 2021
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: 8a718f315324638f834316eb6dae46a9497ec48e
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122528023"
---
# <a name="migrate-your-hb-series-virtual-machines-by-august-31-2024"></a>Migrer vos machines virtuelles de la série HB jusqu’au 31 août 2024
Comme Microsoft Azure a introduit des machines virtuelles de la série HBv2 et HBv3 pour le calcul haute performance (HPC), nous vous recommandons de migrer les charges de travail des machines virtuelles de la série HB d’origine vers nos offres plus récentes.  

Les machines virtuelles Azure [HBv2](hbv2-series.md) et [HBv3](hbv3-series.md) ont une plus bande passante de mémoire supérieure, des fonctionnalités de mise en réseau à accès direct à la mémoire à distance (RDMA) améliorées, des disques SSD locaux plus grands et plus rapides et un meilleur rapport coût/performance sur une large gamme de charges de travail HPC. Par conséquent, nous retirons nos tailles de machines virtuelles Azure de la série HB le 31 août 2024.

## <a name="how-does-the-hb-series-migration-affect-me"></a>En quoi la migration de la série HB m’impacte-t-elle ?  

Après le 31 août 2024, les abonnements de machines virtuelles de taille HB restants seront définis sur un état désalloué, cesseront de fonctionner et n’entraîneront plus de frais de facturation.  
> [!NOTE]
> La mise hors service de la taille de machine virtuelle VM n’affecte que les tailles de machine virtuelle dans la série HB. Cette annonce de retrait ne s’applique pas aux machines virtuelles de la série HBv2, HBv3 et HC les plus récentes. 

## <a name="what-actions-should-i-take"></a>Que dois-je faire ?  

Vous devrez redimensionner ou désallouer vos machines virtuelles de la série H. Nous vous recommandons de migrer les charges de travail des machines virtuelles de la série H d’origine (y compris les promotions de la série H) vers nos offres plus récentes.

Les machines virtuelles [HBv2](hbv2-series.md) et [HBv3](hbv3-series.md) offrent des niveaux de performances de charge de travail HPC et une rentabilité substantiellement supérieurs, en raison des améliorations importantes apportées à l’architecture de cœur du processeur, à une plus grande bande passante, aux caches L3 plus grands et à la mise en réseau améliorée de la solution InfiniBand par rapport à la série HB. Par conséquent, les séries HBv2 et HBv3 offriront en général de meilleures performances par unité de coût (c’est-à-dire l’optimisation des performances pour un montant fixe de dépense), ainsi que le coût par performance (c’est-à-dire la réduction du coût pour un montant fixe de performances).

Toutes les régions contenant des machines virtuelles de la série HB contiennent des machines virtuelles de série HBv2 et HBv3, de sorte que les charges de travail existantes s’exécutant sur des machines virtuelles de la série HB peuvent être migrées sans se préoccuper de l’emplacement géographique ou de l’accès à des services supplémentaires dans ces régions. 

Les machines virtuelles de la[série HB](hb-series.md) ne seront pas retirées avant septembre 2024. Nous fournissons donc ce guide à l’avance pour donner aux clients le temps d’évaluer, planifier et exécuter leur migration. 

### <a name="recommendations-for-workload-migration-from-hb-series-virtual-machines"></a>Recommandations pour la Migration de la charge de travail à partir des Machines virtuelles de la série HB 

| Taille actuelle de machine virtuelle | Taille cible de machine virtuelle | Différence dans la spécification  |
|---|---|---|
|Standard_HB60rs |Standard_HB120rs_v2 <br> Standard_HB120rs_v3 <br> Standard_HB120-64rs_v3 |PROCESSEUR plus récent : AMD Rome et MiIan (+ 20-30 % IPC) <br> Mémoire : jusqu’à 2 fois plus de RAM  <br> Bande passante de la mémoire : jusqu’à 30 % de bande passante de mémoire supplémentaire <br> InfiniBand : 200 Go en HDR (bande passante deux fois supérieure) <br> Disques de données max. : jusqu’à 32 (+ 8x) |
|Standard_HB60-45rs |Standard_HB120-96rs_v3 <br> Standard_HB120-64rs_v3 <br> Standard_HB120-32rs_v3 |PROCESSEUR plus récent : AMD Rome et MiIan (+ 20-30 % IPC) <br> Mémoire : jusqu’à 2 fois plus de RAM  <br>  Bande passante de la mémoire : jusqu’à 30 % de bande passante de mémoire supplémentaire <br> InfiniBand : 200 Go en HDR (bande passante deux fois supérieure) <br> Disques de données max. : jusqu’à 32 (+ 8x) |
|Standard_HB60-30rs |Standard_HB120-32rs_v3 <br> Standard_HB120-16rs_v3 |PROCESSEUR plus récent : AMD Rome et MiIan (+ 20-30 % IPC) <br> Mémoire : jusqu’à 2 fois plus de RAM <br> Bande passante de la mémoire : jusqu’à 30 % de bande passante de mémoire supplémentaire <br> InfiniBand : 200 Go en HDR (bande passante deux fois supérieure) <br> Disques de données max. : jusqu’à 32 (+ 8x) |
|Standard_HB60-15rs |Standard_HB120-16rs_v3 |PROCESSEUR plus récent : AMD Rome et MiIan (+ 20-30 % IPC) <br> Mémoire : jusqu’à 2 fois plus de RAM <br> Bande passante de la mémoire : jusqu’à 30 % de bande passante de mémoire supplémentaire <br> InfiniBand : 200 Go en HDR (bande passante deux fois supérieure) <br> Disques de données max. : jusqu’à 32 (+ 8x) |


### <a name="migration-steps"></a>Étapes de la migration 
1. Choisir une série et une taille pour la migration. 
2. Obtenir le quota pour la série de machines virtuelles cible 
3. Redimensionner la taille actuelle de machine virtuelle de la série HB sur la taille cible 


### <a name="get-quota-for-the-target-vm-family"></a>Obtenir le quota pour la famille de machines virtuelles cible 

Suivez ce guide pour [demander une augmentation du quota de processeurs virtuels par famille de machines virtuelles](../azure-portal/supportability/per-vm-quota-requests.md).


### <a name="resize-the-current-virtual-machine"></a>Redimensionner la machine virtuelle actuelle
Vous pouvez [redimensionner la machine virtuelle à l’aide de PowerShell](./windows/resize-vm.md), de l’interface [Azure CLI](./linux/change-vm-size.md) ou du portail Azure.
