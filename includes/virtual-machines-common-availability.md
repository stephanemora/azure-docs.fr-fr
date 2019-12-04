---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 06b54c3038e8b4f5879a93b696920534c2199008
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414530"
---
Cet article fournit une vue d’ensemble des fonctionnalités de disponibilité des machines virtuelles Azure.

## <a name="high-availability"></a>Haute disponibilité

Les charges de travail sont généralement réparties sur différentes machines virtuelles afin d’obtenir un débit et des performances élevées, et pour créer une redondance au cas où une machine virtuelle serait affectée en raison d’une mise à jour ou d’un autre événement. 

Azure propose de nombreuses options pour obtenir une haute disponibilité. Commençons par parler des constructions de base. 

### <a name="availability-zones"></a>Zones de disponibilité

Les [zones de disponibilité](../articles/availability-zones/az-overview.md) élargissent le niveau de contrôle dont vous disposez pour maintenir la disponibilité des applications et des données sur vos machines virtuelles. Une zone de disponibilité est une zone physiquement séparée au sein d’une région Azure. On distingue trois zones de disponibilité par région Azure prise en charge. 

Chaque zone de disponibilité possède une source d’alimentation, un réseau et un système de refroidissement propres. En concevant vos solutions pour répliquer des machines virtuelles dans des zones, vous pouvez protéger vos applications et vos données de la perte d’un centre de données. Si une zone est compromise, les applications et données répliquées sont instantanément rendues disponibles dans une autre zone. 

