---
title: Configurer les alertes de disponibilité avec Azure Application Insights | Microsoft Docs
description: Configurez des tests web dans Application Insights. Recevez des alertes si un site web devient indisponible ou répond lentement.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: cc022f91d4b4fec42929769df8c979320548a1f9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304767"
---
# <a name="availability-alerts"></a>Alertes de disponibilité

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) envoie des requêtes web à votre application à intervalles réguliers à partir de différents points du monde, et vous alerte si votre application ne répond pas ou si elle répond trop lentement.

## <a name="enable-alerts"></a>Activer les alertes

Les alertes sont maintenant activées automatiquement par défaut, mais afin de configurer complètement l’alerte, vous devez tout d’abord créer initialement votre test de disponibilité.

![Créer l’expérience](./media/availability-alerts/create-test.png)

> [!NOTE]
>  Avec les [nouvelles alertes unifiées](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), le niveau de gravité et les préférences de notification des règles d’alerte des [groupes d’actions](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **doivent être** configurés dans l’expérience d’alertes. Sans les étapes suivantes, vous recevrez les notifications dans le portail uniquement.

1. Après avoir enregistré le test de disponibilité, sous l’onglet des détails, cliquez sur les points de suspension à côté du test que vous venez de faire. Cliquez sur « Modifier l’alerte ».

   ![Modifier après l’enregistrement](./media/availability-alerts/edit-alert.png)

2. Définissez le niveau de gravité et la description des règles souhaités, et surtout, le groupe d’actions disposant des préférences de notification que vous souhaitez utiliser pour cette règle d’alerte.

   ![Modifier après l’enregistrement](./media/availability-alerts/set-action-group.png)

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>L’alerte sur les emplacements de X en dehors de Y signalant des échecs

La règle d’alerte pour les emplacements de X en dehors de Y est activée par défaut dans l’[expérience des nouvelles alertes unifiées](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts) lorsque vous créez un nouveau test de disponibilité. Vous pouvez décliner en sélectionnant l'option « classique » ou en choisissant de désactiver la règle d'alerte.

> [!NOTE]
> Configurez les groupes d’actions pour recevoir des notifications lorsque l’alerte se déclenche en suivant les étapes ci-dessus. Sans cette étape, vous recevrez les notifications dans le portail uniquement lorsque la règle se déclenche.
>

### <a name="alert-on-availability-metrics"></a>Créer une alerte sur les mesures de disponibilité

À l’aide des [nouvelles alertes unifiées](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), vous pouvez créer une alerte sur les mesures de disponibilité des agrégats segmentés et sur la durée des tests :

1. Sélectionnez une ressource Application Insights dans l’expérience Mesures, puis sélectionnez une mesure de disponibilité :

    ![Sélection des mesures de disponibilité](./media/availability-alerts/select-metric.png)

2. L’option Configurer les alertes dans le menu vous redirigera vers la nouvelle expérience où vous pouvez sélectionner des tests ou des emplacements spécifiques sur lesquels il est possible de configurer une règle d’alerte. Ici, vous pouvez également configurer les groupes d’actions pour cette règle d’alerte.

### <a name="alert-on-custom-analytics-queries"></a>Créer des alertes sur les requêtes d’analytique personnalisées

À l’aide des [nouvelles alertes unifiées](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), vous pouvez créer des alertes sur les [requêtes dans les journaux personnalisées](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Avec des requêtes personnalisées, vous pouvez créer des alertes sur une condition arbitraire qui peut vous aider à obtenir le signal le plus fiable pour des problèmes de disponibilité. C’est également très pertinent si vous envoyez les résultats de disponibilité personnalisés à l’aide du Kit de développement logiciel (SDK) TrackAvailability. 

> [!Tip]
> Les mesures sur les données de disponibilité incluent tous les résultats de disponibilité personnalisés que vous pouvez soumettre en appelant notre Kit de développement logiciel (SDK) TrackAvailability. Vous pouvez utiliser la prise en charge de la création d’alertes sur les mesures pour créer des alertes sur les résultats de disponibilité personnalisés.
>

## <a name="troubleshooting"></a>Résolution de problèmes

Consultez l’[article dédié au dépannage](troubleshoot-availability.md).

## <a name="next-steps"></a>Étapes suivantes

* [Tests web à plusieurs étapes](availability-multistep.md)
* [Surveiller la disponibilité et de la réactivité d’un site web](monitor-web-app-availability.md)

