---
title: Activer Azure Monitor pour une machine virtuelle ou un groupe de machines virtuelles identiques dans le portail Azure
description: Découvrez comment activer VM Insights sur une machine virtuelle ou un groupe de machines virtuelles identiques à l’aide du portail Azure.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 076fcab7b0747a7993407edd65f9d08efc27309f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035583"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>Activer Azure Monitor pour une machine virtuelle ou un groupe de machines virtuelles identiques dans le portail Azure
Cet article explique comment activer VM Insights pour une machine virtuelle ou un groupe de machines virtuelles identiques à l’aide du portail Azure. Cette procédure peut être utilisée pour les éléments suivants :

- Machine virtuelle Azure
- Groupe de machines virtuelles identiques Azure
- Machine virtuelle hybride connectée avec Azure Arc

## <a name="prerequisites"></a>Prérequis

- [Créer et configurer un espace de travail Log Analytics](./vminsights-configure-workspace.md) Au cours de ce processus, vous pouvez également créer un espace de travail.
- Pour vous assurer que le système d’exploitation de la machine virtuelle ou du groupe de machines virtuelles identiques que vous activez est pris en charge, consulter [Systèmes d’exploitation pris en charge](./vminsights-enable-overview.md#supported-operating-systems). 

## <a name="enable-vm-insights"></a>Activer VM Insights

Sur le portail Azure, sélectionnez **Machines virtuelles**, **Groupes de machines virtuelles identiques** ou **Serveurs - Azure Arc** et sélectionnez une ressource dans la liste. Dans la section **Supervision** du menu, sélectionnez **Insights**, puis **Activer**. L’exemple suivant montre une machine virtuelle Azure, mais le menu est similaire pour un groupe de machines virtuelles identiques ou Azure Arc.

![Activer VM Insights pour une machine virtuelle](media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

Si la machine virtuelle n’est pas encore connectée à un espace de travail Log Analytics, vous êtes invité à en sélectionner un. Si vous n’avez pas encore [créé d’espace de travail](../logs/quick-create-workspace.md), vous pouvez sélectionner une valeur par défaut pour l’emplacement où la machine virtuelle ou le groupe de machines virtuelles identiques sont déployés dans l’abonnement. Cet espace de travail sera créé et configuré s’il n’existe pas encore. Si vous sélectionnez un espace de travail existant, celui-ci est configuré pour VM Insights si ce n’était pas le cas.

> [!NOTE]
> Si vous sélectionnez un espace de travail non configuré précédemment pour VM Insights, le pack d’administration *VMInsights* est ajouté à cet espace de travail. Cela s’applique à tout agent déjà connecté à l’espace de travail, qu’il soit ou non activé pour VM Insights. Les données de performances seront collectées à partir de ces machines virtuelles et stockées dans la table *InsightsMetrics*.

![Sélectionner un espace de travail](media/vminsights-enable-portal/select-workspace.png)

Vous recevez des messages d’état à mesure que la configuration est effectuée.

>[!NOTE]
>Si vous utilisez un modèle de mise à niveau manuel pour votre groupe de machines virtuelles identiques, mettez à niveau les instances afin de terminer la configuration. Vous pouvez démarrer les mises à niveau depuis la page **Instances** de la section **Paramètres**.

![Activer le traitement du déploiement de la surveillance de VM Insights](media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Étapes suivantes

* Pour afficher les dépendances d’applications découvertes, consultez [Utiliser la Carte VM Insights](vminsights-maps.md). 
* Pour identifier les goulots d’étranglement, l’utilisation globale et les performances de votre machine virtuelle, consultez [Afficher les performances des machines virtuelles Azure](vminsights-performance.md).
