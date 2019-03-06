---
title: Basculer et restaurer automatiquement des machines virtuelles IaaS Azure répliquées vers une région Azure secondaire pour exécuter un plan de récupération d’urgence avec le service Azure Site Recovery.
description: Découvrez comment basculer et restaurer automatiquement des machines virtuelles IaaS Azure répliquées vers une région Azure secondaire pour effectuer un plan de récupération d’urgence avec le service Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 46dae28fd6c9eaa3d5e03f5f06c5e92449653679
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737720"
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

   * **Dernier** (par défaut) : cette option traite toutes les données dans le service Site Recovery et fournit l’objectif de point de récupération (RPO) le plus bas.
   * **Dernier point traité** : cette option rétablit la machine virtuelle vers le dernier point de récupération ayant été traité par le service Site Recovery.
   * **Personnalisé** : utilisez cette option pour effectuer un basculement vers un point de récupération spécifique. Cette option est utile pour effectuer un test de basculement.

3. Sélectionnez **Arrêter la machine avant de commencer le basculement** si vous souhaitez que Site Recovery tente d’arrêter les machines virtuelles source avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. Notez que Site Recovery ne nettoie pas la source après le basculement.

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

> [!NOTE]
> Pour plus d’informations sur le flux de travail de reprotection et sur ce qui se passe pendant la reprotection, consultez [la section de procédure de cet article](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).


## <a name="fail-back-to-the-primary-region"></a>Effectuer une restauration automatique vers la région primaire

Une fois les machines virtuelles reprotégées, vous pouvez effectuer une restauration automatique vers la région primaire en fonction de vos besoins. Pour effectuer cette opération, configurez un basculement de la région secondaire vers la région primaire, comme décrit dans cet article.

![Cliquer avec le bouton droit pour reprotéger](./media/azure-to-azure-tutorial-failover-failback/failback.png)

Dans la capture d’écran précédente, la machine virtuelle « ContosoWin2016 » a fait l’objet d’un basculement de la région USA Centre vers la région USA Est, et d’une restauration automatique de la région USA Est vers la région USA Centre.

Le basculement arrête la machine virtuelle dans la région secondaire, c’est-à-dire la région de reprise d’activité, et crée et démarre la machine virtuelle dans la région primaire. **Notez** que les machines virtuelles de reprise d’activité restent dans l’état Arrêté (libéré) comme indiqué ci-dessus. Ce comportement est normal, car Azure Site Recovery enregistre les informations de la machine virtuelle qui peuvent s’avérer utiles par la suite lors du basculement de la région primaire vers la région secondaire. Les machines virtuelles libérées ne vous étant pas facturées, gardez-les telles quelles.
