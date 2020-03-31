---
title: Alertes de journal de résolution de problèmes dans Azure Monitor | Microsoft Docs
description: Problèmes, erreurs et résolutions courants pour les règles d’alertes de journal dans Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: acb9784b745fa90fc9cd264162930020e6d64751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79226409"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Alertes de journal de résolution de problèmes dans Azure Monitor  

Cet article vous explique comment résoudre des problèmes courants pouvant survenir lors de la configuration d'alertes de journal dans Azure Monitor. Il propose également des réponses aux problèmes courants de fonctionnalité ou de configuration des alertes de journal.

Le terme *alertes de journal* décrit les règles de déclenchement basées sur une requête de journal dans un [espace de travail Azure Log Analytics](../learn/tutorial-viewdata.md) ou dans [Azure Application Insights](../../azure-monitor/app/analytics.md). En savoir plus sur la fonctionnalité, la terminologie et les types dans [Alertes de journal dans Azure Monitor](../platform/alerts-unified-log.md).

> [!NOTE]
> Cet article ne couvre pas les cas où le portail Microsoft Azure affiche une règle d’alerte déclenchée et où une notification n’est pas effectuée via un groupe d'actions associé. Dans ces cas-là, consultez les détails fournis sous [Créer et gérer des groupes d’actions dans le portail Azure](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>L'alerte de journal ne s'est pas déclenchée

Voici quelques-unes des raisons pour lesquelles l’état d’une [règle d’alerte de journal dans Azure Monitor](../platform/alerts-log.md) ne s’affiche pas [comme étant *déclenché* lorsque cela est prévu](../platform/alerts-managing-alert-states.md).

### <a name="data-ingestion-time-for-logs"></a>Durée d’ingestion de données pour les journaux d’activité

Une alerte de journal exécute régulièrement votre requête basée sur [Log Analytics](../learn/tutorial-viewdata.md) ou [Application Insights](../../azure-monitor/app/analytics.md). Comme Azure Monitor traite plusieurs téraoctets de données issues de milliers de clients provenant de sources variées partout dans le monde, le service peut nécessiter certains délais. Pour plus d’informations, consultez [Durée d’ingestion de données dans les journaux Azure Monitor](../platform/data-ingestion-time.md).

Pour réduire les délais, le système attend et relance plusieurs fois la requête d’alerte s’il détecte que les données nécessaires n’ont pas encore été ingérées. Le temps d’attente du système est défini de manière exponentielle. Comme l’alerte de journal est déclenchée uniquement une fois que les données sont disponibles, le délai peut être dû à la lenteur de l’ingestion des données du journal.

### <a name="incorrect-time-period-configured"></a>Configuration d'une période incorrecte

Comme décrit dans l’article relatif à la [terminologie des alertes de journal](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), la période indiquée dans la configuration spécifie l'intervalle de temps de la requête. La requête renvoie uniquement les enregistrements créés dans cet intervalle.

La période limite les données extraites pour une requête de journal afin d’empêcher les abus et contourne toute commande de temps (comme **il y a**) utilisée dans une requête de journal. Par exemple, si la période est définie sur 60 minutes et la requête exécutée à 13 h 15, seuls les enregistrements créés entre 12 h 15 et 13 h 15 sont utilisés pour la requête de journal. Si la requête de journal utilise une commande de temps telle que **il y a (1j)** , la requête continue d’utiliser les données collectées entre 12 h 15 et 13 h 15, car la période est définie sur cet intervalle.

Vérifiez dans la configuration que cette période correspond à votre requête. Dans l'exemple précédent, si la requête de journal utilise **il y a (1j)** avec la marque verte, la période doit être définie sur 24 heures ou 1 440 minutes (indiqué en rouge). Ce paramètre garantit que la requête s’exécute comme prévu.

