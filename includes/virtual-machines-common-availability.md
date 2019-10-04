---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/10/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1582f61befb9b6d71adbfda9482175d67874ffb9
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850650"
---
# <a name="availability-options-for-virtual-machines-in-azure"></a>Options de disponibilité pour les machines virtuelles dans Azure
Cet article fournit une vue d’ensemble des fonctionnalités de disponibilité des machines virtuelles Azure.


## <a name="availability-sets"></a>Groupes à haute disponibilité
Un groupe à haute disponibilité est un regroupement logique de machines virtuelles au sein d’un centre de données qui permet à Azure de comprendre comment votre application est conçue, afin de garantir la redondance et la disponibilité. Il est recommandé de créer au moins deux machines virtuelles dans un groupe à haute disponibilité, de manière à fournir une application hautement disponible et à répondre aux exigences du [niveau de 99,95 % inscrit dans les contrats de niveau de service Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Le groupe à haute disponibilité proprement dit ne vous coûte rien ; vous payez uniquement pour chaque instance de machine virtuelle que vous créez. Lorsqu’une seule machine virtuelle utilise des [disques SSD Azure Premium](../articles/virtual-machines/windows/disks-types.md#premium-ssd), le contrat SLA Azure s’applique pour les événements de maintenance non planifiés.

Un groupe à haute disponibilité est composé de deux regroupements supplémentaires qui vous protègent contre les défaillances matérielles et autorisent l’application des mises à jour en toute sécurité dans des domaines d’erreur (FD) et des domaines de mise à jour (UD). Vous pouvez en savoir plus sur la gestion de la disponibilité des [machines virtuelles Linux](../articles/virtual-machines/linux/manage-availability.md) ou des [machines virtuelles Windows](../articles/virtual-machines/windows/manage-availability.md).

### <a name="fault-domains"></a>Domaines d’erreur
Un domaine d’erreur est un groupe logique de matériels sous-jacents qui partagent la même source d’alimentation et le même commutateur réseau, par exemple un rack dans un centre de données local. Lorsque vous créez des machines virtuelles au sein d’un groupe à haute disponibilité, la plateforme Azure distribue automatiquement vos machines virtuelles dans ces domaines d’erreur. Cette approche limite l’impact des défaillances du matériel physique, des pannes de réseau ou des coupures de courant susceptibles de survenir.

### <a name="update-domains"></a>Domaines de mise à jour
Un domaine de mise à jour est un groupe logique de matériels sous-jacents qui peuvent faire l’objet d’une opération de maintenance ou être redémarrés en même temps. Lorsque vous créez des machines virtuelles au sein d’un groupe à haute disponibilité, la plateforme Azure distribue automatiquement vos machines virtuelles dans ces domaines de mise à jour. Cette approche garantit qu’au moins une instance de votre application reste toujours en cours d’exécution, car la plateforme Azure fait l’objet d’une maintenance périodique. Le redémarrage des domaines de mise à jour peut ne pas suivre un ordre séquentiel au cours de la maintenance planifiée, mais un seul domaine de mise à jour peut être redémarré à la fois.

![Groupes à haute disponibilité](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

### <a name="managed-disk-fault-domains"></a>Domaines d’erreur des disques gérés
Les machines virtuelles faisant appel à [Azure Disques managés](../articles/virtual-machines/windows/faq-for-disks.md) sont alignées sur les domaines d’erreur des disques managés lorsqu’un groupe à haute disponibilité géré est utilisé. Cet alignement garantit que tous les disques managés attachés à une machine virtuelle se trouvent dans le même domaine d’erreur de disques managés. Seules des machines virtuelles avec des disques managés peuvent être créées dans un groupe à haute disponibilité géré. Le nombre de domaines d’erreur de disques gérés varie en fonction de la région (deux ou trois par région). Vous pouvez en savoir plus sur ces domaines d’erreur de disque géré pour [les machines virtuelles Linux](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) ou [les machines virtuelles Windows](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

![Groupe à haute disponibilité géré](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

## <a name="availability-zones"></a>Zones de disponibilité

Les [Zones de disponibilité](../articles/availability-zones/az-overview.md) sont une alternative aux groupes à haute disponibilité, et élargissent le niveau de contrôle dont vous disposez pour maintenir la disponibilité des applications et des données sur vos machines virtuelles. Une zone de disponibilité est une zone physiquement séparée au sein d’une région Azure. On distingue trois zones de disponibilité par région Azure prise en charge. Chaque zone de disponibilité possède une source d’alimentation, un réseau et un système de refroidissement propres. En concevant vos solutions pour répliquer des machines virtuelles dans des zones, vous pouvez protéger vos applications et vos données de la perte d’un centre de données. Si une zone est compromise, les applications et données répliquées sont instantanément rendues disponibles dans une autre zone. 

![Zones de disponibilité](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Apprenez-en davantage sur le déploiement d’une machine virtuelle [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) ou [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) dans une zone de disponibilité.


## <a name="next-steps"></a>Étapes suivantes
Vous pouvez maintenant commencer à utiliser ces fonctionnalités de gestion de la redondance et de la disponibilité pour créer votre environnement Azure. Pour en savoir plus sur les meilleures pratiques, consultez la rubrique relative aux [meilleures pratiques Azure en matière de disponibilité](../articles/best-practices-availability-checklist.md).

