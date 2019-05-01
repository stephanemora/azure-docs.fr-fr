---
title: Résoudre les alertes de journal dans Azure Monitor | Microsoft Docs
description: Problèmes courants, erreurs et résolutions pour les règles d’alerte dans Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 1c7712fc2ce55a3d22995bb119a9ee485a064903
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683393"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Résoudre les alertes de journal dans Azure Monitor  

Cet article vous explique comment résoudre les problèmes courants susceptibles de se produire lorsque vous configurez des alertes de journal dans Azure Monitor. Il fournit également des solutions aux problèmes courants avec les fonctionnalités ou la configuration d’alertes de journal. 

Le terme *alertes de journal* décrit les alertes qu’incendie basé sur une requête de journal dans un [espace de travail Azure Log Analytique](../learn/tutorial-viewdata.md) ou dans [Azure Application Insights](../../azure-monitor/app/analytics.md). En savoir plus sur des fonctionnalités, la terminologie et les types dans [alertes de journal dans Azure Monitor](../platform/alerts-unified-log.md).

> [!NOTE]
> Cet article ne considère pas les cas où le portail Azure affiche une règle d’alerte déclenchée et une notification n’est pas effectuée par un groupe d’actions associé. Pour ce cas, consultez les détails dans [créer et gérer des groupes d’actions dans le portail Azure](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>L'alerte de journal ne s'est pas déclenchée

Voici quelques-unes des raisons pourquoi l’état pour un configuré [règle de journal d’alerte dans Azure Monitor](../platform/alerts-log.md) n’affiche pas [comme *déclenché* prévu](../platform/alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Heure d’ingestion de données pour les journaux

Une alerte de journal s’exécute périodiquement votre requête en fonction de [Analytique de journal](../learn/tutorial-viewdata.md) ou [Application Insights](../../azure-monitor/app/analytics.md). Étant donné qu’Azure Monitor traite plusieurs téraoctets de données à partir des milliers de clients à partir de sources variées dans le monde entier, le service est vulnérable à différents délais. Pour plus d’informations, consultez [heure d’ingestion de données dans les journaux Azure Monitor](../platform/data-ingestion-time.md).

Pour atténuer des retards, le système attend et tente à nouveau la requête d’alerte plusieurs fois s’il trouve que les données nécessaires ne sont pas encore reçues. Le temps d’attente du système est défini de manière exponentielle. L’alerte de journal est déclenchée uniquement une fois que les données sont disponibles, le délai peut donc être en raison d’une ingestion lente de données de journal. 

### <a name="incorrect-time-period-configured"></a>Configuration d'une période incorrecte

Comme décrit dans l’article sur [terminologie pour les alertes de journal](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), la période indiquée dans la configuration spécifie l’intervalle de temps pour la requête. La requête retourne uniquement les enregistrements qui ont été créés dans cette plage. 

La période de temps restreint les données sont extraites pour une requête de journal éviter les abus, et il contourne n’importe quelle commande de temps (comme **il y a**) utilisé dans une requête de journal. Par exemple, si la période est définie sur 60 minutes et la requête exécutée à 13 h 15, seuls les enregistrements créés entre 12 h 15 et 13 h 15 sont utilisés pour la requête de journal. Si la requête de journal utilise une commande de temps comme **il y a (1D)**, la requête utilise toujours uniquement les données entre 12 h 15 et 13 h 15, car la période est définie pour cet intervalle.

Vérifiez que la période de temps dans la configuration correspond à votre requête. Pour l’exemple présentée, si la requête de journal utilise **il y a (1D)** avec la marque verte, la période de temps doit être définie à 24 heures ou 1 440 minutes (indiqués en rouge). Ce paramètre garantit que la requête s’exécute comme prévu.

![Période](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Activer l'option Supprimer les alertes

Comme décrit à l’étape 8 de l’article sur [création d’une règle d’alerte de journal dans le portail Azure](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal), les alertes de journal fournissent un **supprimer les alertes** option pour supprimer les actions de notification et de déclenchement pour un intervalle de temps. Par conséquent, vous pourriez penser qu’une alerte ne se déclenche. En fait, il déclenchées mais a été supprimé.  

![Supprimer les alertes](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>La règle d’alerte Mesure métrique est incorrecte

*Alertes de journal de mesure métrique* représentent un sous-type d’alertes de journal qui ont des fonctionnalités spécifiques et une syntaxe de requête d’alerte limitée. Une règle pour une alerte de journal de mesure métrique nécessite la résultat soit une série de temps de la mesure de la requête. Autrement dit, la sortie est une table avec des périodes distinctes, de taille égale, ainsi que des valeurs agrégées correspondants. 

Vous pouvez choisir d’utiliser des variables supplémentaires dans la table en même temps que **AggregatedValue**. Ces variables peuvent être utilisées pour trier la table. 

Par exemple, qu'une règle pour une alerte de journal de mesure métrique a été configurée en tant que :

- Requête de `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- période de 6 heures
- seuil de 50
- logique d’alerte de trois violations consécutives
- **Agrégation sur** choisi comme **$table**

Étant donné que la commande inclut **résumer... par** et fournit les deux variables (**timestamp** et **$table**), le système choisit **$table** pour **agrégation lors de** . Le système trie la table de résultats par le **$table** champ, comme illustré dans la capture d’écran suivante. Ensuite il examine le multiple **AggregatedValue** instances pour chaque type de table (comme **availabilityResults**) pour voir s’il y a trois ou plusieurs violations consécutives.

![Exécution des requêtes avec plusieurs valeurs de mesure métrique](media/alert-log-troubleshoot/LogMMQuery.png)

Étant donné que **agrégation lors de** est défini sur **$table**, les données sont triées sur un **$table** colonne (indiqué en rouge). Ensuite, nous regrouper et rechercher des types de la **agrégation lors** champ. 

Par exemple, pour **$table**, les valeurs pour **availabilityResults** sera considéré comme une entité/traçage (indiquée en orange). Dans cette valeur de traçage/entité, le service d’alerte vérifie trois violations consécutives (indiquées en vert). Les violations de déclenchent une alerte pour la valeur de table **availabilityResults**. 

De même, si trois violations consécutives se produisent pour toute autre valeur de **$table**, une autre notification d’alerte est déclenchée pour la même chose. Le service d’alerte trie automatiquement les valeurs dans une entité/traçage (indiquée en orange) par heure.

Supposons maintenant que la règle d’alerte de journal de mesure métrique a été modifiée et que la requête a été `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`. Le reste de la configuration reste le même comme avant, y compris la logique d’alerte pour trois violations consécutives. Le **agrégation lors de** option dans ce cas est **timestamp** par défaut. Qu’une seule valeur est fournie dans la requête pour **résumer... par** (autrement dit, **timestamp**). Comme l’exemple précédent, la sortie à la fin d’exécution serait comme illustré comme suit.

   ![Exécution de la requête avec la valeur singulière mesure métrique](media/alert-log-troubleshoot/LogMMtimestamp.png)

Étant donné que **agrégation lors de** est défini sur **timestamp**, les données sont triées sur la **timestamp** colonne (indiqué en rouge). Puis nous regrouper par **timestamp**. Par exemple, les valeurs pour `2018-10-17T06:00:00Z` sera considéré comme une entité/traçage (indiquée en orange). Dans cette valeur de traçage/entité, le service d’alerte ne trouverez aucune violations consécutives (étant donné que chaque **timestamp** valeur n'a qu’une seule entrée). Par conséquent, l’alerte n’est jamais déclenchée. Dans ce cas, l’utilisateur doit soit :

- Ajouter une variable factice ou une variable existante (comme **$table**) pour trier correctement à l’aide de la **agrégation lors** champ.
- Reconfigurer la règle d’alerte pour utiliser la logique d’alerte selon **nombre total de violations** à la place.

## <a name="log-alert-fired-unnecessarily"></a>Alerte de journal déclenchée inutilement

Une configuré [règle de journal d’alerte dans Azure Monitor](../platform/alerts-log.md) peut être déclenchée de façon inattendue lorsque vous l’affichez dans [Azure Alerts](../platform/alerts-managing-alert-states.md). Les sections suivantes décrivent quelques-unes des raisons.

### <a name="alert-triggered-by-partial-data"></a>Alerte déclenchée par des données partielles

Analytique de journal et Application Insights subissent un traitement et des retards d’ingestion. Lorsque vous exécutez une requête d’alerte de journal, vous constaterez peut-être qu’aucune donnée n’est disponible, ou uniquement certaines données sont disponibles. Pour plus d’informations, consultez [temps d’ingestion de données du journal dans Azure Monitor](../platform/data-ingestion-time.md).

Selon la configuration de la règle d’alerte, il peut se produire si il n’est pas de données ou des données partielles dans les journaux au moment de l’exécution des alertes. Dans ce cas, nous vous conseillons de modifier la configuration ou requête d’alerte. 

Par exemple, si vous configurez la règle d’alerte de journal à déclencher lorsque le nombre de résultats à partir d’une requête analytique est inférieur à 5, l’alerte est déclenchée lorsqu’il n’existe aucune donnée (zéro enregistrement) ou les résultats partiels (un enregistrement). Mais après le délai d’ingestion de données, la même requête avec les données complètes peut fournir un résultat de 10 enregistrements.

### <a name="alert-query-output-is-misunderstood"></a>Sortie de la requête d’alerte est considérée à tort

Vous fournissez la logique des alertes de journal dans une requête analytique. La requête d’analytique peut utiliser différentes big data et les fonctions mathématiques. Le service d’alerte s’exécute votre requête à intervalles spécifiés avec des données pour une période de temps spécifié. Le service d’alerte apporte des modifications subtiles à la requête basée sur le type d’alerte. Vous pouvez afficher cette modification dans le **requête doit être exécutée** section sur la **logique du signal configurer** écran :

![Requête à exécuter](media/alert-log-troubleshoot/LogAlertPreview.png)

Le **requête doit être exécutée** boîte est ce que le service d’alerte de journal s’exécute. Si vous souhaitez comprendre ce que la requête d’alerte de sortie peut être avant de créer l’alerte, vous pouvez exécuter la requête indiquée et l’intervalle de temps via le [portail d’Analytique](../log-query/portals.md) ou [Analytique API](https://docs.microsoft.com/rest/api/loganalytics/).

## <a name="log-alert-was-disabled"></a>Alerte de journal a été désactivée.

Les sections suivantes répertorient certaines raisons pourquoi Azure Monitor peut entraîner la désactivation du [règle d’alerte journal](../platform/alerts-log.md).

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Ressource où l’alerte a été créée n’est plus existe

Règles d’alerte créées dans Azure Monitor ciblent une ressource spécifique comme un espace de travail Analytique des journaux Azure, une application Azure Application Insights et une ressource Azure. Le service d’alerte de journal est puis exécutez une requête analytique fournie dans la règle pour la cible spécifiée. Mais après la création de règle, les utilisateurs passent généralement supprimer à partir d’Azure--ou déplacer à l’intérieur de Azure--la cible de la règle d’alerte de journal. Étant donné que la cible de la règle d’alerte n’est plus valide, l’exécution de la règle échoue.

Dans ce cas, Azure Monitor désactive l’alerte de journal et garantit que vous n'êtes pas facturé inutilement lorsque la règle ne peut pas s’exécuter en permanence pendant une période dimensionnable (par exemple, une semaine). Vous pouvez déterminer l’heure exacte lorsque Azure Monitor désactivé l’alerte de journal via [journal d’activité Azure](../../azure-resource-manager/resource-group-audit.md). Dans le journal d’activité Azure, un événement est ajouté quand Azure Monitor désactive la règle d’alerte de journal.

L’exemple d’événement dans le journal d’activité Azure est pour une règle d’alerte qui a été désactivée en raison d’une défaillance permanente.

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>Requête utilisée dans une alerte de journal n’est pas valide

Chaque règle d’alerte journal créé dans Azure Monitor dans le cadre de sa configuration doit spécifier une requête analytique exécute périodiquement le service d’alerte. La requête analytique peut-être la syntaxe correcte au moment de la création de la règle ou de mise à jour. Mais parfois, sur une période de temps, la requête fournie dans la règle d’alerte de journal peut développer des problèmes de syntaxe et provoquer l’échec de l’exécution de la règle. Voici quelques causes courantes pourquoi une requête analytique fournie dans une règle d’alerte de journal permettre développer des erreurs sont :

- La requête est écrite dans [exécuter sur plusieurs ressources](../log-query/cross-workspace-query.md). Et un ou plusieurs des ressources spécifiées n’existent plus.
- Il n’y a eu aucun flux de données pour la plateforme d’analytique. Le [l’exécution de requête génère une erreur](https://dev.loganalytics.io/documentation/Using-the-API/Errors) , car il n’existe aucune donnée pour la requête fournie.
- Modifie dans [langage de requête](https://docs.microsoft.com/azure/kusto/query/) incluent un format révisé pour les commandes et fonctions. Par conséquent, la requête fournie plus haut dans une règle d’alerte n’est plus valide.

[Azure Advisor](../../advisor/advisor-overview.md) vous avertit que ce comportement. Une recommandation est ajoutée pour la règle d’alerte journal spécifique sur Azure Advisor, sous la catégorie de la haute disponibilité avec un impact moyen et une description de « Réparer votre règle d’alerte de journal pour assurer un suivi ». Si une requête d’alerte dans la règle d’alerte de journal n’est pas corrigée une fois que le conseiller Azure a fourni une recommandation pour sept jours, Azure Monitor désactive l’alerte de journal et vérifiez que vous n'êtes pas facturé inutilement lorsque la règle ne peut pas s’exécuter en permanence pour un dimensionnable (période comme une semaine).

Vous pouvez trouver l’heure exacte lorsque Azure Monitor a désactivé la règle d’alerte de journal en recherchant un événement dans [journal d’activité Azure](../../azure-resource-manager/resource-group-audit.md).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [alertes de journal dans Azure](../platform/alerts-unified-log.md).
- En savoir plus sur [Application Insights](../../azure-monitor/app/analytics.md).
- En savoir plus sur [enregistrer des requêtes](../log-query/log-query-overview.md).
