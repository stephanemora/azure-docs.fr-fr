---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 4/30/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: c2931fa410cf92755a5df5b7129dcf93de900930
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155925"
---
Azure met régulièrement à jour sa plateforme afin d’améliorer la fiabilité, performances et la sécurité de l’infrastructure hôte pour les machines virtuelles. L’objectif de ces plages de mises à jour à partir de la mise à jour corrective de composants logiciels dans l’environnement d’hébergement pour la mise à niveau de composants réseau ou de désactivation de matériel. 

Mises à jour affectent rarement les machines virtuelles hébergées. Lorsque les mises à jour n’ont pas d’effet, Azure choisit la méthode la moins fort impact des mises à jour :

- Si la mise à jour ne nécessite pas un redémarrage, la machine virtuelle est en pause pendant que l’hôte est mis à jour, ou la machine virtuelle est migrés vers un hôte déjà mis à jour.

- Si la maintenance nécessite un redémarrage, vous êtes averti de la maintenance planifiée. Azure fournit également une fenêtre de temps dans laquelle vous pouvez commencer la maintenance vous-même, au moment qui vous convient. La fenêtre de maintenance de libre-service est généralement de 30 jours, sauf si la maintenance est urgente. Azure investit dans les technologies afin de réduire le nombre de cas dans lequel plateforme planifié maintenance nécessite les machines virtuelles à redémarrer. 

Cette page décrit la façon dont Azure effectue les deux types de maintenance. Pour plus d’informations sur les événements non planifiés (pannes), consultez [gérer la disponibilité des machines virtuelles pour Windows](../articles/virtual-machines/windows/manage-availability.md) ou l’article correspondant pour [Linux](../articles/virtual-machines/linux/manage-availability.md).

