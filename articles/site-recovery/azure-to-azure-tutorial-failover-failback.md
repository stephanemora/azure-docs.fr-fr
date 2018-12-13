---
title: Basculer et restaurer automatiquement des machines virtuelles IaaS Azure répliquées vers une région Azure secondaire pour exécuter un plan de récupération d’urgence avec le service Azure Site Recovery.
description: Découvrez comment basculer et restaurer automatiquement des machines virtuelles IaaS Azure répliquées vers une région Azure secondaire pour effectuer un plan de récupération d’urgence avec le service Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 93928d7eb84ce986c8a9322188183e4c3dd76d99
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847862"
---
# <a name="fail-over-and-fail-back-azure-vms-between-azure-regions"></a>Basculer et restaurer automatiquement des machines virtuelles Azure entre des régions Azure

Le service [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de récupération d’urgence en gérant et en coordonnant la réplication, le basculement et la restauration automatique des machines locales et des machines virtuelles Azure.

Ce didacticiel explique comment basculer une seule machine virtuelle Azure vers une région Azure secondaire. Après avoir effectué le basculement, vous effectuez la restauration automatique vers la région primaire quand celle-ci est disponible. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Basculer la machine virtuelle Azure
> * Reprotéger la machine virtuelle Azure secondaire, afin qu’elle soit répliquée vers la région primaire
> * Restaurer automatiquement la machine virtuelle secondaire
> * Reprotéger la machine virtuelle principale vers la région secondaire

> [!NOTE]
> Ce tutoriel a pour but de guider l’utilisateur à travers les étapes à suivre pour effectuer un basculement vers une région cible avec une personnalisation minimale. Si vous souhaitez en savoir plus sur les différents aspects associés à l’exécution d’un basculement, y compris les éléments à prendre en compte concernant la mise en réseau, l’automation ou le dépannage, consultez les documents dans « Procédure... » pour les machines virtuelles Azure.

## <a name="prerequisites"></a>Prérequis

- Assurez-vous d’avoir effectué une [simulation de récupération d’urgence](azure-to-azure-tutorial-dr-drill.md) pour vérifier que tout fonctionne comme prévu.
- Vérifiez les propriétés de la machine virtuelle avant d’exécuter le test de basculement. La machine virtuelle doit satisfaire aux [exigences Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Exécuter un basculement vers la région secondaire

1. Dans **Éléments répliqués**, sélectionnez la machine virtuelle que vous souhaitez basculer > **Basculement**.

   ![Basculement](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. Dans **Basculement**, sélectionnez un **point de récupération** vers lequel basculer. Vous pouvez utiliser l’une des options suivantes :

   * **Dernier** (par défaut) : cette option traite toutes les données dans le service Site Recovery et fournit l’objectif de point de récupération (RPO) le plus bas.
   * **Dernier point traité** : cette option rétablit la machine virtuelle vers le dernier point de récupération ayant été traité par le service Site Recovery.
   * **Personnalisé** : utilisez cette option pour effectuer un basculement vers un point de récupération spécifique. Cette option est utile pour effectuer un test de basculement.

3. Sélectionnez **Arrêter la machine avant de commencer le basculement** si vous souhaitez que Site Recovery tente d’arrêter les machines virtuelles source avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt.

4. Suivre la progression du basculement sur la page **Tâches**.

5. Après le basculement, validez la machine virtuelle en vous y connectant. Si vous souhaitez accéder à un autre point de récupération pour la machine virtuelle, utilisez l’option **Changer le point de récupération**.

6. Si vous êtes satisfait de la machine vers laquelle est effectué le basculement, vous pouvez **Valider** le basculement.
   La validation supprime tous les points de récupération disponibles avec le service. L’option **Modifier le point de récupération** n’est plus disponible.

## <a name="reprotect-the-secondary-vm"></a>Reprotéger la machine virtuelle secondaire

Après avoir basculé la machine virtuelle, vous devez la reprotéger afin qu’elle soit répliquée vers la région principale.

1. Vérifiez que la machine virtuelle se trouve dans l’état **Basculement validé** et que la région primaire est disponible ; vous pouvez alors y créer des ressources et accéder à celles-ci.
2. Dans **Coffre** > **Éléments répliqués**, cliquez avec le bouton droit sur la machine virtuelle ayant été basculée et sélectionnez **Reprotéger**.

   ![Cliquer avec le bouton droit pour reprotéger](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Notez que la direction de la protection, à savoir depuis la région secondaire vers la région primaire, est déjà sélectionnée.
3. Vérifiez les informations **Groupe de ressources, Réseau, Stockage et Groupes à haute disponibilité**. Les ressources éventuellement marquées (nouvelles) sont créées dans le cadre de l’opération de reprotection.
4. Cliquez sur **OK** pour déclencher un travail de reprotection. Ce travail amorce le site cible avec les données les plus récentes. Ensuite, il réplique les deltas vers la région primaire. La machine virtuelle est désormais dans un état protégé.

## <a name="fail-back-to-the-primary-region"></a>Effectuer une restauration automatique vers la région primaire

Une fois les machines virtuelles reprotégées, vous pouvez effectuer une restauration automatique vers la région primaire en fonction de vos besoins. Pour effectuer cette opération, configurez un basculement de la région secondaire vers la région primaire, comme décrit dans cet article.
