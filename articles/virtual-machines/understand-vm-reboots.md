---
title: Comprendre les redémarrages de machine virtuelle
description: Comprendre les redémarrages de machine virtuelle - Maintenance et temps d’arrêt
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: d9d53b4e6b7237c93c6203351463515bb2b34d26
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108125298"
---
# <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Comprendre les redémarrages de machine virtuelle - Maintenance et temps d’arrêt
Il existe trois scénarios pouvant affecter une machine virtuelle dans Azure : maintenance matérielle non planifiée, temps d’arrêt imprévu et maintenance planifiée.

## <a name="unplanned-hardware-maintenance-event"></a>Événement de maintenance matérielle non planifiée
Une maintenance matérielle non planifiée survient quand la plateforme Azure prédit qu’une défaillance du matériel ou d’un composant de la plateforme associé à une machine physique est sur le point de se produire. Lorsque la plateforme prédit une défaillance, elle génère un événement de maintenance matérielle non prévue afin de réduire l’impact sur les machines virtuelles hébergées sur ce matériel. Azure utilise la technologie [Migration dynamique](./maintenance-and-updates.md) pour migrer les machines virtuelles depuis le matériel défectueux vers une machine physique intègre. La migration dynamique est une opération de préservation des machines virtuelles qui interrompt la machine virtuelle pendant une courte période. La mémoire, les fichiers ouverts et les connexions réseau sont conservés, mais les performances peuvent être réduites avant et/ou après l’événement. Dans les cas où la migration dynamique ne peut pas être utilisée, la machine virtuelle subit des temps d’arrêt imprévus, comme décrit ci-dessous.


## <a name="unexpected-downtime"></a>Temps d’arrêt inattendus
On parle de temps d’arrêt inattendu quand le matériel ou l’infrastructure physique de la machine virtuelle échoue de manière inattendue. Cela comprend les défaillances du réseau local, du disque local ou au niveau du rack. Quand une défaillance de ce type est détectée, la plateforme Azure migre (répare) automatiquement votre machine virtuelle vers une machine physique intègre se trouvant dans le même centre de données. Lors de la procédure de réparation, les machines virtuelles subissent des temps d’arrêt (redémarrage) et, dans certains cas, une perte du lecteur temporaire. Le système d’exploitation attaché et les disques de données sont toujours conservés.

Les machines virtuelles peuvent également subir un temps d’arrêt en cas de panne ou de catastrophe qui affecte l’ensemble d’un centre de données ou même d’une région. Dans ce genre de scénarios, Azure offre des options de protection pour les [zones de disponibilité](../availability-zones/az-overview.md) et les [régions jumelées](regions.md#region-pairs).

## <a name="planned-maintenance-events"></a>Événements de maintenance planifiée
Les événements de maintenance planifiés sont des mises à jour périodiques effectuées par Microsoft sur la plateforme sous-jacente Azure pour améliorer la fiabilité, les performances et la sécurité de l'infrastructure hébergeant vos machines virtuelles. La plupart de ces mises à jour se déroulent sans aucune incidence sur les machines virtuelles ou les services cloud (voir [Maintenance ne nécessitant pas de redémarrage](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)). La plateforme Azure s’efforce d’utiliser la maintenance de conservation des machines virtuelles lorsque cela est possible. Toutefois, dans quelques rares cas, ces mises à jour nécessitent le redémarrage de votre machine virtuelle afin d’appliquer les mises à jour requises sur l’infrastructure sous-jacente. Dans ce cas, vous pouvez effectuer la maintenance planifiée Azure via l’opération de maintenance-redéploiement, en lançant la maintenance pour les machines virtuelles dans la période de temps adaptée. Pour plus d’informations, consultez [Maintenance planifiée pour les machines virtuelles](maintenance-and-updates.md).

## <a name="reduce-downtime"></a>Réduire les temps d’arrêt
Pour réduire l'effet des interruptions de service dues à un ou plusieurs de ces événements, nous vous recommandons les meilleures pratiques suivantes pour la haute disponibilité de vos machines virtuelles :

* Utiliser [Zones de disponibilité](../availability-zones/az-overview.md) pour vous protéger contre les défaillances des centres de données
* Configurer plusieurs machines virtuelles dans un [groupe à haute disponibilité](availability-set-overview.md) pour assurer la redondance
* Utiliser des [événements planifiés pour les](./linux/scheduled-events.md) ou des [événements planifiés pour Windows](./windows/scheduled-events.md) afin de répondre de manière proactive aux événements affectant les machines virtuelles
* Configuration de chaque couche application dans des groupes à haute disponibilité séparés
* Combiner un [équilibreur de charge](../load-balancer/load-balancer-overview.md) avec des zones de disponibilité ou des groupes à haute disponibilité

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les options de disponibilité dans Azure, consultez [Vue d’ensemble de la disponibilité](availability.md).