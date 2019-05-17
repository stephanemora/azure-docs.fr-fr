---
title: Activer Azure Monitor pour les machines virtuelles (version préliminaire) pour l’évaluation | Microsoft Docs
description: Cet article décrit comment activer des Azure Monitor pour les machines virtuelles pour une seule machine virtuelle Azure ou les machines virtuelles identiques à des fins d’évaluation.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: 673f153b551e4b0c89a564c96d6bd9819ca26f5d
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524084"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Activer Azure Monitor pour les machines virtuelles (version préliminaire) pour l’évaluation

Pour évaluer Azure Monitor pour les machines virtuelles (version préliminaire) sur un petit nombre de machines virtuelles ou une seule machine virtuelle ou les machines virtuelles identiques, la plus simple et la plus directe pour activer l’analyse consiste à partir du portail Azure. À la fin de ce processus, vous serez ont commencé correctement les surveiller et découvrez si elles rencontrent des problèmes de performances ou la disponibilité. 

Avant la mise en route, veillez à consulter le [conditions préalables](vminsights-enable-overview.md) et vérifier votre abonnement et vos ressources sont conformes à la configuration requise.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Activer l’analyse pour une seule machine virtuelle Azure
Pour activer la surveillance de votre machine virtuelle Azure à partir du portail Azure, effectuez les étapes suivantes :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Sélectionnez **Machines virtuelles**.

1. Sélectionnez une machine virtuelle dans la liste.

1. Sur la page de la machine virtuelle, dans la section **Surveillance**, sélectionnez **Insights (préversion)**.

1. Sur la page **Insights (préversion)**, sélectionnez **Essayer maintenant**.

    ![Activer Azure Monitor pour machines virtuelles pour une machine virtuelle](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. Dans la page **Intégration des insights de Azure Monitor**, si vous disposez d’un espace de travail Log Analytics appartenant au même abonnement, sélectionnez-le dans la liste déroulante.  

    La liste présélectionne l’espace de travail par défaut et l’emplacement où la machine virtuelle est déployée dans l’abonnement. 

    >[!NOTE]
    >Si vous souhaitez créer un nouvel espace de travail Analytique de journal pour stocker les données de surveillance de la machine virtuelle, suivez les instructions de [créer un espace de travail Analytique de journal](../../azure-monitor/learn/quick-create-workspace.md) dans une des régions prises en charge répertoriés [ici](vminsights-enable-overview.md#log-analytics).

Une fois que vous avez activé la supervision, 10 minutes peuvent s’écouler avant que vous ne puissiez voir les métriques d’intégrité de la machine virtuelle.

![Activer Azure Monitor pour machines virtuelles surveillant le traitement du déploiement](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Activer la surveillance pour un ensemble d’échelle de machine virtuelle unique

Pour activer la surveillance de votre machine virtuelle identique dans le portail Azure, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Sélectionnez **Virtual Machine Scale Sets**.

3. Dans la liste, sélectionnez un jeu de mise à l’échelle de machine virtuelle.

4. Sur la machine virtuelle identique page, dans le **surveillance** section, sélectionnez **Insights (version préliminaire)**.

5. Sur le **Insights (version préliminaire)** page, si vous disposez d’un espace de travail Analytique de journal existant, vous souhaitez utiliser, sélectionnez-le dans la liste déroulante.

    La liste présélectionne l’espace de travail par défaut et l’emplacement où la machine virtuelle est déployée dans l’abonnement. 

    ![Activer Azure Monitor pour les machines virtuelles pour un jeu de mise à l’échelle de machine virtuelle](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >Si vous souhaitez créer un nouvel espace de travail Analytique de journal pour stocker les données de surveillance de la machine virtuelle, suivez les instructions de [créer un espace de travail Analytique de journal](../learn/quick-create-workspace.md) dans une des régions prises en charge répertoriés [ici](vminsights-enable-overview.md#log-analytics).

Une fois que vous avez activé la surveillance, il peut prendre environ 10 minutes avant de pouvoir afficher les données d’analyse pour le groupe identique.

>[!NOTE]
>Si vous utilisez un modèle de mise à niveau manuels de votre groupe identique, vous devrez mettre à niveau les instances pour terminer l’installation.  Cela est possible à partir de la page Instances sous la **paramètres** section.

![Activer Azure Monitor pour machines virtuelles surveillant le traitement du déploiement](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que l’analyse est activée pour votre machine virtuelle ou les machines virtuelles identiques, ces informations sont disponibles pour l’analyse avec Azure Monitor pour les machines virtuelles. Pour savoir comment utiliser la fonctionnalité de contrôle de l’intégrité, consultez [Comprendre l’intégrité de vos machines virtuelles Azure grâce à Azure Monitor pour machines virtuelles](vminsights-health.md). Pour afficher les dépendances des applications détectées, consultez [Utilisation de la fonctionnalité Map d’Azure Monitor pour machines virtuelles dans le but de comprendre les composants d’application](vminsights-maps.md). Pour identifier les goulots d’étranglement et les performances d’utilisation globale avec vos machines virtuelles, consultez l’article indiquant comment [afficher les performances avec Azure Monitor pour les machines virtuelles](vminsights-performance.md). Pour visualiser les dépendances d’application découvertes, consultez l’article expliquant comment [afficher la fonctionnalité Map d’Azure Monitor pour les machines virtuelles](vminsights-maps.md).