![Zones de disponibilité](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Apprenez-en davantage sur le déploiement d’une machine virtuelle [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) ou [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) dans une zone de disponibilité.


### <a name="fault-domains"></a>Domaines d’erreur

Un domaine d’erreur est un groupe logique de matériels sous-jacents qui partagent la même source d’alimentation et le même commutateur réseau, par exemple un rack dans un centre de données local. 

### <a name="update-domains"></a>Domaines de mise à jour

Un domaine de mise à jour est un groupe logique de matériels sous-jacents qui peuvent faire l’objet d’une opération de maintenance ou être redémarrés en même temps. 

Cette approche garantit qu’au moins une instance de votre application reste toujours en cours d’exécution, car la plateforme Azure fait l’objet d’une maintenance périodique. Le redémarrage des domaines de mise à jour peut ne pas suivre un ordre séquentiel au cours de la maintenance, mais un seul domaine de mise à jour peut être redémarré à la fois.


## <a name="virtual-machines-scale-sets"></a>Groupes de machines virtuelles identiques 

Les groupes identiques de machines virtuelles Azure vous permettent de créer et de gérer un groupe de machines virtuelles et disposant d’une charge équilibrée. Le nombre d’instances de machine virtuelle peut augmenter ou diminuer automatiquement en fonction d’une demande ou d’un calendrier défini. Les groupes identiques offrent une haute disponibilité à vos applications, et vous permettent de gérer, configurer et mettre à jour de manière centralisée de nombreuses machines virtuelles. Il est recommandé de créer au moins deux machines virtuelles dans un groupe identique, de manière à fournir une application hautement disponible et à répondre aux exigences du [niveau de 99,95 % inscrit dans les contrats de niveau de service Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Le groupe identique proprement dit ne vous coûte rien ; vous payez uniquement pour chaque instance de machine virtuelle que vous créez. Lorsqu’une seule machine virtuelle utilise des [disques SSD Azure Premium](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd), le contrat SLA Azure s’applique pour les événements de maintenance non planifiés. Les machines virtuelles d’un groupe identique peuvent être déployées sur plusieurs domaines de mise à jour et domaines d’erreur pour optimiser la disponibilité et la résilience aux pannes dues à des pannes de centre de données ou à des événements de maintenance planifiés ou non. Les machines virtuelles d’un groupe identique peuvent également être déployées dans une seule zone de disponibilité, ou dans une région. Les options de déploiement de la zone de disponibilité peuvent varier en fonction du mode d’orchestration.

### <a name="preview-orchestration-mode-preview"></a>Aperçu : Mode d’orchestration en préversion
Les groupes de machines virtuelles identiques vous permettent de spécifier le mode d’orchestration.  Avec le mode d’orchestration du groupe de machines virtuelles identiques (préversion), vous pouvez maintenant choisir si le groupe identique doit orchestrer des machines virtuelles créées explicitement en dehors d’un modèle de configuration de groupe identique, ou des instances de machine virtuelle créées implicitement sur la base du modèle de configuration. Choisissez le mode d’orchestration pour que le modèle d’orchestration de machine virtuelle vous permette de regrouper des machines virtuelles définies explicitement dans une région ou dans une zone de disponibilité. Les machines virtuelles déployées dans une zone de disponibilité fournissent l’isolation zonale aux machines virtuelles qui sont liées à la limite de la zone de disponibilité et qui ne sont pas exposées à des défaillances qui peuvent se produire dans d’autres zones de disponibilité dans la région. 

|   | “orchestrationMode” : « VM » (VirtualMachine)| “orchestrationMode” : “ScaleSetVM” (VirtualMachineScaleSetVM) |
|----|----|----|
| Modèle de configuration des machines virtuelles| Aucune. VirtualMachineProfile n’est pas défini dans le modèle de groupe identique. | Requis. VirtualMachineProfile est renseigné dans le modèle de groupe identique. |
| Ajout d’une nouvelle machine virtuelle à un groupe identique| Les machines virtuelles sont ajoutées explicitement au groupe identique lors de la création de la machine virtuelle. | Les machines virtuelles sont implicitement créées et ajoutées au groupe identique en fonction du modèle de configuration de machine virtuelle, du nombre d’instances et des règles de mise à l’échelle automatique. |
| Zones de disponibilité| Prend en charge le déploiement régional ou les machines virtuelles dans une zone de disponibilité| Prend en charge le déploiement régional ou plusieurs zones de disponibilité ; peut définir la stratégie d’équilibrage de zone |
| Domaines d’erreur| Peut définir le nombre de domaines d’erreur. 2 ou 3 en fonction du support régional, et 5 pour la zone de disponibilité. Le domaine d’erreur affecté à la machine virtuelle sera conservé tout au long du cycle de vie de la machine virtuelle, notamment lors de la désallocation et du redémarrage. | Vous pouvez définir 1, 2 ou 3 domaines d’erreur pour les déploiements non zonaux, et 5 pour les déploiements de zone de disponibilité. Le domaine d’erreur affecté à la machine virtuelle n’est pas persistant tout au long du cycle de vie ; le domaine d’erreur est affecté aux machines virtuelles au moment de l’allocation. |
| Domaines de mise à jour| N/A. Les domaines de mise à jour sont automatiquement mappés aux domaines d’erreur| N/A. Les domaines de mise à jour sont automatiquement mappés aux domaines d’erreur |

**Domaines d’erreur et domaines de mise à jour**

Les groupes de machines virtuelles identiques simplifient la conception pour la haute disponibilité en alignant les domaines d’erreur et les domaines de mise à jour. Vous avez uniquement à définir le nombre de domaines d’erreur pour le groupe identique. Le nombre de domaines d’erreur disponibles pour les groupes identiques peut varier selon la région. Voir [Nombre de domaines d’erreur par région](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#number-of-fault-domains-per-region).


## <a name="availability-sets"></a>Groupes à haute disponibilité
Un groupe à haute disponibilité est un regroupement logique de machines virtuelles au sein d’un centre de données qui permet à Azure de comprendre comment votre application est conçue, afin de garantir la redondance et la disponibilité. Il est recommandé de créer au moins deux machines virtuelles dans un groupe à haute disponibilité, de manière à fournir une application hautement disponible et à répondre aux exigences du [niveau de 99,95 % inscrit dans les contrats de niveau de service Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Le groupe à haute disponibilité proprement dit ne vous coûte rien ; vous payez uniquement pour chaque instance de machine virtuelle que vous créez. Lorsqu’une seule machine virtuelle utilise des [disques SSD Azure Premium](../articles/virtual-machines/windows/disks-types.md#premium-ssd), le contrat SLA Azure s’applique pour les événements de maintenance non planifiés.

Dans un groupe à haute disponibilité, les machines virtuelles sont automatiquement réparties sur ces domaines d’erreur. Cette approche limite l’impact des défaillances du matériel physique, des pannes de réseau ou des coupures de courant susceptibles de survenir.

Les machines virtuelles faisant appel à [Azure Disques managés](../articles/virtual-machines/windows/faq-for-disks.md) sont alignées sur les domaines d’erreur des disques managés lorsqu’un groupe à haute disponibilité géré est utilisé. Cet alignement garantit que tous les disques managés attachés à une machine virtuelle se trouvent dans le même domaine d’erreur de disques managés. 

Seules des machines virtuelles avec des disques managés peuvent être créées dans un groupe à haute disponibilité géré. Le nombre de domaines d’erreur de disques gérés varie en fonction de la région (deux ou trois par région). Vous pouvez en savoir plus sur ces domaines d’erreur de disque géré pour [les machines virtuelles Linux](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) ou [les machines virtuelles Windows](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

![Groupe à haute disponibilité géré](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


Les machines virtuelles d’un groupe à haute disponibilité sont également automatiquement réparties entre les domaines de mise à jour. 

![Groupes à haute disponibilité](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez maintenant commencer à utiliser ces fonctionnalités de gestion de la redondance et de la disponibilité pour créer votre environnement Azure. Pour en savoir plus sur les meilleures pratiques, consultez la rubrique relative aux [meilleures pratiques Azure en matière de disponibilité](/azure/architecture/checklist/resiliency-per-service).

