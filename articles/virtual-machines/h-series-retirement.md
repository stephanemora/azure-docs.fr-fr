---
title: Mise hors service de la série H
description: Mise hors service de la série H à partir du 1er septembre 2021
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: 98465066f79f93777255a3072c472d342b557bdc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600225"
---
# <a name="migrate-your-h-and-h_promo-series-virtual-machines-by-august-31-2022"></a>Migrer vos machines virtuelles de la série H et H_Promo le 31 août 2022
Microsoft Azure ayant introduit des générations plus récentes de machines virtuelles de calcul haute performance (HPC), d’usage général et à mémoire optimisées, nous vous recommandons de migrer les charges de travail des machines virtuelles de la série H d’origine (y compris les promotions de la série H) vers nos offres plus récentes.

Les machines virtuelles Azure [HC](hc-series.md), [HBv2](hbv2-series.md), [HBv3](hbv3-series.md), [Dv4](dv4-dsv4-series.md), [Dav4](dav4-dasv4-series.md), [Ev4](ev4-esv4-series.md) et [Eav4](eav4-easv4-series.md) disposent de plus de bande passante mémoire, de meilleures fonctionnalités réseau et d’un meilleur rapport coût/performance sur l’ensemble d’une grande variété de charges de travail HPC. Par conséquent, nous retirons nos tailles de machines virtuelles Azure de la série H (H8, H8m, H16, H16r, H16m, H16mr, H8 promo, H8m promo, H16 promo, H16r promo, H16m promo et H16mr promo) le 31 août 2022.

## <a name="how-does-the-h-series-migration-affect-me"></a>En quoi la migration de la série H me concerne-t-elle ?  

Après le 31 août 2022, les abonnements de machines virtuelles de série H restants indiqués ci-dessus seront définis sur un état libéré, cesseront de fonctionner et n’entraîneront plus de frais de facturation. 

La mise hors service de la taille de machine virtuelle actuelle n’affecte que les tailles de machine virtuelle dans la [série H](h-series.md), y compris les promotions de la série H. 

## <a name="what-actions-should-i-take"></a>Que dois-je faire ?  

Vous devrez redimensionner ou désallouer vos machines virtuelles de la série H. Nous vous recommandons de migrer les charges de travail des machines virtuelles de la série H d’origine (y compris les promotions de la série H) vers nos offres plus récentes.

Les machines virtuelles [HC](hc-series.md), [HBv2](hbv2-series.md) et [HBv3](hbv3-series.md) offrent des niveaux de performances de charge de travail HPC et une rentabilité substantiellement supérieurs, en raison des améliorations importantes apportées à l’architecture des cœurs du processeur, à une plus grande bande passante, aux caches L3 plus grands et au support logiciel et matériel de la mise en réseau améliorée de la solution InfiniBand par rapport à la série H. Par conséquent, les séries HC, HBv2 et HBv3 offriront en général de meilleures performances par unité de coût (c’est-à-dire l’optimisation des performances pour un montant fixe de dépense), ainsi que le coût par performance (c’est-à-dire la réduction du coût pour un montant fixe de performances). 

Pour les charges de travail d’usage général, les machines virtuelles [Dv4](dv4-dsv4-series.md), [Dav4](dav4-dasv4-series.md) et Dv5 offrent des performances de processeur identiques ou supérieures à des nombres de cœurs identiques ou supérieurs, une quantité comparable de mémoire par cœur de processeur physique, de meilleures fonctionnalités Azure Networking et des coûts globaux réduits. 

Pour les charges de travail à mémoire optimisée, les machines virtuelles [Ev4](ev4-esv4-series.md), [Eav4](eav4-easv4-series.md) et Ev5 offrent des performances de processeur identiques ou supérieures à des nombres de cœurs identiques ou supérieurs, une quantité comparable de mémoire par cœur de processeur physique, de meilleures fonctionnalités Azure Networking et des coûts globaux réduits. 

Les machines virtuelles de la[série H](h-series.md) (y compris les promotions de la série H) ne seront pas retirées avant septembre 2022. Nous fournissons donc ce guide à l’avance pour donner aux clients le temps d’évaluer, planifier et exécuter leur migration. 


### <a name="migration-steps"></a>Étapes de la migration 
1. Choisir une série et une taille pour la migration. 
2. Obtenir le quota pour la série de machines virtuelles cible 
3. Redimensionner la taille actuelle de machine virtuelle de la série H sur la taille cible 


### <a name="breaking-changes"></a>Dernières modifications 
Si vous utilisez des machines virtuelles de la série H qui exposent une interface de mise en réseau InfiniBand (par exemple, celles avec un « r » dans le nom de la taille de machine virtuelle) et que vous souhaitez que vos nouvelles tailles de machines virtuelles prennent également en charge la mise en réseau InfiniBand, vous ne pourrez plus utiliser d’images de système d’exploitation héritées avec prise de pilote InfiniBand (CentOS 7.4 et versions antérieures, Windows Server 2012). Utilisez plutôt des images modernes du système d’exploitation, telles que celles sur la place de marché Azure qui prennent en charge les systèmes d’exploitation modernes (CentOS 7,5 et versions ultérieures, Windows Server 2016 et versions ultérieures) et les pilotes OFED standard. Consultez la [pile logicielle prise en charge](hbv3-series.md#get-started), y compris le système d’exploitation pris en charge pour les tailles de machine virtuelle respectives. 


### <a name="get-quota-for-the-target-vm-family"></a>Obtenir le quota pour la famille de machines virtuelles cible 

Suivez ce guide pour [demander une augmentation du quota de processeurs virtuels par famille de machines virtuelles](../azure-portal/supportability/per-vm-quota-requests.md).


### <a name="resize-the-current-virtual-machine"></a>Redimensionner la machine virtuelle actuelle
Vous pouvez [redimensionner la machine virtuelle](resize-vm.md).
