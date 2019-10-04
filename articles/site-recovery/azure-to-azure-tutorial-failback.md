---
title: Restaurer automatiquement des machines virtuelles Azure répliquées vers une région Azure secondaire pour exécuter un plan de reprise d’activité avec le service Azure Site Recovery.
description: Découvrez comment restaurer des machines virtuelles Azure avec le service Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c8be547790452774992b9226ca8010532263aaff
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814517"
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

2. Dans **Éléments répliqués**, sélectionnez la machine virtuelle, puis **Basculer**.
3. Dans **Basculer**, sélectionnez un point de récupération vers lequel effectuer le basculement :
    - **Dernier (par défaut)**  : traite toutes les données dans le service Site Recovery et fournit l’objectif de point de récupération (RPO) le plus bas.
    - **Dernier point traité** : rétablit la machine virtuelle sur le dernier point de récupération ayant été traité par Site Recovery.
    - **Personnalisé** : effectue un basculement vers un point de récupération spécifique. Cette option est utile pour effectuer un test de basculement.
4. Sélectionnez **Arrêter la machine avant de commencer le basculement** si vous souhaitez que Site Recovery tente d’arrêter les machines virtuelles dans la région DR avant de déclencher le basculement. Le basculement se poursuit même en cas d’échec de l’arrêt. 
5. Suivre la progression du basculement sur la page **Tâches**.
6. Après le basculement, validez la machine virtuelle en vous y connectant. Vous pouvez changer le point de récupération en fonction des besoins.
7. Une fois que vous avez vérifié le basculement, sélectionnez **Valider le basculement**. La validation supprime tous les points de récupération disponibles. L’option de changement de point de récupération n’est plus disponible.
8. La machine virtuelle doit apparaître comme ayant été basculée et restaurée.

    ![Régions primaires et secondaires de machine virtuelle](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

## <a name="next-steps"></a>Étapes suivantes

[Découvrez-en plus](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) sur le flux de reprotection.
