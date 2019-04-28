---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 12/14/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: c26c037455b6d14a906894ec39bf46630826950b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301708"
---
Azure met régulièrement à jour la plateforme pour améliorer la fiabilité, le niveau de performance et la sécurité de l’infrastructure hôte des machines virtuelles. Ces mises à jour vont de l’application d’une mise à jour corrective aux composants logiciels de l’environnement d’hébergement, en passant par la mise à niveau des composants réseau, à la désactivation du matériel. La majorité de ces mises à jour n’a aucun impact sur les machines virtuelles hébergées. Toutefois, dans certains cas, les mises à jour ont un impact. Azure choisit alors la méthode ayant le moins d’impact pour l’exécution des mises à jour :

- Si une mise à jour sans redémarrage est possible, la machine virtuelle est en pause pendant la mise à jour de l’hôte ou fait l’objet d’une migration dynamique vers un hôte déjà mis à jour.

- Si la maintenance nécessite un redémarrage, une notification vous dira pour quand est prévue la maintenance. Azure vous permet également de disposer d’une fenêtre de temps où vous pouvez démarrer la maintenance vous-même, au moment qui vous convient. Azure investit dans des technologies qui permettent de réduire le nombre de cas où les machines virtuelles doivent redémarrer pour une maintenance planifiée de la plateforme. 

Cette page décrit la façon dont Azure effectue les deux types de maintenance. Pour plus d’informations sur les événements non planifiés (interruptions), consultez Gérer la disponibilité des machines virtuelles pour [Windows](../articles/virtual-machines/windows/manage-availability.md) ou [Linux](../articles/virtual-machines/linux/manage-availability.md).

