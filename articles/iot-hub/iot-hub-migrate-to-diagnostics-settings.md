---
title: Migrer IoT Hub Azure vers les paramètres de diagnostic | Microsoft Docs
description: Découvrez comment mettre à jour Azure IoT Hub pour utiliser les paramètres de diagnostic Azure plutôt que la surveillance des opérations servant à superviser l’état des opérations sur votre hub IoT en temps réel.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: b6cde8402c699a7477cd0efc79a44b3f5e150ad0
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792648"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migrer votre IoT Hub de la surveillance des opérations vers les paramètres de diagnostic

Les clients qui utilisent la [surveillance des opérations](iot-hub-operations-monitoring.md) pour effectuer le suivi de l’état des opérations dans IoT Hub peuvent migrer ce flux de travail vers les [paramètres de diagnostic Azure](../azure-monitor/platform/diagnostic-logs-overview.md), une fonctionnalité d’Azure Monitor. Les paramètres de diagnostic fournissent des informations de diagnostic au niveau des ressources pour de nombreux services Azure.

**Les opérations de fonctionnalités d’analyse de IoT Hub sont déconseillée**et a été supprimée à partir du portail. Cet article montre les étapes à effectuer pour déplacer vos charges de travail de la surveillance des opérations vers les paramètres de diagnostic. Pour plus d’informations sur la chronologie de dépréciation, consultez l’article [Surveiller vos solutions Azure IoT avec Azure Monitor et Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Mettre à jour IoT Hub

Pour mettre à jour votre IoT Hub dans le portail Azure, activez d’abord les paramètres de diagnostic, puis désactivez la surveillance des opérations.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Désactiver la surveillance des opérations

> [!NOTE]
> Comme de 11 mars 2019, les opérations de fonctionnalité de surveillance est supprimée de l’interface du portail Azure d’IoT Hub. Les étapes ci-dessous ne s’appliquent plus. Pour effectuer la migration, assurez-vous que les catégories appropriées sont activées dans les paramètres de diagnostic Azure Monitor ci-dessus.

Une fois que vous testez les nouveaux paramètres de diagnostics dans votre flux de travail, vous pouvez désactiver les fonctionnalité de la surveillance des opérations. 

1. Dans le menu de votre IoT Hub, sélectionnez **Surveillance des opérations**.

2. Sous chaque catégorie de surveillance, sélectionnez **Aucune**.

3. Enregistrez les modifications apportées à la surveillance des opérations.

## <a name="update-applications-that-use-operations-monitoring"></a>Mettre à jour les applications qui utilisent la surveillance des opérations

Les schémas de surveillance des opérations et de paramètres de diagnostic varient légèrement. Il est important que vous mettiez à jour les applications qui utilisent la surveillance des opérations dès aujourd’hui afin qu’elles mappent au schéma utilisé par les paramètres de diagnostic. 

En outre, les paramètres de diagnostic propose cinq nouvelles catégories pour le suivi. Une fois que vous avez mis à jour les applications pour le schéma existant, ajoutez aussi les nouvelles catégories :

* Opérations jumelles cloud à appareil
* Opérations jumelles appareil-à-cloud
* Requêtes de jumeaux
* Opérations de travaux
* Méthodes directes

Pour plus de détails sur les structures de schéma spécifiques, consultez [Présentation du schéma pour les paramètres de diagnostic](iot-hub-monitor-resource-health.md#understand-the-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Supervision des événements de connexion et de déconnexion des appareils avec une faible latence

Pour surveiller les appareil vous connecter et déconnecter les événements en production, nous vous recommandons de s’abonner à la [ **périphérique déconnecté** événement](iot-hub-event-grid.md#event-types) sur Event Grid pour recevoir des alertes et de surveiller l’état de connexion de périphérique. Suivez ce [tutoriel](iot-hub-how-to-order-connection-state-events.md) pour savoir comment intégrer les événements Appareil connecté et Appareil déconnecté d’IoT Hub à votre solution IoT.

## <a name="next-steps"></a>Étapes suivantes

[Surveiller l’intégrité d’Azure IoT Hub et diagnostiquer rapidement les problèmes](iot-hub-monitor-resource-health.md)
