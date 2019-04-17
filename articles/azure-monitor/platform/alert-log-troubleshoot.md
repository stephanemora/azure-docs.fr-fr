---
title: Résolution des alertes de journal dans Azure Monitor | Microsoft Docs
description: Problèmes courants, les erreurs et résolution pour les règles d’alerte dans Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 0c7189f1d43a114532b30b0c1aabe6f7cd4402d8
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578711"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Résolution des problèmes liés aux alertes de journal d’activité dans Azure Monitor  

## <a name="overview"></a>Présentation

Cet article vous montre comment résoudre les problèmes couramment rencontrés lors de la définition des alertes de journal dans Azure Monitor. Il propose également des réponses aux questions fréquemment posées en termes de fonctionnalité ou de configuration des alertes de journal. 

Le terme **alertes de journal** décrit les alertes qu’incendie basé sur une requête de journal dans un [espace de travail Analytique de journal](../learn/tutorial-viewdata.md) ou [Application Insights](../../azure-monitor/app/analytics.md). Obtenez plus d’informations sur la fonctionnalité, la terminologie et les types dans [Alertes de journal - Vue d’ensemble](../platform/alerts-unified-log.md).

> [!NOTE]
> Cet article ne couvre pas les cas où le portail Microsoft Azure affiche une règle d’alerte déclenchée et notification effectuée via des groupes d'actions associés. Pour ces différents cas, reportez-vous aux détails de l’article relatif aux [Groupes d’actions](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>L'alerte de journal ne s'est pas déclenchée

