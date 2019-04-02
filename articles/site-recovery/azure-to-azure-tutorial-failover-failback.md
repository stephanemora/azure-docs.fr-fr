---
title: Basculez et reprotégez des machines virtuelles Azure répliquées sur une région Azure secondaire pour une reprise d’activité après sinistre avec le service Azure Site Recovery.
description: Découvrez comment basculer et reprotéger des machines virtuelles Azure répliquées sur une région Azure secondaire pour une reprise d’activité après sinistre avec le service Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/18/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c19e554571927efa907350df1f1fbbd8ff491c42
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314754"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Basculer et reprotéger des machines virtuelles Azure entre des régions

Le service [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de récupération d’urgence en gérant et en coordonnant la réplication, le basculement et la restauration automatique des machines locales et des machines virtuelles Azure.

Ce tutoriel explique comment basculer une machine virtuelle Azure vers une région Azure secondaire. Une fois le basculement effectué, vous reprotégez la machine virtuelle. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Basculer la machine virtuelle Azure
> * Reprotéger la machine virtuelle Azure secondaire, afin qu’elle soit répliquée vers la région primaire.

> [!NOTE]
> Ce tutoriel indique la méthode la plus simple avec des paramètres par défaut et une personnalisation minimale. Pour les scénarios plus complexes, utilisez les articles figurant sous « Guide pratique » pour les machines virtuelles Azure.

## <a name="prerequisites"></a>Prérequis

- Assurez-vous d’avoir effectué une [simulation de récupération d’urgence](azure-to-azure-tutorial-dr-drill.md) pour vérifier que tout fonctionne comme prévu.
- Vérifiez les propriétés de la machine virtuelle avant d’exécuter le test de basculement. La machine virtuelle doit satisfaire aux [exigences Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Exécuter un basculement vers la région secondaire

1. Dans **Éléments répliqués**, sélectionnez la machine virtuelle que vous souhaitez basculer > **Basculement**.

   ![Basculement](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. Dans **Basculement**, sélectionnez un **point de récupération** vers lequel basculer. Vous pouvez utiliser l’une des options suivantes :

   * **Dernier** (par défaut) : Traite toutes les données dans le service Site Recovery et fournit l’objectif de point de récupération (RPO) le plus bas.
   * **Dernier point traité** : Rétablit la machine virtuelle sur le dernier point de récupération ayant été traité par le service Site Recovery.
   * **Personnalisé** : Effectue un basculement vers un point de récupération spécifique. Cette option est utile pour effectuer un test de basculement.

3. Sélectionnez **Arrêter la machine avant de commencer le basculement** si vous souhaitez que Site Recovery tente d’arrêter les machines virtuelles sources avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. Site Recovery ne nettoie pas la source après le basculement.

4. Suivre la progression du basculement sur la page **Tâches**.

5. Après le basculement, validez la machine virtuelle en vous y connectant. Si vous souhaitez accéder à un autre point de récupération pour la machine virtuelle, utilisez l’option **Changer le point de récupération**.

6. Si vous êtes satisfait de la machine vers laquelle est effectué le basculement, vous pouvez **Valider** le basculement.
   La validation supprime tous les points de récupération disponibles avec le service. Désormais, vous ne pouvez pas changer le point de récupération.

## <a name="reprotect-the-secondary-vm"></a>Reprotéger la machine virtuelle secondaire

Après avoir basculé la machine virtuelle, vous devez la reprotéger afin qu’elle soit répliquée vers la région principale.

1. Vérifiez que la machine virtuelle se trouve dans l’état **Basculement validé** et que la région primaire est disponible ; vous pouvez alors y créer des ressources et accéder à celles-ci.
2. Dans **Coffre** > **Éléments répliqués**, cliquez avec le bouton droit sur la machine virtuelle ayant été basculée et sélectionnez **Reprotéger**.

   ![Cliquer avec le bouton droit pour reprotéger](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Vérifiez que la direction de la protection, à savoir depuis la région secondaire vers la région primaire, est déjà sélectionnée.
3. Vérifiez les informations **Groupe de ressources, Réseau, Stockage et Groupes à haute disponibilité**. Les ressources éventuellement marquées comme nouvelles sont créées dans le cadre de l’opération de reprotection.
4. Cliquez sur **OK** pour déclencher un travail de reprotection. Ce travail amorce le site cible avec les données les plus récentes. Ensuite, il réplique les deltas vers la région primaire. La machine virtuelle est désormais dans un état protégé.

## <a name="next-steps"></a>Étapes suivantes
- Après avoir effectué la reprotection, [découvrez comment](azure-to-azure-tutorial-failback.md) effectuer une restauration automatique vers la région primaire quand celle-ci est disponible.
- [Découvrez-en plus](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) sur le flux de reprotection.
