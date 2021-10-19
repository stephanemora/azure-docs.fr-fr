---
title: Mise hors service de la série HB
description: La mise hors service de la série HB a commencé le 1er septembre 2021.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: f582aad0b5af8053ff7accfac2917d4d5191ce5d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712144"
---
# <a name="migrate-your-hb-series-virtual-machines-by-august-31-2024"></a>Migrer vos machines virtuelles de la série HB jusqu’au 31 août 2024

Microsoft Azure a introduit des machines virtuelles des séries HBv2 et HBv3 pour le calcul haute performance (HPC). Pour cette raison, nous vous recommandons de migrer les charges de travail des machines virtuelles de la série HB d’origine vers nos offres plus récentes.

Les machines virtuelles Azure [HBv2](hbv2-series.md) et [HBv3](hbv3-series.md) ont une plus bande passante de mémoire supérieure, des fonctionnalités de mise en réseau à accès direct à la mémoire à distance (RDMA) améliorées, des disques SSD locaux plus grands et plus rapides et un meilleur rapport coût/performance sur différentes charges de travail HPC. Par conséquent, nous retirons nos tailles de machines virtuelles Azure de la série HB le 31 août 2024.

## <a name="how-does-the-hb-series-migration-affect-me"></a>En quoi la migration de la série HB m’impacte-t-elle ?

Après le 31 août 2024, tous les abonnements de machines virtuelles HB restants seront définis sur un état libéré. Ils cesseront de fonctionner et n’entraîneront plus de frais de facturation.

> [!NOTE]
> La mise hors service de la taille de machine virtuelle VM n’affecte que les tailles de machine virtuelle dans la série HB. Cette annonce de retrait ne s’applique pas aux machines virtuelles de la série HBv2, HBv3 et HC les plus récentes.

## <a name="what-actions-should-i-take"></a>Que dois-je faire ?

Vous devrez redimensionner ou libérer vos machines virtuelles de la série H. Nous vous recommandons de migrer les charges de travail des machines virtuelles de la série H et H Promo d’origine vers nos offres plus récentes.

Les machines virtuelles [HBv2](hbv2-series.md) et [HBv3](hbv3-series.md) offrent un meilleur rapport coût/performance pour les charges de travail HPC en raison des éléments suivants :

- Améliorations importantes apportées à l’architecture au cœur du processeur.
- Plus de bande passante de mémoire.
- Caches L3 plus volumineux.
- Mise en réseau InfiniBand améliorée par rapport à la série HB.

Par conséquent, les séries HBv2 et HBv3 offriront en général de meilleures performances par unité de coût (c’est-à-dire l’optimisation des performances pour un montant fixe de dépense), ainsi que le coût par performance (c’est-à-dire la réduction du coût pour un montant fixe de performances).

Toutes les régions qui contiennent des machines virtuelles de la série HB incluent des machines virtuelles des séries HBv2 et HBv3. Les charges de travail existantes exécutées sur des machines virtuelles de la série HB peuvent être migrées sans se préoccuper de leur emplacement géographique ou de l'accès à davantage de services dans ces régions.

Les machines virtuelles de la [série HB](hb-series.md) ne seront pas retirées avant septembre 2024. Nous fournissons ce guide à l’avance afin de vous donner un meilleur délai pour évaluer, planifier et exécuter votre migration.

### <a name="recommendations-for-workload-migration-from-hb-series-vms"></a>Recommandations pour la Migration de la charge de travail à partir des Machines virtuelles de la série HB

| Taille actuelle de machine virtuelle | Taille de machine virtuelle cible | Différence de spécification  |
|---|---|---|
|Standard_HB60rs |Standard_HB120rs_v2 <br> Standard_HB120rs_v3 <br> Standard_HB120-64rs_v3 |PROCESSEUR plus récent : AMD Rome et MiIan (+ 20-30 % IPC) <br> Mémoire : jusqu’à 2 fois plus de RAM  <br> Bande passante de la mémoire : jusqu’à 30 % de bande passante de mémoire supplémentaire <br> InfiniBand : 200 Go en HDR (bande passante deux fois supérieure) <br> Disques de données max. : jusqu’à 32 (+ 8x) |
|Standard_HB60-45rs |Standard_HB120-96rs_v3 <br> Standard_HB120-64rs_v3 <br> Standard_HB120-32rs_v3 |PROCESSEUR plus récent : AMD Rome et MiIan (+ 20-30 % IPC) <br> Mémoire : jusqu’à 2 fois plus de RAM  <br>  Bande passante de la mémoire : jusqu’à 30 % de bande passante de mémoire supplémentaire <br> InfiniBand : 200 Go en HDR (bande passante deux fois supérieure) <br> Disques de données max. : jusqu’à 32 (+ 8x) |
|Standard_HB60-30rs |Standard_HB120-32rs_v3 <br> Standard_HB120-16rs_v3 |PROCESSEUR plus récent : AMD Rome et MiIan (+ 20-30 % IPC) <br> Mémoire : jusqu’à 2 fois plus de RAM <br> Bande passante de la mémoire : jusqu’à 30 % de bande passante de mémoire supplémentaire <br> InfiniBand : 200 Go en HDR (bande passante deux fois supérieure) <br> Disques de données max. : jusqu’à 32 (+ 8x) |
|Standard_HB60-15rs |Standard_HB120-16rs_v3 |PROCESSEUR plus récent : AMD Rome et MiIan (+ 20-30 % IPC) <br> Mémoire : jusqu’à 2 fois plus de RAM <br> Bande passante de la mémoire : jusqu’à 30 % de bande passante de mémoire supplémentaire <br> InfiniBand : 200 Go en HDR (bande passante deux fois supérieure) <br> Disques de données max. : jusqu’à 32 (+ 8x) |

### <a name="migration-steps"></a>Étapes de la migration

1. Choisissez une série et une taille pour la migration.
1. Obtenez le quota de la série de machines virtuelles cible.
1. Redimensionner la taille actuelle de machine virtuelle de la série HB sur la taille cible.

### <a name="get-a-quota-for-the-target-vm-family"></a>Obtenir un quota pour la famille de machines virtuelles cible

Suivez ce guide pour [demander une augmentation du quota de processeurs virtuels par famille de machines virtuelles](../azure-portal/supportability/per-vm-quota-requests.md).

### <a name="resize-the-current-vm"></a>Redimensionner la machine virtuelle actuelle

Vous pouvez [redimensionner la machine virtuelle](resize-vm.md).
