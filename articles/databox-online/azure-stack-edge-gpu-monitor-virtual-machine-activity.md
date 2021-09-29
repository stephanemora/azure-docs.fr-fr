---
title: Surveiller l’activité d’une machine virtuelle sur un appareil Azure Stack Edge Pro avec GPU
description: Découvrez comment surveiller l’activité des machines virtuelles sur un appareil Azure Stack Edge Pro GPU dans le portail Azure.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: aa1974347caa04c314e51b33daba47d7835026db
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124757607"
---
# <a name="monitor-vm-activity-on-your-azure-stack-edge-pro-gpu-device"></a>Surveiller l’activité d’une machine virtuelle sur votre appareil Azure Stack Edge Pro avec GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment afficher les journaux d’activité dans le portail Azure pour les machines virtuelles sur votre appareil Azure Stack Edge Pro GPU.

> [!NOTE]
> Vous pouvez effectuer un zoom avant sur l’utilisation de l’UC et de la mémoire de la machine virtuelle pendant les périodes d’activité sur l’onglet **Métriques** pour la machine virtuelle. Pour plus d’informations, voir [Surveiller les métriques de machine virtuelle](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md).

## <a name="view-activity-logs"></a>Afficher les journaux d’activité

Pour afficher les journaux d’activité des machines virtuelles sur votre appareil Azure Stack Edge Pro GPU, procédez comme suit :

1. Accédez à l’appareil, puis à **Machines virtuelles**. Sélectionnez **Journal d’activité**.

    ![Capture d’écran montrant le volet Journal d’activité pour les machines virtuelles sur un appareil Azure Stack Edge. Le journal d’activité est mis en surbrillance dans le volet de navigation.](./media/azure-stack-edge-gpu-monitor-virtual-machine-activity/activity-log-01.png)

    Vous verrez les journaux invités de machine virtuelle pour les machines virtuelles sur l’appareil.

1. Utilisez les filtres au-dessus de la liste pour cibler l’activité que vous voulez voir.

    ![Capture d’écran montrant les options d’intervalle de temps sur la page Journaux d’activité pour les machines virtuelles. L’intervalle de temps sélectionné est mis en surbrillance.](./media/azure-stack-edge-gpu-monitor-virtual-machine-activity/activity-log-02.png)<!--Reshoot to remove pointer. Lightbox treatment?-->

1. Cliquez sur la flèche vers le bas en regard d’un nom d’opération pour afficher l’activité associée.

    ![Capture d’écran montrant la page des journaux d’activité pour les machines virtuelles sur un appareil Azure Stack Edge. Une opération développée est mise en surbrillance dans la liste.](./media/azure-stack-edge-gpu-monitor-virtual-machine-activity/activity-log-03.png)<!--Reshoot to remove pointer. May be able to replace drop-down only.-->

Dans n’importe quel volet du **Journal d’activité** dans Azure, vous pouvez filtrer et trier les activités, sélectionner les colonnes à afficher, approfondir les détails d’une activité spécifique et obtenir des **Insights rapides** sur les erreurs, les échecs de déploiement, les alertes, l’intégrité des services et les changements de sécurité au cours des dernières 24 heures. Pour plus d’informations sur les journaux et les options de filtrage, voir [ Afficher les journaux d’activité ](../azure-monitor/essentials/activity-log.md).

## <a name="next-steps"></a>Étapes suivantes

- [Résoudre les problèmes de déploiement des machines virtuelles](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md).
- [Collecte des journaux invités des machines virtuelles dans le package de support](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md).