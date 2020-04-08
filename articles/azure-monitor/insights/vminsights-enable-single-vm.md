---
title: Activer Azure Monitor pour machines virtuelles sur le portail Azure
description: Apprenez à évaluer Azure Monitor pour machines virtuelles sur une seule machine virtuelle Azure ou sur un groupe de machines virtuelles identiques.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 45bc8f16a547d4a95820f9dcd02132844b3be83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480706"
---
# <a name="enable-azure-monitor-for-vms-in-the-azure-portal"></a>Activer Azure Monitor pour machines virtuelles sur le portail Azure

Cet article explique comment activer Azure Monitor pour machines virtuelles sur un petit nombre de machines virtuelles Azure à partir du portail Azure. Votre objectif est de surveiller vos machines virtuelles et de découvrir tout problème de performances ou de disponibilité. 

Avant de commencer, consultez les [conditions préalables](vminsights-enable-overview.md) et vérifiez que votre abonnement et vos ressources répondent aux exigences.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Activer la surveillance pour une machine virtuelle Azure
Pour activer la surveillance pour votre machine virtuelle Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Machines virtuelles**.

1. Sélectionnez une machine virtuelle dans la liste.

1. Dans la page de la machine virtuelle, dans la section **Supervision**, sélectionnez **Insights**, puis **Activer**.

    ![Activer Azure Monitor pour machines virtuelles pour une machine virtuelle](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Dans la page **Intégration des insights de Azure Monitor**, si vous disposez d’un espace de travail Log Analytics appartenant au même abonnement, sélectionnez-le dans la liste déroulante.  

    La liste présélectionne l’espace de travail par défaut et l’emplacement où la machine virtuelle est déployée dans l’abonnement. 

    >[!NOTE]
    >Pour créer un espace de travail Log Analytics pour stocker les données de surveillance de la machine virtuelle, consultez [Créer un espace de travail Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Votre espace de travail Log Analytics doit appartenir à l’une des [régions prises en charge](vminsights-enable-overview.md#log-analytics).

6. Vous recevez des messages d’état à mesure que la configuration est effectuée.

    ![Activer Azure Monitor pour machines virtuelles surveillant le traitement du déploiement](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Activer la surveillance pour un groupe de machines virtuelles identiques

Pour activer la surveillance pour un groupe de machines virtuelles identiques :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Sélectionnez **Groupes de machines virtuelles identiques**.

3. Dans la liste, sélectionnez un groupe de machines virtuelles identiques.

4. Dans la page du groupe de machines virtuelles identiques, dans la section **Supervision**, sélectionnez **Insights**, puis **Activer**.

5. Si vous voulez utiliser un espace de travail Log Analytics existant, sélectionnez-le dans la liste déroulante de la page **Insights**.

    La liste présélectionne l’espace de travail par défaut et l’emplacement où la machine virtuelle est déployée dans l’abonnement. 

    ![Activer Azure Monitor pour machines virtuelles pour un groupe de machines virtuelles identiques](media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Pour créer un espace de travail Log Analytics pour stocker les données de surveillance du groupe de machines virtuelles identiques, consultez [Créer un espace de travail Log Analytics](../learn/quick-create-workspace.md). Votre espace de travail Log Analytics doit appartenir à l’une des [régions prises en charge](vminsights-enable-overview.md#log-analytics).

6. Vous recevez des messages d’état à mesure que la configuration est effectuée.

    >[!NOTE]
    >Si vous utilisez un modèle de mise à niveau manuel pour votre groupe identique, mettez à jour les instances afin de terminer la configuration. Vous pouvez démarrer les mises à niveau depuis la page **Instances** de la section **Paramètres**.
    
    ![Activer Azure Monitor pour machines virtuelles surveillant le traitement du déploiement](media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status.png)

À présent que vous avez activé la surveillance de votre machine virtuelle ou de votre groupe de machines virtuelles identiques, les informations de surveillance sont disponibles pour analyse dans Azure Monitor pour machines virtuelles. 

## <a name="next-steps"></a>Étapes suivantes

* Pour afficher les dépendances des applications détectées, consultez [Utilisation de la fonctionnalité Map d’Azure Monitor pour machines virtuelles](vminsights-maps.md). 
* Pour identifier les goulots d’étranglement, l’utilisation globale et les performances de votre machine virtuelle, consultez [Afficher les performances des machines virtuelles Azure](vminsights-performance.md).
