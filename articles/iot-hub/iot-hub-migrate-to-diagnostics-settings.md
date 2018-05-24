---
title: Migrer IoT Hub Azure vers les paramètres de diagnostic | Microsoft Docs
description: Découvrez comment mettre à jour Azure IoT Hub pour utiliser les paramètres de diagnostic Azure plutôt que la surveillance des opérations pour surveiller l’état des opérations sur votre hub IoT en temps réel.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2017
ms.author: kgremban
ms.openlocfilehash: a46f6798a71c93ed769ae68877e72801d45b74a4
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2018
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migrer votre IoT Hub de la surveillance des opérations vers les paramètres de diagnostic

Les clients qui utilisent la [surveillance des opérations][lnk-opsmon] pour effectuer le suivi de l’état des opérations dans IoT Hub peuvent migrer ce flux de travail vers les [paramètres de diagnostic Azure][lnk-diagnostics-settings], une fonctionnalité d’Azure Monitor. Les paramètres de diagnostic fournissent des informations de diagnostic au niveau des ressources pour de nombreux services Azure.

La fonctionnalité de surveillance des opérations d’IoT Hub est dépréciée et sera supprimée ultérieurement. Cet article montre les étapes à effectuer pour déplacer vos charges de travail de la surveillance des opérations vers les paramètres de diagnostic. Pour plus d’informations sur le calendrier de dépréciation, consultez [Surveiller vos solutions Azure IoT avec Azure Monitor et Azure Resource Health][lnk-blog-announcement].

## <a name="update-iot-hub"></a>Mettre à jour IoT Hub

Pour mettre à jour votre IoT Hub dans le portail Azure, activez d’abord les paramètres de diagnostic, puis désactivez la surveillance des opérations.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Désactiver la surveillance des opérations

Une fois que vous avez testé les nouveaux paramètres de diagnostic sur votre flux de travail, vous pouvez désactiver la fonctionnalité de surveillance des opérations. 

1. Dans le menu de votre IoT Hub, sélectionnez **Surveillance des opérations**.
1. Sous chaque catégorie de surveillance, sélectionnez **Aucune**.
1. Enregistrez les modifications apportées à la surveillance des opérations.

## <a name="update-applications-that-use-operations-monitoring"></a>Mettre à jour les applications qui utilisent la surveillance des opérations

Les schémas de surveillance des opérations et de paramètres de diagnostic varient légèrement. Il est important que vous mettiez à jour les applications qui utilisent la surveillance des opérations dès aujourd’hui afin qu’elles mappent au schéma utilisé par les paramètres de diagnostic. 

De plus, les paramètres de diagnostic offrent un suivi pour cinq nouvelles catégories. Une fois que vous avez mis à jour les applications pour le schéma existant, ajoutez aussi les nouvelles catégories :

- Opérations jumelles cloud à appareil
- Opérations jumelles appareil-à-cloud
- Requêtes de jumeaux
- Opérations de travaux
- Méthodes directes

Pour plus de détails sur les structures de schéma spécifiques, consultez [Présentation du schéma pour les paramètres de diagnostic][lnk-diagnostics-schema].

## <a name="next-steps"></a>Étapes suivantes

- [Surveiller l’intégrité d’Azure IoT Hub et diagnostiquer rapidement les problèmes][lnk-monitor]

[lnk-opsmon]: iot-hub-operations-monitoring.md
[lnk-diagnostics-settings]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[lnk-diagnostics-schema]: iot-hub-monitor-resource-health.md#understand-the-logs
[lnk-blog-announcement]: https://azure.microsoft.com/blog/monitor-your-azure-iot-accelerators-with-azure-monitor-and-azure-resource-health
[lnk-monitor]: iot-hub-monitor-resource-health.md
