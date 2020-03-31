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
ms.openlocfilehash: ab07da38c01b052a4220274fb059683a22950a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750699"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migrer votre IoT Hub de la surveillance des opérations vers les paramètres de diagnostic

Les clients qui utilisent la [surveillance des opérations](iot-hub-operations-monitoring.md) pour effectuer le suivi de l’état des opérations dans IoT Hub peuvent migrer ce flux de travail vers les [paramètres de diagnostic Azure](../azure-monitor/platform/platform-logs-overview.md), une fonctionnalité d’Azure Monitor. Les paramètres de diagnostic fournissent des informations de diagnostic au niveau des ressources pour de nombreux services Azure.

**La fonctionnalité de surveillance des opérations d’IoT Hub est déconseillée** et a été supprimée du portail. Cet article montre les étapes à effectuer pour déplacer vos charges de travail de la surveillance des opérations vers les paramètres de diagnostic. Pour plus d’informations sur la chronologie de dépréciation, consultez l’article [Surveiller vos solutions Azure IoT avec Azure Monitor et Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Mettre à jour IoT Hub

Pour mettre à jour votre IoT Hub dans le portail Azure, activez d’abord les paramètres de diagnostic, puis désactivez la surveillance des opérations.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Désactiver la surveillance des opérations

> [!NOTE]
> À compter du 11 mars 2019, la fonctionnalité de surveillance des opérations est supprimée de l’interface du portail Azure d’IoT Hub. La procédure décrite ci-dessous ne s’applique plus. Pour effectuer une migration, assurez-vous que les catégories appropriées sont activées dans les paramètres de diagnostic Azure Monitor ci-dessus.

Une fois que vous avez testé les nouveaux paramètres de diagnostic sur votre flux de travail, vous pouvez désactiver la fonctionnalité de surveillance des opérations. 

1. Dans le menu de votre IoT Hub, sélectionnez **Surveillance des opérations**.

2. Sous chaque catégorie de surveillance, sélectionnez **Aucune**.

3. Enregistrez les modifications apportées à la surveillance des opérations.

## <a name="update-applications-that-use-operations-monitoring"></a>Mettre à jour les applications qui utilisent la surveillance des opérations

Les schémas de surveillance des opérations et de paramètres de diagnostic varient légèrement. Il est important que vous mettiez à jour les applications qui utilisent la surveillance des opérations dès aujourd’hui afin qu’elles mappent au schéma utilisé par les paramètres de diagnostic. 

De plus, les paramètres de diagnostic offrent cinq nouvelles catégories de suivi. Une fois que vous avez mis à jour les applications pour le schéma existant, ajoutez aussi les nouvelles catégories :

* Opérations jumelles cloud à appareil
* Opérations jumelles appareil à cloud
* Requêtes de jumeaux
* Opérations de travaux
* Méthodes directes

Pour plus de détails sur les structures de schéma spécifiques, consultez [Présentation du schéma pour les paramètres de diagnostic](iot-hub-monitor-resource-health.md#understand-the-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Supervision des événements de connexion et de déconnexion des appareils avec une faible latence

Pour surveiller les événements de connexion et de déconnexion des appareils dans les environnements de production, nous vous recommandons de vous abonner à [l’événement **Appareil déconnecté**](iot-hub-event-grid.md#event-types) sur Event Grid pour recevoir des alertes et suivre l’état de connexion de l’appareil. Suivez ce [tutoriel](iot-hub-how-to-order-connection-state-events.md) pour savoir comment intégrer les événements Appareil connecté et Appareil déconnecté d’IoT Hub à votre solution IoT.

## <a name="next-steps"></a>Étapes suivantes

[Surveiller l’intégrité d’Azure IoT Hub et diagnostiquer rapidement les problèmes](iot-hub-monitor-resource-health.md)