Au sein d’une machine virtuelle, vous pouvez obtenir des notifications concernant la maintenance à venir par [à l’aide de planifié des événements pour Windows](../articles/virtual-machines/windows/scheduled-events.md) ou pour [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Pour obtenir des instructions sur la gestion de la maintenance planifiée, consultez [gestion des notifications de maintenance pour Linux planifiée](../articles/virtual-machines/linux/maintenance-notifications.md) ou l’article correspondant pour [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-that-doesnt-require-a-reboot"></a>Maintenance qui ne nécessite pas un redémarrage

Comme indiqué précédemment, la plupart des mises à jour de plateforme n’affectent pas les machines virtuelles client. Lorsqu’une mise à jour sans impact n’est pas possible, Azure choisit le mécanisme de mise à jour qui est moins qui risquent d’affecter aux machines virtuelles du client. 

La plupart des différente de zéro impact maintenance suspend la machine virtuelle pour moins de 10 secondes. Dans certains cas, Azure utilise des mécanismes de maintenance de préservation de la mémoire. Ces mécanismes suspend la machine virtuelle pendant 30 secondes et conservent la mémoire RAM. La machine virtuelle est redémarrée, et son horloge est automatiquement synchronisée. 

Fonctionne de la maintenance de préservation de la mémoire pour plus de 90 % des machines virtuelles Azure. Il ne fonctionne pas pour la série G, M, N et H. Azure utilise les technologies de migration dynamique plus en plus et améliore les mécanismes de maintenance de préservation de la mémoire pour réduire les durées de pause.  

Ces opérations de maintenance qui ne nécessitent pas un redémarrage sont appliqués un domaine d’erreur à la fois. Ils arrêtent s’ils reçoivent les signaux d’intégrité d’avertissement. 

Ces types de mises à jour peuvent affecter certaines applications. Lorsque la machine virtuelle est migrés vers un hôte différent, certaines charges de travail sensibles peuvent afficher une légère dégradation des performances dans les minutes mènent à la suspension de la machine virtuelle. Pour préparer pour la maintenance de la machine virtuelle et réduire l’impact lors de la maintenance Azure, essayez [à l’aide de planifié des événements pour Windows](../articles/virtual-machines/windows/scheduled-events.md) ou [Linux](../articles/virtual-machines/linux/scheduled-events.md) pour de telles applications. Azure fonctionne sur les fonctionnalités de contrôle de maintenance pour ces applications sensibles. 

### <a name="live-migration"></a>Migration dynamique

Migration dynamique est une opération qui ne nécessite pas un redémarrage et qui conserve la mémoire pour la machine virtuelle. Il entraîne une pause ou un blocage, dure généralement pas plus de 5 secondes. À l’exception de G, série H, M et N, toute l’infrastructure en tant qu’un service (IaaS) des machines virtuelles, est éligibles pour la migration dynamique. Machines virtuelles éligibles représentent plus de 90 % des machines virtuelles IaaS déployées sur la flotte Azure. 

La plateforme Azure démarre la migration dynamique dans les scénarios suivants :
- Maintenance planifiée
- Défaillance matérielle
- Optimisations d’allocation

Certains scénarios de maintenance planifiée utilisent la migration dynamique, et vous pouvez utiliser des événements planifiés pour savoir à l’avance en direct les opérations de migration démarre.

Migration dynamique peut également servir à déplacer des machines virtuelles lorsque les algorithmes Azure Machine Learning prédire une défaillance matérielle imminente ou lorsque vous souhaitez optimiser les allocations d’ordinateurs virtuels. Pour plus d’informations sur la modélisation prédictive qui détecte les instances du matériel détérioré, consultez [la résilience d’amélioration de machine virtuelle Azure avec apprentissage prédictive et la migration dynamique](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Notifications de migration en direct s’affichent dans le portail Azure dans les journaux Moniteur et l’intégrité du Service ainsi que dans les événements planifiés, si vous utilisez ces services.

## <a name="maintenance-that-requires-a-reboot"></a>Maintenance nécessitant un redémarrage

Dans les rares cas où les machines virtuelles doivent être redémarrés pour la maintenance planifiée, vous serez averti à l’avance. Maintenance planifiée comprend deux phases : la phase de libre-service et une phase de maintenance planifiée.

Lors de la *phase libre-service*, qui dure généralement des quatre dernières semaines, vous démarrez la maintenance sur vos machines virtuelles. Dans le cadre de libre-service, vous pouvez interroger chaque machine virtuelle pour voir son état et le résultat de votre dernière demande de maintenance.

Quand vous démarrez la maintenance libre-service, votre machine virtuelle est redéployée sur un nœud déjà mis à jour. Étant donné que la machine virtuelle redémarre, le disque temporaire est perdu et les adresses IP dynamiques associées à l’interface réseau virtuelle sont mises à jour.

Si une erreur se produit lors de la maintenance de libre-service, l’opération s’arrête, la machine virtuelle n’est pas mis à jour, et vous avez la possibilité pour retenter la maintenance en libre-service. 

Lorsque la phase de libre-service se termine, le *phase de maintenance planifiée* commence. Pendant cette phase, vous pouvez toujours interroger la phase de maintenance, mais vous ne pouvez pas démarrer la maintenance.

Pour plus d’informations sur la gestion de maintenance nécessitant un redémarrage, consultez [gestion des notifications de maintenance pour Linux planifiée](../articles/virtual-machines/linux/maintenance-notifications.md) ou l’article correspondant pour [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Considérations sur la disponibilité lors de la maintenance planifiée 

Si vous décidez d’attendre jusqu'à la phase de maintenance planifiée, il existe quelques éléments que vous devez envisager de maintenir la haute disponibilité de vos machines virtuelles. 

#### <a name="paired-regions"></a>Régions jumelées

Chaque région Azure est associée à une autre région au sein de la même zone géographique. Elles constituent une paire de régions. Pendant la phase de maintenance planifiée, Azure met à jour uniquement les machines virtuelles dans une seule région d’une paire régionale. Par exemple, lors de la mise à jour de la machine virtuelle en Amérique du Nord, Azure ne met à jour toutes les machines virtuelles dans sud du centre des États-Unis en même temps. En revanche, les autres régions, Europe Nord par exemple, peuvent faire l’objet d’une maintenance en même temps que la région USA Est. Comprendre le fonctionnement des paires régionales peut vous aider à mieux répartir vos machines virtuelles entre les régions. Pour en savoir plus, consultez [Paires régionales Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Groupes à haute disponibilité et groupes identiques

Lorsque vous déployez une charge de travail sur des machines virtuelles Azure, vous pouvez créer les machines virtuelles au sein d’un *à haute disponibilité* pour fournir une haute disponibilité pour votre application. À l’aide de groupes à haute disponibilité, vous pouvez vous assurer que, pendant les événements d’une panne ou de la maintenance nécessitant un redémarrage, au moins une machine virtuelle est disponible.

Au sein d’un groupe à haute disponibilité, les machines virtuelles individuelles sont réparties sur un maximum de 20 domaines de mise à jour (UD). Lors de la maintenance planifiée, qu’un seul UD est mis à jour à un moment donné. Mise à niveau ne sont pas nécessairement actualisés de façon séquentielle. 

Machine virtuelle *identiques* sont les ressources que vous pouvez utiliser pour déployer et gérer un ensemble de machines virtuelles identiques en tant qu’une seule ressource de calcul Azure. Le groupe identique est déployé automatiquement entre les ud, comme les machines virtuelles dans un groupe à haute disponibilité. Comme avec les groupes à haute disponibilité, lorsque vous utilisez des groupes identiques, UD qu’une seule est mis à jour à un moment donné pendant la maintenance planifiée.

Pour plus d’informations sur la configuration de vos machines virtuelles pour la haute disponibilité, consultez [gestion de la disponibilité de vos machines virtuelles pour Windows](../articles/virtual-machines/windows/manage-availability.md) ou l’article correspondant pour [Linux](../articles/virtual-machines/linux/manage-availability.md).
