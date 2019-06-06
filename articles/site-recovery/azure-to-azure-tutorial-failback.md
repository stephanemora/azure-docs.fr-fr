---
title: Restaurer automatiquement des machines virtuelles Azure répliquées vers une région Azure secondaire pour exécuter un plan de reprise d’activité avec le service Azure Site Recovery.
description: Découvrez comment restaurer des machines virtuelles Azure avec le service Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: a3b67e9b0dc41eeb14000400912892fbf29acfe2
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399496"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Restaurer automatiquement des machines virtuelles Azure entre des régions Azure

Le service [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de reprise d’activité après sinistre en gérant et en coordonnant la réplication, le basculement et la restauration automatique des machines locales et des machines virtuelles Azure.

Ce didacticiel explique comment restaurer une machine virtuelle Azure. Une fois que vous avez effectué le basculement, vous devez effectuer la restauration automatique vers la région primaire quand celle-ci est disponible. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> 
> * Restaurez automatiquement la machine virtuelle Azure dans la région secondaire.
> * Reprotégez la machine virtuelle principale vers la région secondaire.
> 
> [!NOTE]
> 
> Ce tutoriel vous aide à faire basculer plusieurs machines virtuelles vers une région cible et à les restaurer vers la région source avec des personnalisations minimales. Pour plus d’instructions détaillées, consultez les [guides pratiques sur les machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/windows/).

## <a name="before-you-start"></a>Avant de commencer

* Assurez-vous que la machine virtuelle se trouve dans l’état **Basculement validé**.
* Vérifiez que la région primaire est disponible ; vous pouvez alors y créer des ressources et accéder à celles-ci.
* Vérifiez que la reprotection est activée.

## <a name="fail-back-to-the-primary-region"></a>Effectuer une restauration automatique vers la région primaire

Une fois les machines virtuelles reprotégées, vous pouvez effectuer une restauration automatique vers la région primaire en fonction de vos besoins.

1. Dans le coffre, sélectionnez **Éléments répliqués**, puis la machine virtuelle qui a été reprotégée.

    ![Restaurer vers la région primaire](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Sélectionnez **Test de basculement** pour effectuer un nouveau test de basculement dans la région primaire.
4. Sélectionnez le point de récupération et le réseau virtuel pour le test de basculement, puis sélectionnez **OK**. Vous pouvez examiner la machine virtuelle de test créée dans la région primaire.
5. Une fois le test de basculement correctement effectué, sélectionnez **Nettoyer le test de basculement** pour nettoyer les ressources créées dans la région source pour l’occasion.
6. Dans **Éléments répliqués**, sélectionnez la machine virtuelle, puis **Basculer**.
7. Dans **Basculer**, sélectionnez un point de récupération vers lequel effectuer le basculement :
    - **Dernier (par défaut)**  : traite toutes les données dans le service Site Recovery et fournit l’objectif de point de récupération (RPO) le plus bas.
    - **Dernier point traité** : rétablit la machine virtuelle sur le dernier point de récupération ayant été traité par Site Recovery.
    - **Personnalisé** : effectue un basculement vers un point de récupération spécifique. Cette option est utile pour effectuer un test de basculement.

8. Sélectionnez **Arrêter la machine avant de commencer le basculement** si vous souhaitez que Site Recovery tente d’arrêter les machines virtuelles sources avant de déclencher le basculement. Le basculement se poursuit même en cas d’échec de l’arrêt. Notez que Site Recovery ne nettoie pas la source après le basculement.
9. Suivre la progression du basculement sur la page **Tâches**.
10. Après le basculement, validez la machine virtuelle en vous y connectant. Vous pouvez changer le point de récupération en fonction des besoins.
11. Une fois que vous avez vérifié le basculement, sélectionnez **Valider le basculement**. La validation supprime tous les points de récupération disponibles. L’option de changement de point de récupération n’est plus disponible.
12. La machine virtuelle doit apparaître comme ayant été basculée et restaurée.

    ![Régions primaires et secondaires de machine virtuelle](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> Les machines virtuelles utilisées pour la reprise d’activité après sinistre demeurent dans l’état libéré/arrêté. En effet, Site Recovery enregistre les informations des machines virtuelles, qui pourraient être utiles pour le basculement depuis la région principale vers la région secondaire. Les machines virtuelles libérées ne vous étant pas facturées, gardez-les telles quelles.

## <a name="next-steps"></a>Étapes suivantes

[Découvrez-en plus](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) sur le flux de reprotection.
