---
title: Activer Azure Monitor pour machines virtuelles (préversion) pour l’évaluation | Microsoft Docs
description: Apprenez à évaluer Azure Monitor pour machines virtuelles sur une seule machine virtuelle Azure ou sur un groupe de machines virtuelles identiques.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: c77fabf0220ecfcb889ce7cb71fa075fde27226f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75400594"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Activer Azure Monitor pour machines virtuelles (préversion) pour l’évaluation

Vous pouvez évaluer Azure Monitor pour machines virtuelles (préversion) sur un petit nombre de machines virtuelles Azure ou sur un groupe de machines virtuelles identiques. La façon la plus simple et la plus directe d’activer la surveillance est d’utiliser le portail Azure. Votre objectif est de surveiller vos machines virtuelles et de découvrir tout problème de performances ou de disponibilité. 

Avant de commencer, consultez les [conditions préalables](vminsights-enable-overview.md) et vérifiez que votre abonnement et vos ressources répondent aux exigences.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Activer la surveillance pour une machine virtuelle Azure
Pour activer la surveillance pour votre machine virtuelle Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Machines virtuelles**.

1. Sélectionnez une machine virtuelle dans la liste.

1. Sur la page de la machine virtuelle, dans la section **Surveillance**, sélectionnez **Insights (préversion)** .

1. Sur la page **Insights (préversion)** , sélectionnez **Essayer maintenant**.

    ![Activer Azure Monitor pour machines virtuelles pour une machine virtuelle](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Dans la page **Intégration des insights de Azure Monitor**, si vous disposez d’un espace de travail Log Analytics appartenant au même abonnement, sélectionnez-le dans la liste déroulante.  

    La liste présélectionne l’espace de travail par défaut et l’emplacement où la machine virtuelle est déployée dans l’abonnement. 

    >[!NOTE]
    >Pour créer un espace de travail Log Analytics pour stocker les données de surveillance de la machine virtuelle, consultez [Créer un espace de travail Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Votre espace de travail Log Analytics doit appartenir à l’une des [régions prises en charge](vminsights-enable-overview.md#log-analytics).

Une fois que vous avez activé la supervision, 10 minutes peuvent s’écouler avant que vous ne puissiez voir les métriques d’intégrité de la machine virtuelle.

![Activer Azure Monitor pour machines virtuelles surveillant le traitement du déploiement](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Activer la surveillance pour un groupe de machines virtuelles identiques

Pour activer la surveillance pour un groupe de machines virtuelles identiques :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Sélectionnez **Groupes de machines virtuelles identiques**.

3. Dans la liste, sélectionnez un groupe de machines virtuelles identiques.

4. Sur la page du groupe de machines virtuelles identiques, dans la section **Surveillance**, sélectionnez **Insights (préversion)** .

5. Si vous voulez utiliser un espace de travail Log Analytics existant, sélectionnez-le dans la liste déroulante sur la page **Insights (préversion)** .

    La liste présélectionne l’espace de travail par défaut et l’emplacement où la machine virtuelle est déployée dans l’abonnement. 

    ![Activer Azure Monitor pour machines virtuelles pour un groupe de machines virtuelles identiques](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Pour créer un espace de travail Log Analytics pour stocker les données de surveillance du groupe de machines virtuelles identiques, consultez [Créer un espace de travail Log Analytics](../learn/quick-create-workspace.md). Votre espace de travail Log Analytics doit appartenir à l’une des [régions prises en charge](vminsights-enable-overview.md#log-analytics).

Une fois que vous avez activé la supervision, 10 minutes peuvent s’écouler avant que vous ne puissiez voir les données de surveillance du groupe identique.

>[!NOTE]
>Si vous utilisez un modèle de mise à niveau manuel pour votre groupe identique, mettez à jour les instances afin de terminer la configuration. Vous pouvez démarrer les mises à niveau depuis la page **Instances** de la section **Paramètres**.

![Activer Azure Monitor pour machines virtuelles surveillant le traitement du déploiement](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

À présent que vous avez activé la surveillance de votre machine virtuelle ou de votre groupe de machines virtuelles identiques, les informations de surveillance sont disponibles pour analyse dans Azure Monitor pour machines virtuelles. 

## <a name="next-steps"></a>Étapes suivantes

* Pour afficher les dépendances des applications détectées, consultez [Utilisation de la fonctionnalité Map d’Azure Monitor pour machines virtuelles](vminsights-maps.md). 
* Pour identifier les goulots d’étranglement, l’utilisation globale et les performances de votre machine virtuelle, consultez [Afficher les performances des machines virtuelles Azure](vminsights-performance.md).