Vous pouvez recevoir une notification de machine virtuelle concernant une maintenance à venir à l’aide de Scheduled Events pour [Windows](../articles/virtual-machines/windows/scheduled-events.md) ou [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Pour obtenir des guides pratiques sur la gestion de la maintenance planifiée, consultez « Gestion des notifications de maintenance planifiée » pour [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) ou [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-not-requiring-a-reboot"></a>Maintenance ne pas nécessitant un redémarrage

L’objectif de la plupart des maintenance qui ne nécessite pas un redémarrage est inférieure à 10 secondes Suspendre pour la machine virtuelle. Dans certains cas, des mécanismes de maintenance liés à la conservation de la mémoire sont utilisés, ce qui permet de mettre la machine virtuelle en pause pendant 30 secondes au maximum et de préserver le contenu de la RAM. La machine virtuelle est redémarrée et l’horloge de la machine virtuelle est automatiquement synchronisée. Azure utilise de plus en plus les technologies de migration dynamique et améliore le mécanisme de maintenance lié à la conservation de la mémoire pour réduire la durée de la mise en pause.

Ces opérations de maintenance sans redémarrage sont appliquées domaine d’erreur par domaine d’erreur et sont arrêtées si des signaux d’avertissement sont reçus. 

Certaines applications peuvent être affectées par ces types de mises à jour. Si la machine virtuelle fait l’objet d’une migration dynamique vers un autre hôte, certaines charges de travail sensibles peuvent subir une légère détérioration des performances au cours des quelques minutes qui précèdent la mise en pause de la machine virtuelle. De telles applications peuvent tirer parti de l’utilisation de Scheduled Events pour [Windows](../articles/virtual-machines/windows/scheduled-events.md) ou [Linux](../articles/virtual-machines/linux/scheduled-events.md) afin de préparer la maintenance de la machine virtuelle, et n’ont aucun impact sur la maintenance d’Azure. Azure travaille également sur les fonctionnalités de contrôle de maintenance pour ces applications ultra-sensibles. 


## <a name="maintenance-requiring-a-reboot"></a>Maintenance nécessitant un redémarrage

Dans les rares cas où les machines virtuelles doivent redémarrer pour une maintenance planifiée, vous recevez une notification à l’avance. Une maintenance planifiée comprend deux phases : la fenêtre de libre-service et une fenêtre de maintenance planifiée.

La **fenêtre libre-service** vous permet de démarrer la maintenance sur vos machines virtuelles. Pendant ce temps, vous pouvez interroger chaque machine virtuelle pour afficher leur état et vérifier le résultat de votre dernière requête de maintenance.

Quand vous démarrez la maintenance libre-service, votre machine virtuelle est redéployée sur un nœud déjà mis à jour. À cause du redémarrage de la machine virtuelle, le disque temporaire est perdu et les adresses IP dynamiques associées à l’interface réseau virtuelle sont mises à jour.

Si vous démarrez une maintenance en libre-service et rencontrez une erreur au cours du processus, l’opération s’interrompt, la machine virtuelle n’est pas mise à jour et vous aurez la possibilité de retenter la maintenance en libre-service. 

C’est après la fenêtre de libre-service qu’apparaît la **fenêtre de maintenance planifiée**. Pendant cette fenêtre de temps, vous pouvez toujours interroger la fenêtre de maintenance, mais vous ne pouvez pas démarrer la maintenance vous-même.

Pour obtenir des informations sur la gestion de la maintenance nécessitant un redémarrage, consultez « Gestion des notifications de maintenance planifiée » pour [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) ou [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Considérations relatives à la disponibilité lors de la maintenance planifiée 

Si vous décidez d’attendre jusqu’à la fenêtre de maintenance planifiée, plusieurs éléments sont à prendre en compte si vous voulez maintenir une haute disponibilité de vos machines virtuelles. 

#### <a name="paired-regions"></a>Régions jumelées

Chaque région Azure fait l’objet d’un appairage avec une autre région de la même zone géographique. Ensemble, elles forment une paire régionale. Durant la phase de maintenance planifiée, Azure met à jour uniquement les machines virtuelles d’une seule région d’une paire régionale. Par exemple, durant la mise à jour de la machine virtuelle dans la région USA Centre Nord, Azure ne met à jour aucune machine virtuelle dans la région USA Centre Sud. En revanche, les autres régions, Europe Nord par exemple, peuvent faire l’objet d’une maintenance en même temps que la région USA Est. Comprendre le fonctionnement des paires régionales peut vous aider à mieux répartir vos machines virtuelles entre les régions. Pour en savoir plus, consultez [Paires régionales Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Groupes à haute disponibilité et groupes identiques

Lorsque vous déployez une charge de travail sur des machines virtuelles Azure, vous pouvez créer des machines virtuelles dans un groupe à haute disponibilité afin de fournir une haute disponibilité pour votre application. Ainsi, pendant une interruption ou des événements de maintenance entraînant un redémarrage, au moins une machine virtuelle est disponible.

Au sein d’un groupe à haute disponibilité, les machines virtuelles individuelles sont réparties sur un maximum de 20 domaines de mise à jour (UD). Durant la maintenance planifiée, un seul domaine de mise à jour est mis à jour à un moment donné. L’ordre de mise à jour des domaines de mise à jour ne s’effectue pas nécessairement de manière séquentielle. 

Les groupes de machines virtuelles identiques sont des ressources Azure Compute que vous pouvez utiliser pour déployer et gérer un ensemble de machines virtuelles identiques en tant que ressource unique. Le groupe identique est automatiquement déployé dans les domaines de mise à jour, comme les machines virtuelles dans un groupe à haute disponibilité. Comme pour les groupes à haute disponibilité, un seul domaine de mise à jour est mis à jour à un moment donné dans les groupes identiques durant la maintenance planifiée.

Pour plus d’informations sur la configuration de vos machines virtuelles pour une haute disponibilité, consultez Gérer la disponibilité de vos machines virtuelles pour [Windows](../articles/virtual-machines/windows/manage-availability.md) ou [Linux](../articles/virtual-machines/linux/manage-availability.md).