Voici quelques-unes des raisons pour lesquelles un état de [règle d’alerte de journal dans Azure Monitor](../platform/alerts-log.md) ne s’affiche pas [comme étant *déclenché* lorsque cela est prévu](../platform/alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Durée d’ingestion de données pour les journaux d’activité

L'alerte de journal exécute régulièrement votre requête basée sur [Log Analytics](../learn/tutorial-viewdata.md) ou [Application Insights](../../azure-monitor/app/analytics.md). Étant donné qu’Azure Monitor traite plusieurs téraoctets de données à partir des milliers de clients à partir de sources variées dans le monde entier, le service est vulnérable à un certain délai différentes. Pour plus d’informations, consultez [heure d’ingestion de données dans les journaux d’Azure Monitor](../platform/data-ingestion-time.md).

Pour réduire le délai d’ingestion de données, le système attend et relance plusieurs fois la requête d’alerte s’il détecte que les données nécessaires n’ont pas encore été ingérées. Le temps d’attente du système est défini de manière exponentielle. Comme l’alerte de journal se déclenche uniquement une fois que les données sont disponibles, ce délai peut être dû à la lenteur du processus d’ingestion des données du journal. 

### <a name="incorrect-time-period-configured"></a>Configuration d'une période incorrecte

Comme décrit dans l’article relatif à la [terminologie des les alertes de journal](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), la période indiquée dans la configuration spécifie l'intervalle de temps de la requête. La requête renvoie uniquement les enregistrements créés dans cet intervalle de temps. La période limite les données extraites pour la requête de journal afin d’empêcher les abus, et contourne toute commande de temps (comme « il y a ») utilisée dans une requête de journal. Par exemple, si la période est définie sur 60 minutes et la requête exécutée à 13 h 15, seuls les enregistrements créés entre 12 h 15 et 13 h 15 sont utilisés pour la requête de journal. Si la requête de journal utilise une commande de temps telle que *il y a (1j)*, la requête continue d’utiliser les données collectées entre 12 h 15 et 13 h 15, car la période est définie sur cet intervalle.*

Par conséquent, vérifiez dans la configuration que cette période correspond à votre requête. Dans le cas de l'exemple indiqué précédemment, si la requête de journal utilise *il y a (1d)* comme illustré avec la marque verte, la période doit être définie sur 24 heures ou 1440 minutes (comme indiqué en rouge) pour veiller à ce que la requête s’exécute comme prévu.

![Période](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Activer l'option Supprimer les alertes

Comme décrit à l’étape 8 de l’article relatif à la [création d’une règle d’alerte de journal dans le portail Microsoft Azure](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal), les alertes de journal proposer une option **Supprimer les alertes** permettant de supprimer les actions de déclenchement et de notification pendant un laps de temps défini. Par conséquent, vous pouvez penser qu’une alerte ne se déclenche alors qu’en réalité c’est le cas, mais cette alerte a été supprimée.  

![Supprimer les alertes](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>La règle d’alerte Mesure métrique est incorrecte

Les **alertes de journal Mesure métrique** représentent un sous-type d’alertes de journal, dotées de fonctionnalités spécifiques et d’une syntaxe de requête d’alerte limitée. Une règle d'alerte de journal Mesure métrique implique que la sortie de la requête constitue une série chronologique de métriques, c’est-à-dire une table présentant des périodes équivalentes distinctes, avec les valeurs agrégées correspondantes. En outre, les utilisateurs peuvent ajouter d’autres variables à la table en plus de la valeur AggregatedValue. Ces variables peuvent être utilisées pour trier la table. 

Par exemple, supposons qu’une règle d’alerte de journal Mesure métrique soit configurée comme suit :

- requête : `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- période de 6 heures
- seuil de 50
- logique d’alerte de trois violations consécutives
- Aggregate Upon sélectionné en tant que $table

Étant donné que la commande inclut *summarize … by* et fournit deux variables (timestamp & $table), le système choisit $table avec la valeur *Aggregate Upon*. Elle trie la table des résultats selon le champ  *$table* comme indiqué ci-dessous, puis examine la valeur AggregatedValue pour chaque type de table (comme availabilityResults) afin de détecter la présence de 3 violations consécutives ou plus.

![Exécution de la requête Mesure métrique avec plusieurs valeurs](media/alert-log-troubleshoot/LogMMQuery.png)

*Aggregate Upon* étant défini sur *$table*, les données sont triées sur la colonne $table (comme en ROUGE), puis nous regroupons et examinons les types du champ *Aggregate Upon* (autrement dit) $table – par exemple : les valeurs pour availabilityResults seront considérées en tant que tracé/entité (comme mis en surbrillance en orange). Dans cette valeur de tracé/entité, le service d’alerte vérifie trois violations consécutives se produisant (comme indiqué en vert) pour lesquelles l’alerte sera déclenchée pour la valeur de table « availabilityResults ». De même, si pour toute autre valeur de $table trois violations consécutives sont observées, une autre notification d’alerte sera déclenchée pour la même chose ; le service d’alerte triant automatiquement les valeurs d’un tracé/d’une entité (comme indiqué en orange) par heure.

Maintenant, supposons que la règle d’alerte de journal ait été modifiée et que la requête correspondait à `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` avec le reste de la configuration identique avant d’inclure la logique d’alerte pour trois violations consécutives. Dans ce cas, l’option « Aggregate Upon » est par défaut : timestamp. Une seule valeur étant fournie dans la requête pour *summarize ... by* (autrement dit) *timestamp*, comme dans l’exemple précédent, au terme de l’exécution, le résultat correspond à celui illustré ci-dessous.

   ![Exécution de la requête Mesure métrique avec valeur singulière](media/alert-log-troubleshoot/LogMMtimestamp.png)

*Aggregate Upon* correspondant à timestamp, les données sont triées sur la colonne *timestamp* (comme indiqué en ROUGE), puis nous effectuons un regroupement par timestamp, par exemple : les valeurs pour `2018-10-17T06:00:00Z` seront considérées en tant que tracé/entité (comme mis en surbrillance en orange). Dans cette valeur de tracé/entité, le service d’alerte ne détectera aucune violation consécutive (la valeur timestamp ne présentant qu’une entrée) et dès lors, l’alerte ne sera jamais déclenchée. Dans ce cas, l’utilisateur doit :

- Ajouter une variable factice ou une variable existante (par exemple, $table) au tri correctement effectué à l’aide du champ « Aggregate Upon » configuré
- (Ou) Reconfigurer la règle d’alerte pour utiliser plutôt la logique d’alerte basée sur *violation totale* en conséquence

## <a name="log-alert-fired-unnecessarily"></a>Alerte de journal déclenchée inutilement

Les informations développées ci-dessous portent sur certaines raisons pour lesquelles une [règle d'alerte de journal configurée dans Azure Monitor](../platform/alerts-log.md) peut être déclenchée lorsqu'elle apparaît dans les [alertes Azure](../platform/alerts-managing-alert-states.md) alors que vous ne vous y attendiez pas.

### <a name="alert-triggered-by-partial-data"></a>Alerte déclenchée par des données partielles

L'analytique alimentant Log Analytics et Application Insights est sujette à des délais d'ingestion et de traitement et dès lors, au moment où une requête d'alerte de journal est exécutée, il peut arriver qu'aucune donnée ne soit disponible ou que seules quelques données soient disponibles. Pour plus d’informations, consultez [temps d’ingestion de données du journal dans Azure Monitor](../platform/data-ingestion-time.md).

Selon la configuration de la règle d’alerte, elle peut mal se déclencher en l’absence partielle ou totale de données dans les journaux d’activité au moment de son exécution. Dans ce cas, nous vous conseillons de modifier la requête d’alerte ou la configuration. 

Par exemple, si la règle d’alerte de journal est configurée pour se déclencher lorsque le nombre de résultats d’une requête analytique est inférieur à 5, l’alerte se déclenche en l’absence de données (zéro enregistrement) ou de résultats partiels (un enregistrement). Toutefois, après le délai d’ingestion des données, la même requête avec les données complètes peut arriver à un résultat de 10 enregistrements.

### <a name="alert-query-output-misunderstood"></a>Incompréhension des résultats de la requête d'alerte

Vous fournissez la logique des alertes de journal dans une requête analytique. La requête analytique peut utiliser différentes fonctions de Big Data et différentes fonctions mathématiques.  Le service d’alerte exécute votre requête selon les intervalles spécifiés avec les données pour la période spécifiée. Le service d’alerte apporte de petites modifications à la requête fournie en fonction du type d’alerte choisi. Cette modification peut être affichée dans la section « Query pour être exécutée » dans *logique du signal configurer* s’affiche, comme indiqué ci-dessous : ![Requête à exécuter](media/alert-log-troubleshoot/LogAlertPreview.png)

Ce qui est affiché dans la zone **Requête à exécuter** correspond à ce que le service d’alerte de journal exécute. Vous pouvez exécuter la requête indiquée, ainsi que les intervalles via le [portail Analytics](../log-query/portals.md) ou l’[API Analytics](https://docs.microsoft.com/rest/api/loganalytics/) si vous souhaitez comprendre ce que le résultat de la requête d’alerte peut être avant que vous ne créiez l’alerte.

## <a name="log-alert-was-disabled"></a>Alerte de journal a été désactivée.

Vous trouverez ci-dessous certaines raisons en raison de laquelle [règle de journal d’alerte dans Azure Monitor](../platform/alerts-log.md) peut être désactivée par Azure Monitor.

### <a name="resource-on-which-alert-was-created-no-longer-exists"></a>Ressource sur laquelle alerte a été créée n’est plus existe

Règles d’alerte créées dans Azure Monitor ciblent une ressource spécifique comme un espace de travail Analytique des journaux Azure, Azure Application application Insights et des ressources Azure. Et le service d’alerte de journal puis exécutera la requête analytique fournie dans la règle pour la cible spécifiée. Mais après la création de la règle, souvent les utilisateurs se connecter à supprimer à partir d’Azure ou déplacer à l’intérieur de Azure - la cible de la règle d’alerte. Comme la cible de la règle d’alerte de journal n’est plus valide, l’exécution de la règle ne parvient pas.

Dans ce cas, Azure Monitor désactivera l’alerte de journal et vérifiez les clients ne sont pas facturés inutilement, lorsque la règle proprement dite n’est pas en mesure d’exécuter en permanence pendant une période importante comme une semaine. Les utilisateurs peuvent trouver sur l’heure exacte à laquelle la règle d’alerte de journal a été désactivée par l’analyse Azure via [journal d’activité Azure](../../azure-resource-manager/resource-group-audit.md). Dans le journal d’activité Azure lorsque la règle d’alerte de journal est désactivée par Azure, un événement est ajouté dans le journal d’activité Azure.

Exemple d’événement dans le journal d’activité Azure pour la désactivation de la règle d’alerte en raison de sa défaillance permanente ; est présenté ci-dessous.

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

### <a name="query-used-in-log-alert-is-not-valid"></a>Requête utilisée dans l’alerte de journal n’est pas valide

Chaque règle d’alerte journal créé dans Azure Monitor dans le cadre de sa configuration doit spécifier une requête analytique doit être exécuté régulièrement par le service d’alerte. Si la requête analytique peut-être avoir la syntaxe correcte au moment de la création de la règle ou de mise à jour. Quelques fois sur une période de temps, la requête fournir dans le journal des règle d’alerte peut développer des problèmes de syntaxe et provoquer l’échec de l’exécution de la règle. Voici quelques causes courantes pourquoi requête analytique fournie dans une règle d’alerte de journal permettre développer des erreurs sont :

- Requête est écrite dans [exécuter sur plusieurs ressources](../log-query/cross-workspace-query.md) et un ou plusieurs des ressources spécifiées, désormais n’existent pas.
- Il n’y a eu aucun flux de données pour la plateforme d’analytique, ce qui le [entraîne une erreur d’exécution de la requête](https://dev.loganalytics.io/documentation/Using-the-API/Errors) car il n’existe aucune donnée pour la requête fournie.
- Modifie dans [langage de requête](https://docs.microsoft.com/azure/kusto/query/) se sont produites dans les commandes et fonctions ont un format révisé. Par conséquent, la requête fournie plus haut dans la règle d’alerte n’est plus valide.

L’utilisateur est averti de ce comportement tout d’abord [Azure Advisor](../../advisor/advisor-overview.md). Une recommandation sera ajoutée pour la règle d’alerte journal spécifique sur Azure Advisor, sous la catégorie de la haute disponibilité avec un impact moyen et une description comme « Réparer votre règle d’alerte de journal pour assurer un suivi ». Si la requête d’alerte dans la règle d’alerte de journal spécifié n’est pas faite bout de sept jours de la recommandation en fournissant sur Azure Advisor. Azure Monitor sera désactiver l’alerte de journal, puis vérifiez les clients ne sont pas facturés inutilement, lorsque la règle proprement dite n’est pas en mesure d’exécuter en permanence pendant une période importante comme une semaine.

Les utilisateurs peuvent trouver sur l’heure exacte à laquelle la règle d’alerte de journal a été désactivée par l’analyse Azure via [journal d’activité Azure](../../azure-resource-manager/resource-group-audit.md). Dans le journal d’activité Azure, lorsque la règle d’alerte de journal est désactivée par Azure - un événement est ajouté dans le journal d’activité Azure.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [alertes de journal dans les alertes Azure ](../platform/alerts-unified-log.md)
- En savoir plus sur [Application Insights](../../azure-monitor/app/analytics.md)
- En savoir plus sur [enregistrer des requêtes](../log-query/log-query-overview.md)
