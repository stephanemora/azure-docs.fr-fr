---
title: Effectuez une restauration automatique des machines virtuelles Azure vers une région primaire à l’aide du service Azure Site Recovery.
description: Explique comment effectuer une restauration automatique des machines virtuelles Azure vers la région primaire à l’aide du service Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 432c92bcfa8a2e0df26adf1516f5bdc9ee73d267
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502373"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Restaurer automatiquement des machines virtuelles Azure entre des régions Azure

Le service [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de reprise d’activité après sinistre en gérant et en coordonnant la réplication, le basculement et la restauration automatique des machines locales et des machines virtuelles Azure.

Ce didacticiel explique comment restaurer une machine virtuelle Azure. Une fois que vous avez effectué le basculement, vous devez effectuer la restauration automatique vers la région primaire quand celle-ci est disponible. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> 
> * Restaurez automatiquement la machine virtuelle Azure dans la région secondaire.
> * Reprotégez la machine virtuelle principale vers la région secondaire.
> 
> [!NOTE]
> 
> Ce tutoriel vous aide à faire basculer plusieurs machines virtuelles vers une région cible et à les restaurer vers la région source avec des personnalisations minimales. Pour plus d’instructions détaillées, consultez les [guides pratiques sur les machines virtuelles Azure](../virtual-machines/windows/index.yml).

## <a name="before-you-start"></a>Avant de commencer

* Assurez-vous que la machine virtuelle se trouve dans l’état **Basculement validé**.
* Vérifiez que la région primaire est disponible ; vous pouvez alors y créer des ressources et accéder à celles-ci.
* Vérifiez que la reprotection est activée.

## <a name="fail-back-to-the-primary-region"></a>Effectuer une restauration automatique vers la région primaire

Une fois les machines virtuelles reprotégées, vous pouvez effectuer une restauration automatique vers la région primaire en fonction de vos besoins.

1. Dans le coffre, sélectionnez **Éléments répliqués**, puis la machine virtuelle qui a été reprotégée.

    ![Capture d’écran montrant la restauration automatique vers la région primaire dans le portail Azure](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. Dans **Éléments répliqués**, sélectionnez la machine virtuelle, puis **Basculer**.
3. Dans **Basculer**, sélectionnez un point de récupération vers lequel effectuer le basculement :
    - **Dernier (par défaut)**  : traite toutes les données dans le service Site Recovery et fournit l’objectif de point de récupération (RPO) le plus bas.
    - **Dernier point traité** : rétablit la machine virtuelle sur le dernier point de récupération ayant été traité par Site Recovery.
    - **Personnalisé** : effectue un basculement vers un point de récupération spécifique. Cette option est utile pour effectuer un test de basculement.
4. Sélectionnez **Arrêter la machine avant de commencer le basculement** si vous souhaitez que Site Recovery tente d’arrêter les machines virtuelles dans la région DR avant de déclencher le basculement. Le basculement se poursuit même en cas d’échec de l’arrêt. 
5. Suivre la progression du basculement sur la page **Tâches**.
6. Après le basculement, validez la machine virtuelle en vous y connectant. Vous pouvez changer le point de récupération en fonction des besoins.
7. Une fois que vous avez vérifié le basculement, sélectionnez **Valider le basculement**. La validation supprime tous les points de récupération disponibles. L’option de changement de point de récupération n’est plus disponible.
8. La machine virtuelle doit apparaître comme ayant été basculée et restaurée.

    ![Capture d’écran montrant la machine virtuelle dans la région primaire et la région secondaire](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> Pour les machines utilisant des disques managés et exécutant l’extension Site Recovery versions 9.28.x.x et supérieures avec le [Correctif cumulatif 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery), Site Recovery nettoie les machines de la région secondaire de reprise d’activité après sinistre, une fois la restauration automatique effectuée et les machines virtuelles reprotégées. Il n’est pas nécessaire de supprimer manuellement les machines virtuelles et les cartes d’interface réseau dans la région secondaire. Notez que les machines virtuelles dotées de disques non managés ne sont pas nettoyées. Si vous désactivez complètement la réplication après une restauration automatique, Site Recovery nettoie les disques de la région de reprise d’activité après sinistre, en plus des machines virtuelles et des cartes d’interface réseau.

## <a name="next-steps"></a>Étapes suivantes

[Découvrez-en plus](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) sur le flux de reprotection.
