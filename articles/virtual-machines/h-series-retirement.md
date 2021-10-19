---
title: Mise hors service de la série H
description: La mise hors service de la série H a commencé le 1er septembre 2021.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: 1ad9a9384d00ec0961235c167379d9984ec417fe
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705239"
---
# <a name="migrate-your-h-and-h-series-promo-virtual-machines-by-august-31-2022"></a>Migrer vos machines virtuelles de la série H et H Promo avant le 31 août 2022

Microsoft Azure a introduit des générations plus récentes de machines virtuelles à calcul hautes performances (HPC), usage général et mémoire optimisée. Pour cette raison, nous vous recommandons de migrer les charges de travail des machines virtuelles de la série H et H Promo d’origine vers nos offres plus récentes.

Les machines virtuelles Azure [HC](hc-series.md), [HBv2](hbv2-series.md), [HBv3](hbv3-series.md), [Dv4](dv4-dsv4-series.md), [Dav4](dav4-dasv4-series.md), [Ev4](ev4-esv4-series.md) et [Eav4](eav4-easv4-series.md) disposent de plus de bande passante mémoire, de meilleures fonctionnalités réseau et d’un meilleur rapport coût/performance sur plusieurs charges de travail HPC. Le 31 août 2022, nous supprimerons les tailles de machine virtuelle Azure de la série H suivantes :

- H8
- H8m
- H16
- H16r
- H16m
- H16mr
- H8 Promo
- H8m Promo
- H16 Promo
- H16r Promo
- H16m Promo
- H16mr Promo

## <a name="how-does-the-h-series-migration-affect-me"></a>En quoi la migration de la série H me concerne-t-elle ?

Après le 31 août 2022, tous les abonnements restants aux machines virtuelles de la série H de la liste précédente seront définis sur un état libéré. Elles cesseront de fonctionner et n’entraîneront plus de frais de facturation.

La mise hors service de la taille de machine virtuelle actuelle n’affecte que les tailles de machine virtuelle dans la [série H](h-series.md), y compris la série H Promo.

## <a name="what-actions-should-i-take"></a>Que dois-je faire ?

Vous devrez redimensionner ou libérer vos machines virtuelles de la série H. Nous vous recommandons de migrer les charges de travail des machines virtuelles de la série H et H Promo d’origine vers nos offres plus récentes.

**Charges de travail HPC :** les machines virtuelles [HC](hc-series.md), [HBv2](hbv2-series.md) et [HBv3](hbv3-series.md) offrent un meilleur rapport coût/performance pour les charges de travail HPC en raison des éléments suivants :

- Améliorations importantes apportées à l’architecture au cœur du processeur.
- Plus de bande passante de mémoire.
- Caches L3 plus volumineux.
- Prise en charge améliorée du matériel et des logiciels de mise en réseau InfiniBand par rapport à la série H.

Par conséquent, les séries HC, HBv2 et HBv3 offriront en général de meilleures performances par unité de coût (c’est-à-dire l’optimisation des performances pour un montant fixe de dépense) et un meilleur coût par performance (c’est-à-dire la réduction du coût pour un montant fixe de performances).

**Charges de travail d’usage général :** les machines virtuelles [Dv4](dv4-dsv4-series.md), [Dav4](dav4-dasv4-series.md) et Dv5 offrent des performances de processeur identiques ou supérieures à des nombres de cœurs identiques ou supérieurs, une quantité comparable de mémoire par cœur de processeur physique, de meilleures fonctionnalités de mise en réseau Azure et des coûts globaux réduits.

**Charges de travail à mémoire optimisée :** les machines virtuelles [Ev4](ev4-esv4-series.md), [Eav4](eav4-easv4-series.md) et Ev5 offrent des performances de processeur identiques ou supérieures à des nombres de cœurs identiques ou supérieurs, une quantité comparable de mémoire par cœur de processeur physique, de meilleures fonctionnalités de mise en réseau Azure et des coûts globaux réduits.

Les machines virtuelles de la [série H](h-series.md) et H Promo ne seront pas mises hors service avant septembre 2022. Nous fournissons ce guide à l’avance afin de vous donner un meilleur délai pour évaluer, planifier et exécuter votre migration.

### <a name="migration-steps"></a>Étapes de la migration

1. Choisissez une série et une taille pour la migration.
1. Obtenez le quota de la série de machines virtuelles cible.
1. Redimensionnez la taille actuelle de machine virtuelle de la série H sur la taille cible.

### <a name="breaking-changes"></a>Changements cassants

Si vous utilisez des machines virtuelles de la série H qui exposent une interface de mise en réseau InfiniBand (par exemple, celles avec un « r » dans le nom de la taille de machine virtuelle) et que vous souhaitez que vos nouvelles tailles de machines virtuelles prennent également en charge la mise en réseau InfiniBand, vous ne pourrez plus utiliser d’images de système d’exploitation héritées avec prise de pilote InfiniBand (CentOS 7.4 et versions antérieures, Windows Server 2012).

Utilisez plutôt des images modernes du système d’exploitation, telles que celles disponibles sur la Place de marché Azure qui prennent en charge les systèmes d’exploitation modernes (CentOS 7,5 et versions ultérieures, Windows Server 2016 et versions ultérieures) et les pilotes OFED standard. Consultez la [pile logicielle prise en charge](hbv3-series.md#get-started), qui inclut le système d’exploitation pris en charge pour les tailles de machine virtuelle respectives.

### <a name="get-a-quota-for-the-target-vm-family"></a>Obtenir un quota pour la famille de machines virtuelles cible

Suivez ce guide pour [demander une augmentation du quota de processeurs virtuels par famille de machines virtuelles](../azure-portal/supportability/per-vm-quota-requests.md).

### <a name="resize-the-current-vm"></a>Redimensionner la machine virtuelle actuelle

Vous pouvez [redimensionner la machine virtuelle](resize-vm.md).
