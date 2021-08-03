---
title: Alertes de journal de résolution de problèmes dans Azure Monitor | Microsoft Docs
description: Problèmes, erreurs et résolutions courants pour les règles d’alertes de journal dans Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f7e5340b69bacb14180b4feee7ada22c7ca298d0
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2021
ms.locfileid: "111592702"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Alertes de journal de résolution de problèmes dans Azure Monitor  

Cet article décrit comment résoudre les problèmes courants liés aux alertes de journal dans Azure Monitor. Il propose également des réponses aux problèmes courants liés à la fonctionnalité et à la configuration des alertes de journal.

Vous pouvez utiliser les alertes de journal pour évaluer les journaux de ressources à une fréquence définie en utilisant une requête [Log Analytics](../logs/log-analytics-tutorial.md) et déclencher une alerte basée sur les résultats. Les règles peuvent déclencher une ou plusieurs actions à l’aide des [groupes d’actions](./action-groups.md). Pour en savoir plus sur les fonctionnalités et la terminologie des alertes de journal, consultez [Alertes de journal dans Azure Monitor](alerts-unified-log.md).

> [!NOTE]
> Cet article ne prend pas en compte les cas où le portail Azure indique qu’une règle d’alerte a été déclenchée, mais qu’aucune notification n’a été reçue. Pour de tels cas, consultez [L’action ou la notification sur mon alerte n’a pas fonctionné comme prévu](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected).

## <a name="log-alert-didnt-fire"></a>L'alerte de journal ne s'est pas déclenchée

### <a name="data-ingestion-time-for-logs"></a>Durée d’ingestion de données pour les journaux d’activité

Azure Monitor traite des téraoctets de journaux de clients du monde entier, ce qui peut entraîner une [latence dans l’ingestion des journaux](../logs/data-ingestion-time.md).

Les journaux sont des données semi-structurées et ont, par nature, une latence plus importante que les métriques. Si vous constatez un retard de plus de quatre minutes dans les alertes déclenchées, vous devez envisager d’utiliser des [alertes de métrique](alerts-metric-overview.md). Vous pouvez envoyer des données au magasin de métriques à partir de journaux à l’aide d’[alertes de métrique pour les journaux](alerts-metric-logs.md).

Pour atténuer la latence, le système réitère l’évaluation de l’alerte plusieurs fois. Une fois les données reçues, l’alerte se déclenche, ce qui, dans la plupart des cas, ne correspond pas à l’heure d’enregistrement dans le journal.

### <a name="incorrect-query-time-range-configured"></a>Configuration d’un intervalle de temps de requête incorrect

L’intervalle de temps de requête est défini dans la définition de la condition de règle. Pour les espaces de travail et Application Insights, ce champ est appelé **Période**. Dans tous les autres types de ressources, il s’agit de **Remplacer l’intervalle de temps de la requête**. Comme dans Log Analytics, l’intervalle de temps limite les données de requête à la période spécifiée. Même si la commande **ago** est utilisée dans la requête, l’intervalle de temps s’applique. 

Par exemple, une requête analyse 60 minutes lorsque l’intervalle de temps est de 60 minutes, et ce, même si le texte contient **ago(1d)** . L’intervalle de temps et le filtrage du temps de requête doivent correspondre. Dans cet exemple, la modification du champ **Période** / **Remplacer l’intervalle de temps de la requête** à une journée fonctionne comme prévu.

![Période](media/alerts-troubleshoot-log/LogAlertTimePeriod.png)

### <a name="actions-are-muted-in-the-alert-rule"></a>Les actions sont en sourdine dans la règle d’alerte

Les alertes de journal offrent la possibilité de mettre en sourdine les actions d’alerte déclenchées pendant un laps de temps défini. Dans les espaces de travail et Application Insights, ce champ est appelé **Supprimer les alertes**. Dans tous les autres types de ressources, il s’agit de **Mettre les actions en sourdine**. 

Un problème fréquent est que vous pensez que l’alerte n’a pas déclenché les actions en raison d’un problème de service, même si elle a été mise en sourdine par la configuration de la règle.

![Supprimer les alertes](media/alerts-troubleshoot-log/LogAlertSuppress.png)

### <a name="alert-scope-resource-has-been-moved-renamed-or-deleted"></a>La ressource d’étendue de l’alerte a été déplacée, renommée ou supprimée

Lorsque vous créez une règle d’alerte, Log Analytics crée un instantané des autorisations pour votre identifiant utilisateur. Cet instantané est enregistré dans la règle et contient la ressource d’étendue de la règle, l’ID d’Azure Resource Manager. Si la ressource d’étendue de la règle est déplacée, renommée ou supprimée, toutes les règles d’alerte de journal qui font référence à cette ressource seront arrêtées. Pour fonctionner correctement, les règles d’alerte doivent être recréées en utilisant le nouvel ID d’Azure Resource Manager.