![Période](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Activer l'option Supprimer les alertes

Comme décrit à l’étape 8 de l’article relatif à la [création d’une règle d’alerte de journal dans le portail Microsoft Azure](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal), les alertes de journal proposer une option **Supprimer les alertes** permettant de supprimer les actions de déclenchement et de notification pendant un laps de temps défini. Par conséquent, vous pourriez penser qu’une alerte ne s’est pas déclenchée. En fait, elle a été déclenchée, mais supprimée.  

![Supprimer les alertes](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>La règle d’alerte Mesure métrique est incorrecte

Les *alertes de journal Mesure métrique* représentent un sous-type d’alertes de journal dotées de fonctionnalités spécifiques et d’une syntaxe de requête d’alerte limitée. Une règle pour une alerte de journal de mesure métrique requiert que le résultat de la requête soit une série chronologique métrique. Autrement dit, la sortie est une table avec des périodes distinctes de taille égale ainsi que des valeurs agrégées correspondantes.

Vous pouvez choisir d’ajouter d’autres variables à la table en plus de **AggregatedValue**. Ces variables peuvent être utilisées pour trier la table.

Par exemple, supposons qu’une règle pour une alerte de journal de mesure métrique soit configurée comme suit :

- Requête de `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- Période de 6 heures
- Seuil de 50
- Logique d’alerte de trois violations consécutives
- **Aggregate Upon** sélectionné en tant que **$table**

Étant donné que la commande inclut **summarize … by** et fournit deux variables (**timestamp** et **$table**), le système choisit **$table** avec la valeur **Aggregate Upon**. Le système trie la table de résultats par le champ **$table**, comme le montre la capture d’écran suivante. Ensuite il examine les multiples instances **AggregatedValue** pour chaque type de table (comme **availabilityResults**) pour voir s’il y a au moins trois violations consécutives.

![Exécution de la requête de mesure métrique avec plusieurs valeurs](media/alert-log-troubleshoot/LogMMQuery.png)

Étant donné qu’**Aggregate Upon** est défini sur **$table**, les données sont triées sur une colonne **$table** (indiquée en rouge). Ensuite, nous regroupons et recherchons des types du champ **Aggregate Upon**.

Par exemple, pour **$table**, les valeurs pour **availabilityResults** sont considérées comme un tracé/une entité (indiqués en orange). Dans cette valeur tracé/entité, le service d’alerte recherche trois violations consécutives (indiquées en vert). Les violations déclenchent une alerte pour la valeur de table **availabilityResults**.

De même, si trois violations consécutives se produisent pour toute autre valeur de **$table**, une autre notification d’alerte est déclenchée pour la même chose. Le service d’alerte trie automatiquement les valeurs dans un tracé/une entité (indiqués en orange) par heure.

Supposons maintenant que la règle d’alerte de journal de mesure métrique a été modifiée et que la requête était `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`. Le reste de la configuration est resté le même qu’avant, notamment la logique d’alerte pour trois violations consécutives. Dans ce cas, l’option **Aggregate Upon** est **timestamp** par défaut. Une seule valeur est fournie dans la requête pour **résumer ... par** (à savoir **timestamp**). Comme dans l’exemple précédent, la sortie à la fin de l’exécution serait conforme à l’illustration ci-dessous.

   ![Exécution de la requête de mesure métrique avec valeur singulière](media/alert-log-troubleshoot/LogMMtimestamp.png)

Étant donné qu’**Aggregate Upon** est défini sur **timestamp**, les données sont triées sur une colonne **timestamp** (indiquée en rouge). Puis nous regroupons par **timestamp**. Par exemple, les valeurs pour `2018-10-17T06:00:00Z` sont considérées comme un tracé/une entité (indiqués en orange). Dans cette valeur de tracé/d’entité, le service d’alerte ne détectera aucune violation consécutive (parce que chaque valeur **timestamp** n’a qu’une entrée). Par conséquent, l’alerte n’est jamais déclenchée. Dans ce cas, l’utilisateur doit :

- ajouter une variable factice ou une variable existante (par exemple, **$table**) pour trier correctement à l’aide du champ **Aggregate Upon** ;
- reconfigurer la règle d’alerte pour utiliser la logique d’alerte basée sur **violation totale** à la place.

## <a name="log-alert-fired-unnecessarily"></a>Alerte de journal déclenchée inutilement

Une [règle d’alerte de journal dans Azure Monitor](../platform/alerts-log.md) configurée peut être déclenchée de façon inattendue lorsque vous l’affichez dans les [alertes Azure](../platform/alerts-managing-alert-states.md). Les sections suivantes décrivent certaines raisons courantes.

### <a name="alert-triggered-by-partial-data"></a>Alerte déclenchée par des données partielles

Log Analytics et Application Insights subissent un traitement et des retards d’ingestion. Lorsque vous exécutez une requête d’alerte de journal, vous constatez peut-être qu’aucune donnée n’est disponible, ou que seules certaines données sont disponibles. Pour plus d’informations, consultez [Durée d’ingestion de données de journal dans Azure Monitor](../platform/data-ingestion-time.md).

Selon la façon dont vous avez configuré la règle d’alerte, un mauvais déclenchement est possible en l’absence partielle ou totale de données dans les journaux d’activité au moment de l’exécution de l’alerte. Dans ce cas, nous vous conseillons de modifier la requête d’alerte ou la configuration.

Par exemple, si vous configurez la règle d’alerte de journal pour un déclenchement lorsque le nombre de résultats d’une requête analytique est inférieur à 5, l’alerte est déclenchée en l’absence de données (zéro enregistrement) ou de résultats partiels (un enregistrement). Mais après le délai d’ingestion des données, la même requête avec les données complètes peut arriver à un résultat de 10 enregistrements.

### <a name="alert-query-output-is-misunderstood"></a>Les résultats de la requête d'alerte sont pas compris.

Vous fournissez la logique des alertes de journal dans une requête analytique. La requête analytique peut utiliser différentes fonctions de Big Data et différentes fonctions mathématiques. Le service d’alerte exécute votre requête selon les intervalles spécifiés avec les données pour une période définie. Le service d’alerte apporte de petites modifications à la requête en fonction du type d’alerte. Vous pouvez afficher cette modification dans la section **Requête à exécuter** sur l’écran **Configurer la logique du signal** :

![Requête à exécuter](media/alert-log-troubleshoot/LogAlertPreview.png)

La zone **Requête à exécuter** correspond à ce que le service d’alerte de journal exécute. Si vous souhaitez comprendre ce que le résultat de la requête d’alerte peut être avant que vous ne créiez l’alerte, vous pouvez exécuter la requête indiquée, ainsi que les intervalles, via le [portail Analytics](../log-query/portals.md) ou l’[API Analytics](https://docs.microsoft.com/rest/api/loganalytics/).

## <a name="log-alert-was-disabled"></a>L’alerte de journal a été désactivée.

Les sections suivantes mentionnent certaines raisons pour lesquelles Azure Monitor peut désactiver la [règle d’alerte du journal](../platform/alerts-log.md).

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>La ressource où l’alerte a été créée n’existe plus.

Les règles d’alerte de journal créées dans Azure Monitor ciblent une ressource spécifique, telle qu’un espace de travail Log Analytics, une application Azure Application Insights et une ressource Azure. Le service d’alerte de journal exécute alors une requête analytique fournie dans la règle pour la cible spécifiée. Mais après la création de la règle, les utilisateurs suppriment généralement la cible de l’alerte de journal à partir d’Azure ou la déplacent au sein d’Azure. Comme la cible de la règle d’alerte n’est plus valide, l’exécution de la règle échoue.

Dans ce cas, Azure Monitor désactive l’alerte de journal et empêche que vous soyez facturé inutilement lorsque la règle ne peut pas s’exécuter en permanence pendant une période prolongée (par exemple, une semaine). Vous pouvez déterminer l’heure exacte à laquelle Azure Monitor a désactivé l’alerte de journal via le [journal d’activité Azure](../../azure-resource-manager/management/view-activity-logs.md). Dans le journal d’activité Azure, un événement est ajouté lorsqu’Azure Monitor désactive la règle d’alerte de journal.

L’exemple d’événement suivant dans le journal d’activité Azure concerne une règle d’alerte qui a été désactivée en raison d’une défaillance permanente.

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>La requête utilisée dans une alerte de journal n’est pas valide.

Chaque règle d’alerte de journal créée dans Azure Monitor dans le cadre de sa configuration doit spécifier une requête analytique que le service d’alerte exécutera périodiquement. La syntaxe de la requête analytique peut être correcte au moment de la création ou de la mise à jour de la règle. Mais parfois, au fil du temps, la requête fournie dans la règle d’alerte de journal peut développer des problèmes de syntaxe et provoquer l’échec de l’exécution de la règle. Voici quelques causes courantes du développement d’erreurs d’une requête analytique fournie dans une règle d’alerte de journal :

- La requête est écrite pour [s’exécuter sur plusieurs ressources](../log-query/cross-workspace-query.md). Or, une ou plusieurs des ressources spécifiées n’existent plus.
- Une [alerte de journal de type Mesure métrique](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) configurée sous forme de requête d’alerte n’est pas conforme aux normes de la syntaxe.
- Il n’y a eu aucun flux de données vers la plateforme d’analyse. L’[exécution de la requête retourne une erreur](https://dev.loganalytics.io/documentation/Using-the-API/Errors), car il n’existe aucune donnée pour la requête fournie.
- Des modifications apportées au [langage de requête](https://docs.microsoft.com/azure/kusto/query/) incluent un format révisé pour les commandes et les fonctions. Par conséquent, la requête fournie précédemment dans une règle d’alerte n’est plus valide.

[Azure Advisor](../../advisor/advisor-overview.md) vous avertit de ce comportement. Une recommandation est ajoutée pour la règle d’alerte de journal spécifique sur Azure Advisor, dans la catégorie Haute disponibilité, avec un impact moyen et une description permettant de « Réparer votre règle d’alerte de journal pour garantir la surveillance ». Si une requête d’alerte dans la règle d’alerte de journal n’est pas corrigée dans les sept jours suivant une recommandation émise par Azure Advisor, Azure Monitor désactive l’alerte de journal et empêche que vous soyez facturé inutilement lorsque la règle ne peut pas s’exécuter en permanence pendant une période prolongée (par exemple, une semaine).

Vous pouvez déterminer l’heure exacte à laquelle Azure Monitor a désactivé la règle d’alerte de journal en recherchant un événement dans le [journal d’activité Azure](../../azure-resource-manager/management/view-activity-logs.md).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [alertes de journal dans Azure](../platform/alerts-unified-log.md).
- En savoir plus sur [Application Insights](../../azure-monitor/app/analytics.md).
- Découvrez plus en détail les [requêtes dans les journaux](../log-query/log-query-overview.md).
