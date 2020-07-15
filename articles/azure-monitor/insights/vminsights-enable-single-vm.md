---
title: Activer Azure Monitor pour machines virtuelles sur le portail Azure
description: Apprenez à évaluer Azure Monitor pour machines virtuelles sur une seule machine virtuelle Azure ou sur un groupe de machines virtuelles identiques.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 4cdb9390b3146df74f2cbe8eba7b170a5d11fb2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85507056"
---
# <a name="enable-azure-monitor-for-single-vm-or-vmss-in-the-azure-portal"></a>Activer Azure Monitor pour un machine virtuelle unique ou un VMSS dans le portail Azure
Cet article explique comment activer Azure Monitor pour machines virtuelles sur une machine virtuelle unique ou un groupe de machines virtuelles identiques à l’aide du portail Azure. Cette procédure peut être utilisée pour les éléments suivants :

- Machine virtuelle Azure
- Groupe de machines virtuelles identiques Azure
- Machine Azure Arc

Avant de commencer, consultez les [conditions préalables](vminsights-enable-overview.md) et vérifiez que votre abonnement et vos ressources répondent aux exigences.  

## <a name="enable-azure-monitor-for-vms"></a>Activer Azure Monitor pour machines virtuelles

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Machines virtuelles**, **Groupes de machines virtuelles identiques** ou **Machines - Azure Arc**.

1. Sélectionnez une ressource dans la liste.

1. Dans la section **Supervision** du menu, sélectionnez **Insights**, puis **Activer**. L’exemple suivant montre une machine virtuelle Azure, mais le menu est similaire pour Azure VMSS ou Azure Arc.

    ![Activer Azure Monitor pour machines virtuelles pour une machine virtuelle](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Si la machine virtuelle n’est pas encore connectée à un espace de travail Log Analytics, vous êtes invité à en sélectionner un. Si vous n’avez pas encore [créé d’espace de travail](../../azure-monitor/learn/quick-create-workspace.md), vous pouvez sélectionner une valeur par défaut pour l’emplacement où la machine virtuelle ou le VMSS est déployé dans l’abonnement. Cet espace de travail sera créé et configuré s’il n’existe pas encore.

2. Vous recevez des messages d’état à mesure que la configuration est effectuée.

    >[!NOTE]
    >Si vous utilisez un modèle de mise à niveau manuel pour votre groupe identique, mettez à jour les instances afin de terminer la configuration. Vous pouvez démarrer les mises à niveau depuis la page **Instances** de la section **Paramètres**.

    ![Activer Azure Monitor pour machines virtuelles surveillant le traitement du déploiement](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Étapes suivantes

* Pour afficher les dépendances des applications détectées, consultez [Utilisation de la fonctionnalité Map d’Azure Monitor pour machines virtuelles](vminsights-maps.md). 
* Pour identifier les goulots d’étranglement, l’utilisation globale et les performances de votre machine virtuelle, consultez [Afficher les performances des machines virtuelles Azure](vminsights-performance.md).
