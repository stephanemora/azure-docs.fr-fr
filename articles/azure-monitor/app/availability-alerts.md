---
title: Configurer les alertes de disponibilité avec Azure Application Insights | Microsoft Docs
description: Configurez des tests web dans Application Insights. Recevez des alertes si un site web devient indisponible ou répond lentement.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: a1637ac82c33c2a541bcec9f2848a248c29d56ca
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872670"
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

À l’aide des [nouvelles alertes unifiées](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), vous pouvez créer des alertes sur les [requêtes dans les journaux personnalisées](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Avec des requêtes personnalisées, vous pouvez créer des alertes sur une condition arbitraire qui peut vous aider à obtenir le signal le plus fiable pour des problèmes de disponibilité. Cela s’applique également si vous envoyez les résultats de disponibilité personnalisés à l’aide du Kit de développement logiciel (SDK) TrackAvailability.

> [!Tip]
> Les mesures sur les données de disponibilité incluent tous les résultats de disponibilité personnalisés que vous pouvez soumettre en appelant notre Kit de développement logiciel (SDK) TrackAvailability. Vous pouvez utiliser la prise en charge de la création d’alertes sur les mesures pour créer des alertes sur les résultats de disponibilité personnalisés.
>

## <a name="automate-alerts"></a>Automatiser les alertes

Pour automatiser ce processus à l’aide de modèles Azure Resource Manager, voir la [Créer une alerte de métrique avec un modèle Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert).

## <a name="troubleshooting"></a>Résolution de problèmes

Consultez l’[article dédié au dépannage](troubleshoot-availability.md).

## <a name="next-steps"></a>Étapes suivantes

* [Tests web à plusieurs étapes](availability-multistep.md)
* [Surveiller la disponibilité et de la réactivité d’un site web](monitor-web-app-availability.md)