### <a name="metric-measurement-alert-rule-with-splitting-using-the-legacy-log-analytics-api"></a>Règle d’alerte de mesure de métrique avec fractionnement à l’aide de l’API Log Analytics héritée

[Mesure de métrique](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) est un type d’alerte de journal qui repose sur des résultats résumés de séries chronologiques. Vous pouvez utiliser ces règles pour regrouper par colonnes afin de [fractionner les alertes](alerts-unified-log.md#split-by-alert-dimensions). Si vous utilisez l’API Log Analytics héritée, le fractionnement ne fonctionne pas comme prévu, car il ne prend pas en charge le regroupement.

Vous pouvez utiliser l’API ScheduledQueryRules actuelle pour définir **Agrégation activée** dans les règles [Mesure de métrique](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value), qui fonctionnent comme prévu. Pour en savoir plus sur le passage à l’API ScheduledQueryRules actuelle, consultez [Mise à niveau vers l’API Alertes de journal actuelle à partir de l’API Alerte héritée de Log Analytics]](../alerts/alerts-log-api-switch.md).

## <a name="log-alert-fired-unnecessarily"></a>Alerte de journal déclenchée inutilement

Une [règle d’alerte de journal dans Azure Monitor](./alerts-log.md) configurée peut être déclenchée de façon inattendue. Les sections suivantes décrivent certaines raisons courantes.

### <a name="alert-triggered-by-partial-data"></a>Alerte déclenchée par des données partielles

Azure Monitor traite des téraoctets de journaux de clients du monde entier, ce qui peut entraîner une [latence dans l’ingestion des journaux](../logs/data-ingestion-time.md).

Les journaux sont des données semi-structurées et ont, par nature, une latence plus importante que les métriques. Si vous rencontrez de nombreux déclenchements d’alerte par erreur dans les alertes déclenchées, vous devez envisager d’utiliser des [alertes de métrique](alerts-metric-overview.md). Vous pouvez envoyer des données au magasin de métriques à partir de journaux à l’aide d’[alertes de métrique pour les journaux](alerts-metric-logs.md).

Les alertes de journal fonctionnent mieux lorsque vous tentez de détecter des données dans les journaux. Elles fonctionnent moins bien lorsque vous essayez de détecter l’absence de données dans les journaux, comme les alertes sur les pulsations des machines virtuelles. 

Il existe des capacités intégrées pour prévenir les fausses alertes, mais elles peuvent toujours se produire sur des données très latentes (plus de 30 minutes) et des données avec des pics de latence.

### <a name="query-optimization-issues"></a>Problèmes d’optimisation des requêtes

Le service d’alerte modifie votre requête pour optimiser la charge et la latence des alertes. Le flux d’alertes a été créé pour transformer les résultats qui indiquent le problème à une alerte. Par exemple, dans le cas d’une requête telle que :

``` Kusto
SecurityEvent
| where EventID == 4624
```

Si l’intention de l’utilisateur est d’alerter, quand ce type d’événement se produit, la logique d’alerte ajoute `count` à la requête. La requête qui sera exécutée est :

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

Il n’est pas nécessaire d’ajouter une logique d’alerte à la requête, et cela peut même poser des problèmes. Dans l’exemple précédent, si vous incluez `count` dans votre requête, vous obtiendrez toujours la valeur **1**, car le service d’alerte effectue une opération `count` de `count`.

Le service d’alerte de journal exécute la requête optimisée. Vous pouvez exécuter la requête modifiée dans le [portail](../logs/log-query-overview.md) ou l’[API](/rest/api/loganalytics/) de Log Analytics.

Pour les espaces de travail et Application Insights, elle est appelée **Requête à exécuter** dans le volet Condition. Dans tous les autres types de ressources, sélectionnez **Voir la requête d’alerte finale** dans l’onglet **Condition**.

![Requête à exécuter](media/alerts-troubleshoot-log/LogAlertPreview.png)

## <a name="log-alert-was-disabled"></a>L’alerte de journal a été désactivée.

