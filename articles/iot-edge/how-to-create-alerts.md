---
title: Être informé des problèmes à l’aide d’alertes – Azure IoT Edge
description: Utiliser des règles d’alerte Azure Monitor pour surveiller à grande échelle
author: veyalla
manager: philmea
ms.author: veyalla
ms.date: 06/08/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 14deb9a8a8ecaf67306ab8e29b2dfea7fa130c00
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904387"
---
# <a name="get-notified-about-issues-using-alerts-preview"></a>Être informé des problèmes à l’aide d’alertes (préversion)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Utilisez les [alertes de journal Azure Monitor](../azure-monitor/alerts/alerts-unified-log.md) pour surveiller les appareils IoT Edge à grande échelle. En surbrillance dans l’[architecture de la solution](how-to-collect-and-transport-metrics.md#architecture), Azure Monitor Log Analytics est utilisé comme base de données de métriques. Cette intégration déverrouille des capacités d’alerte puissantes et flexibles grâce à des alertes de journal centrées sur les ressources.

## <a name="create-an-alert-rule"></a>Création d'une règle d'alerte

Vous pouvez [créer une règle d’alerte de journal](../azure-monitor/alerts/alerts-log.md) pour surveiller un large éventail de conditions dans votre parc d’appareils.

Les exemples de requêtes d’alerte [KQL](https://aka.ms/kql) sont fournis dans la ressource IoT Hub. Les requêtes qui opèrent sur les données de métriques à partir des périphériques sont précédées de la mention *IoT Edge :* dans leur titre. Utilisez ces exemples tels quels ou modifiez-les si nécessaire pour créer une requête répondant exactement à vos besoins.

Pour accéder aux exemples de requêtes d’alerte, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub.
1. Sélectionnez **Journaux** dans la section **Supervision** du menu.
1. Sélectionnez **Requêtes** pour ouvrir l’exemple de navigateur de requêtes.

:::image type="content" source="./media/how-to-create-alerts/example-alerts.png" alt-text="Accédez à des exemples de requêtes d’alerte." lightbox="./media/how-to-create-alerts/example-alerts.png":::

Le [module metrics-collector](how-to-collect-and-transport-metrics.md#metrics-collector-module) ingère toutes les données dans la table [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) standard. Vous pouvez créer des règles d’alerte basées sur les données de métriques des modules personnalisés en interrogeant la même table.

### <a name="split-by-device-dimension"></a>Fractionner par dimension d’appareil

Tous les exemples de règles d’alerte interrogent les valeurs agrégées par ID de périphérique. Ce regroupement est nécessaire pour déterminer quel périphérique a déclenché l’alerte. Vous pouvez sélectionner des périphériques spécifiques pour activer la règle d’alerte ou l’activer sur tous les périphériques. Utilisez le graphique d’aperçu pour explorer la tendance par périphérique avant de définir la logique d’alerte.

### <a name="choose-notification-preferences"></a>Choisir les préférences de notification

Configurez vos préférences de notification dans un [groupe d’actions](../azure-monitor/alerts/action-groups.md) et associez-le à une règle d’alerte lors de la création d’une règle d’alerte.

## <a name="select-alert-rule-scope"></a>Sélectionner l’étendue d’une règle d’alerte

En utilisant l’aide de la section précédente, vous créez une règle d’alerte étendue à un seul hub IoT. Toutefois, il est possible de créer la même règle pour plusieurs hubs IoT. Modifiez l’étendue pour un groupe de ressources ou un abonnement entier afin d’activer la règle d’alerte sur tous les hubs de cette étendue.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub.
1. Sélectionnez **Journaux** dans la section **Supervision** du menu.
1. Sélectionnez **Sélectionner l’étendue** pour modifier l’étendue d’une règle d’alerte.

:::image type="content" source="./media/how-to-create-alerts/change-scope.png" alt-text="Modifier l’étendue des alertes" lightbox="./media/how-to-create-alerts/change-scope.png":::

Agrégez les valeurs par le champ `_ResourceId` et choisissez-le comme *Colonne d’ID de ressource* lors de la création de la règle d’alerte. Cette approche permettra d’associer une alerte à la bonne ressource pour plus de commodité.

## <a name="viewing-alerts"></a>Affichage des alertes

Consultez les alertes générées pour les appareils de plusieurs hubs IoT dans l’onglet **Alertes** du [classeur d’affichage du parc IoT Edge](how-to-explore-curated-visualizations.md#iot-edge-fleet-view-workbook).

Cliquez sur le nom de la règle d’alerte pour voir plus de contexte sur l’alerte. En cliquant sur le nom de l’appareil, vous obtiendrez les mesures détaillées de l’appareil au moment où l’alerte a été déclenchée.

## <a name="next-steps"></a>Étapes suivantes

Améliorez votre solution de supervision à l’aide de [métriques provenant de modules personnalisés](how-to-add-custom-metrics.md). 