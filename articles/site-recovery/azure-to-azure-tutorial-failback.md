---
title: Restaurer automatiquement des machines virtuelles Azure répliquées vers une région Azure secondaire pour exécuter un plan de reprise d’activité avec le service Azure Site Recovery.
description: Découvrez comment restaurer des machines virtuelles Azure avec le service Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c8ce05e644ad556542314b17151b808586734824
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315315"
---
# <a name="fail-back-azure-vms-between-azure-regions"></a>Restaurer des machines virtuelles Azure entre des régions Azure

Le service [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de reprise d’activité après sinistre en gérant et en coordonnant la réplication, le basculement et la restauration automatique des machines locales et des machines virtuelles Azure.

Ce didacticiel explique comment restaurer une machine virtuelle Azure. Après avoir effectué le basculement, vous effectuez la restauration automatique vers la région primaire quand celle-ci est disponible. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> 
> * Restaurer la machine virtuelle Azure dans la région secondaire.
> * Reprotéger la machine virtuelle Azure principale vers la région secondaire.
> 
> [!NOTE]
> 
> Ce tutoriel vous aide à faire basculer plusieurs machines virtuelles vers une région cible et à les restaurer vers la région source avec des personnalisations minimales. Pour plus d’instructions détaillées, consultez les articles pratiques (procédures) concernant les machines virtuelles Azure.

## <a name="before-you-start"></a>Avant de commencer

> * Assurez-vous que la machine virtuelle se trouve dans l’état **Basculement validé**.
> * Vérifiez que la région primaire est disponible ; vous pouvez alors y créer des ressources et accéder à celles-ci.
> * Assurez-vous que la reprotection est activée.

## <a name="fail-back-to-the-primary-region"></a>Effectuer une restauration automatique vers la région primaire

Une fois les machines virtuelles reprotégées, vous pouvez effectuer une restauration automatique vers la région primaire en fonction de vos besoins.

1. Dans le coffre, cliquez sur **Éléments répliqués**, puis sélectionnez la machine virtuelle qui a été reprotégée.

    ![Restaurer vers la région primaire](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Cliquez sur **Test de basculement** pour effectuer un nouveau test de basculement dans la région primaire.
4. Sélectionnez le point de récupération et le réseau virtuel pour le test de basculement, puis cliquez sur **OK**. Vous pouvez examiner la machine virtuelle de test créée dans la région primaire.
5. Une fois le test de basculement correctement effectué, cliquez sur **Nettoyer le test de basculement** pour nettoyer les ressources créées dans la région source pour l’occasion.
6. Dans **Éléments répliqués**, sélectionnez la machine virtuelle, puis cliquez sur **Basculement**.
7. Dans **Basculement**, sélectionnez un point de récupération vers lequel basculer.
    - **Dernier (par défaut)**  : traite toutes les données dans le service Site Recovery et fournit l’objectif de point de récupération (RPO) le plus bas.
    - **Dernier point traité** : rétablit la machine virtuelle sur le dernier point de récupération ayant été traité par Site Recovery.
    - **Personnalisé** : effectue un basculement vers un point de récupération spécifique. Cette option est utile pour effectuer un test de basculement.

8. Sélectionnez **Arrêter la machine avant de commencer le basculement** si vous souhaitez que Site Recovery tente d’arrêter les machines virtuelles sources avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. Notez que Site Recovery ne nettoie pas la source après le basculement.
9. Suivre la progression du basculement sur la page **Tâches**.
10. Après le basculement, validez la machine virtuelle en vous y connectant. Vous pouvez changer le point de récupération en fonction des besoins.
11. Une fois que vous avez vérifié le basculement, cliquez sur **Valider le basculement**. La validation supprime tous les points de récupération disponibles. L’option de changement de point de récupération n’est plus disponible.
12. La machine virtuelle doit apparaître comme ayant été basculée et restaurée.

    ![Régions primaires et secondaires de machine virtuelle](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> Les machines virtuelles utilisées pour la reprise d’activité demeurent libérées pendant l’arrêt. En effet, Site Recovery enregistre les informations des machines virtuelles, qui pourraient être utiles pour le basculement depuis la région principale vers la région secondaire. Les machines virtuelles libérées ne vous étant pas facturées, gardez-les telles quelles.

## <a name="next-steps"></a>Étapes suivantes

[Découvrez-en plus](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) sur le flux de reprotection.
