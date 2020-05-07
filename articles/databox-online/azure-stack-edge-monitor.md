---
title: Superviser votre appareil Azure Stack Edge | Microsoft Docs
description: Décrit comment utiliser le portail Azure et l’interface utilisateur web locale pour superviser votre appareil Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 2fc7435988a07968e65aaf265c33878c6e72e85b
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82568642"
---
# <a name="monitor-your-azure-stack-edge"></a>Superviser votre appareil Azure Stack Edge

Cet article explique comment superviser votre appareil Azure Stack Edge. Pour ce faire, vous pouvez utiliser le portail Azure ou l’interface utilisateur web locale. Utilisez le portail Azure pour voir les événements de l’appareil, configurer et gérer les alertes et voir les métriques. Utilisez l’interface utilisateur web locale sur votre appareil physique pour voir l’état des différents composants matériels de l’appareil.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Afficher les événements de l’appareil et les alertes correspondantes
> * Afficher l’état des composants matériels de l’appareil
> * Afficher les métriques de capacité et de transaction de votre appareil
> * Configurer et gérer les alertes

## <a name="view-device-events"></a>Afficher les événements de l’appareil

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Afficher l’état de matériel

Effectuez les étapes suivantes dans l’interface utilisateur web locale pour afficher l’état des composants matériel de votre appareil.

1. Connectez-vous à l’interface utilisateur web locale de votre appareil.
2. Allez dans **Maintenance > État du matériel**. Vous pouvez afficher l’intégrité des différents composants de l’appareil. 

    ![Afficher l’état de matériel](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Afficher les mesures

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Gérer les alertes

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Étapes suivantes 

Découvrez comment [gérer la bande passante](azure-stack-edge-manage-bandwidth-schedules.md).