---
title: Superviser le processeur et la mémoire des machines virtuelles sur un appareil GPU Azure Stack Edge Pro
description: Découvrez comment superviser les métriques du processeur et de la mémoire des machines virtuelles sur les appareils GPU Azure Stack Edge Pro dans le portail Azure.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: c58b7fd1cf4ab08dd8d645e4fc5f537d9ee937e3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524794"
---
# <a name="monitor-vm-metrics-for-cpu-memory-on-azure-stack-edge-pro-gpu"></a>Superviser les métriques du processeur et de la mémoire des machines virtuelles sur un appareil GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment superviser les métriques du processeur et de la mémoire d’une machine virtuelle sur votre appareil GPU Azure Stack Edge Pro.

## <a name="about-vm-metrics"></a>À propos des métriques des machines virtuelles

Sous l’onglet **Métriques** pour une machine virtuelle, vous pouvez voir les métriques du processeur et de la mémoire, ajuster la période examinée et afficher les détails d’un moment en particulier.

Les métriques de la machine virtuelle sont basées sur les données d’utilisation du processeur et de la mémoire qui sont collectées à partir du système d’exploitation invité de la machine virtuelle. L’utilisation des ressources est échantillonnée chaque minute.

Si un appareil est déconnecté, les métriques sont mises en cache sur l’appareil. Quand l’appareil est de nouveau connecté, les métriques sont tirées du cache, et l’onglet **Métriques** pour la machine virtuelle est mis à jour.

## <a name="monitor-cpu-and-memory-metrics"></a>Superviser les métriques du processeur et de la mémoire

1. Ouvrez l’appareil dans le portail Azure et accédez à **Machines virtuelles**. Sélectionnez la machine virtuelle, puis sélectionnez **Métriques**.

    ![Capture d’écran montrant l’onglet Métriques pour une machine virtuelle sur un appareil Azure Stack Edge. L’onglet Métriques est mis en évidence.](media/azure-stack-edge-gpu-monitor-virtual-machine-metrics/metrics-01.png)

2. Par défaut, les graphiques indiquent l’utilisation moyenne du processeur et de la mémoire durant l’heure précédente. Pour voir les données d’une période différente, sélectionnez une autre option à côté de **Afficher les données du/de la dernier(ère)** .

    ![Capture d’écran de l’onglet Métriques pour une machine virtuelle sur un appareil Azure Stack Edge. L’option Afficher les données du/de la dernier(ère) et la valeur sélectionnée sont mises en évidence.](./media/azure-stack-edge-gpu-monitor-virtual-machine-metrics/metrics-02.png)

3. Pointez n’importe où dans un graphique à l’aide de la souris pour afficher une ligne verticale avec une main, que vous pouvez déplacer vers la gauche ou la droite pour voir un échantillon de données d’une période antérieure ou postérieure. Cliquez pour ouvrir une vue détaillée de cette période.

    ![Capture d’écran montrant l’onglet Métriques pour une machine virtuelle. Le pointeur qui s’affiche lorsque vous pointez sur une zone d’un graphique est mis en évidence.](./media/azure-stack-edge-gpu-monitor-virtual-machine-metrics/metrics-03.png)


## <a name="next-steps"></a>Étapes suivantes

- [Superviser l’activité des machines virtuelles sur votre appareil](azure-stack-edge-gpu-monitor-virtual-machine-activity.md).
- [Collecter des journaux invités des machines virtuelles dans le package de support](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md).