Les sections suivantes mentionnent certaines raisons pour lesquelles Azure Monitor peut désactiver une règle d’alerte de journal. Après ces sections, vous trouverez un [exemple du journal d’activité envoyé lorsqu’une règle est désactivée](#activity-log-example-when-rule-is-disabled).

### <a name="alert-scope-no-longer-exists-or-was-moved"></a>L’étendue de l’alerte n’existe plus ou a été déplacée

Lorsque les ressources d’étendue d’une règle d’alerte ne sont plus valides, l’exécution de la règle échoue et la facturation s’arrête.

Si une alerte de journal échoue continuellement pendant une semaine, Azure Monitor la désactive.

### <a name="query-used-in-a-log-alert-isnt-valid"></a>La requête utilisée dans une alerte de journal n’est pas valide

Lorsqu’une règle d’alerte de journal est créée, la bonne syntaxe de la requête est vérifiée. Cependant, la requête fournie dans la règle d’alerte de journal peut parfois commencer à échouer. Voici quelques raisons courantes :

- Les règles ont été créées via l’API, et la validation a été ignorée par l’utilisateur.
- La requête [s’exécute sur plusieurs ressources](../logs/cross-workspace-query.md), et une ou plusieurs des ressources ont été supprimées ou déplacées.
- La [requête échoue](https://dev.loganalytics.io/documentation/Using-the-API/Errors), car :
    - La solution de journalisation n’a pas été [déployée sur l’espace de travail](../insights/solutions.md#install-a-monitoring-solution), de sorte que les tables ne sont pas créées.
    - Les données ont cessé d’alimenter une table de la requête depuis plus de 30 jours.
    - Les [tables de journaux personnalisés](../agents/data-sources-custom-logs.md) ne sont pas encore créées, car le flux de données n’a pas démarré.
- Les modifications apportées au [langage de requête](/azure/kusto/query/) incluent une révision du format des commandes et des fonctions, de sorte que la requête fournie précédemment n’est plus valide.

[Azure Advisor](../../advisor/advisor-overview.md) vous avertit de ce comportement. Le service ajoute une recommandation pour la règle d’alerte de journal concernée. La catégorie utilisée est « Haute disponibilité » avec un impact moyen et la description « Réparer votre règle d’alerte de journal pour garantir la supervision ».

## <a name="alert-rule-quota-was-reached"></a>Le quota de la règle d’alerte a été atteint

Pour plus d’informations sur le nombre de règles d’alerte de Recherche dans les journaux par abonnement et les limites maximales de ressources, consultez [Limites du service Azure Monitor](../service-limits.md).

### <a name="recommended-steps"></a>Étapes recommandées
    
Si vous avez atteint la limite de quota, les étapes suivantes peuvent vous aider à résoudre le problème.

1. Supprimez ou désactivez les règles d’alerte de recherche dans les journaux qui ne sont plus utilisées.
1. Utilisez le [fractionnement des alertes par dimensions](alerts-unified-log.md#split-by-alert-dimensions) pour réduire le nombre de règles. Ces règles peuvent analyser de nombreuses ressources et cas de détection.
1. Si vous avez besoin d’augmenter la limite de quota, continuez pour ouvrir une demande de support et fournissez les informations suivantes :

    - Les ID d’abonnement et les ID de ressource pour lesquels la limite de quota doit être augmentée
    - La raison de l’augmentation du quota
    - Le type de ressource pour l’augmentation de quota, comme **Log Analytics** ou **Application Insights**
    - La limite de quota demandée

### <a name="to-check-the-current-usage-of-new-log-alert-rules"></a>Pour vérifier l’utilisation actuelle des nouvelles règles d’alerte de journal
    
#### <a name="from-the-azure-portal"></a>À partir du portail Azure

1. Dans l’écran Alertes, sélectionnez **Gérer les règles d’alerte**.
1. Dans le contrôle de liste déroulante **Abonnement**, filtrez sur l’abonnement de votre choix. (Veillez à ne pas filtrer sur un groupe de ressources, un type de ressource ou une ressource spécifique.)
1. Dans le contrôle de liste déroulante **Type de signal**, sélectionnez **Recherche dans les journaux**.
1. Vérifiez que le contrôle de liste déroulante **État** est défini sur la valeur **Activé**.

Le nombre total de règles d’alerte de Recherche dans les journaux s’affiche au-dessus de la liste des règles.

#### <a name="from-api"></a>À partir de l’API

- PowerShell - [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule)
- API REST - [Répertorier par abonnement](/rest/api/monitor/scheduledqueryrules/listbysubscription)

## <a name="activity-log-example-when-rule-is-disabled"></a>Exemple de journal d’activité lorsque la règle est désactivée

Si la requête échoue pendant sept jours consécutifs, Azure Monitor désactive l’alerte de journal et arrête la facturation de la règle. Vous pouvez voir l’heure exacte à laquelle Azure Monitor a désactivé l’alerte de journal dans le [journal d’activité Azure](../../azure-resource-manager/management/view-activity-logs.md). 

Consultez l’exemple suivant :

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [alertes de journal dans Azure](./alerts-unified-log.md).
- En savoir plus sur la [configuration des alertes de journal](../logs/log-query-overview.md).
- Découvrez plus en détail les [requêtes dans les journaux](../logs/log-query-overview.md).
