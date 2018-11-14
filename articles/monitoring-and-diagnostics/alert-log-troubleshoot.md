---
title: Résolution des problèmes liés aux alertes de journal d’activité dans Azure Monitor
description: Problèmes courants, erreurs et résolution pour les règles d'alerte de journal dans Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 5572c80879584e7f6df650263ae455a134ee4088
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283595"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Résolution des problèmes liés aux alertes de journal d’activité dans Azure Monitor  

## <a name="overview"></a>Vue d’ensemble
Cet article vous explique comment gérer les problèmes courants lors de la configuration d'alertes de journal dans Azure Monitor, et proposer une réponse aux questions fréquemment posées en termes de fonctionnalité ou de configuration des alertes de journal. Le terme **alertes de journal** décrit les alertes où le signal est une requête personnalisée basée sur [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) ou [Application Insights](../application-insights/app-insights-analytics.md). Obtenez plus d’informations sur la fonctionnalité, la terminologie et les types dans [Alertes de journal - Vue d’ensemble](monitor-alerts-unified-log.md).

> [!NOTE]
> Cet article ne couvre pas les cas où la règle d'alerte apparaît comme déclenchée dans le portail Microsoft Azure ou par notification via les groupes d'actions associés. Pour ces différents cas, reportez-vous aux détails de l’article relatif aux [Groupes d’actions](monitoring-action-groups.md).


## <a name="log-alert-didnt-fire"></a>L'alerte de journal ne s'est pas déclenchée

Les informations développées ci-dessous portent sur certaines raisons pour lesquelles une [règle d'alerte de journal configurée dans Azure Monitor](alert-log.md) ne se déclenche alors qu'elle apparaît dans les [alertes Azure](monitoring-alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Durée d’ingestion de données pour les journaux
L'alerte de journal fonctionne en exécutant régulièrement la requête fournie par le client basée sur [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) ou [Application Insights](../application-insights/app-insights-analytics.md). Ces deux services sont optimisés par Analytics, qui traite de vastes quantités de données de journal et fournit des fonctionnalités. Le service Log Analytics impliquant le traitement de nombreux téraoctets de données issues de milliers de clients, ainsi que de sources variées partout dans le monde, il peut nécessiter un certain délai. Pour plus d’informations, consultez [Durée d’ingestion de données dans Log Analytics](../log-analytics/log-analytics-data-ingestion-time.md).

Pour combler la durée d’ingestion de données possible dans les journaux Log Analytics ou Application Insights, l'alerte de journal patiente et effectue une nouvelle tentative si les données ne sont pas encore ingérées dans le délai d'alerte. Les alertes de journal s'accompagnent d'un délai d'attente exponentiel pour veiller à ce que nous patientions le temps nécessaire à l'ingestion des données par Log Analytics. Dès lors, si les journaux interrogés par votre règle d’alerte de journal sont affectés par des retards d’ingestion, l'alerte de journal se déclenche uniquement une fois les données disponibles après ingestion dans Log Analytics et après un délai exponentiel dû aux différentes tentatives du service d'alerte de journal.

### <a name="incorrect-time-period-configured"></a>Configuration d'une période incorrecte
Comme décrit dans l’article relatif à la [terminologie des les alertes de journal](monitor-alerts-unified-log.md#log-search-alert-rule---definition-and-types), la période indiquée dans la configuration spécifie l'intervalle de temps de la requête. La requête renvoie uniquement les enregistrements créés dans cet intervalle de temps. La période limite les données extraites pour la requête de journal afin d’empêcher les abus, et contourne toute commande de temps (comme « il y a ») utilisée dans une requête de journal. 
*Par exemple, si la période est définie sur 60 minutes et la requête exécutée à 13 h 15, seuls les enregistrements créés entre 12 h 15 et 13 h 15 sont retournés pour l’exécution de la requête de journal. Maintenant, si la requête de journal utilise une commande de temps telle que « il y a » (1d), la requête de journal est exécutée uniquement pour les données collectées entre 12 h 15 et 13 h 15, comme si celles-ci n’avaient existé que pendant les 60 minutes passées, et non pendant les sept jours de données spécifiés dans la requête de journal.*

Selon votre logique de requête, vérifiez que la période a été indiquée dans la configuration. Dans le cas de l'exemple indiqué précédemment, si la requête de journal utilise il y a (1d) comme illustré avec la marque verte, la période doit être définie sur 24 heures ou 1 440 minutes (comme indiqué en rouge) pour veiller à ce que la requête fournie s’exécute comme prévu.
    ![Période](./media/monitor-alerts-unified/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Activer l'option Supprimer les alertes
Comme décrit à l’étape 8 de l’article relatif à la [création d’une règle d’alerte de journal dans le portail Microsoft Azure](alert-log.md#managing-log-alerts-from-the-azure-portal), une alerte de journal permet de configurer la suppression automatique de la règle d’alerte et d'empêcher la notification et/ou le déclencheur pendant le laps de temps stipulé. L'option Supprimer les alertes entraîne l'exécution de l'alerte de journal, sans déclencher le groupe d'actions pendant le laps de temps spécifié dans l'option **Supprimer les alertes**, et dès lors, l'utilisateur peut penser que l'alerte ne s'est pas déclenchée alors qu'elle a été supprimée comme configuré.
    ![Supprimer les alertes](./media/monitor-alerts-unified/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>La règle d’alerte Mesure métrique est incorrecte
Le type de mesure métrique de la règle d'alerte de journal correspond à un sous-type des alertes de journal, doté de fonctionnalités spécifiques mais limitant la syntaxe de requête d'alerte. La règle d'alerte de journal Mesure métrique implique que la sortie de la requête d'alerte fournisse une série chronologique de métriques, une table présentant des périodes équivalentes, ainsi que les valeurs correspondantes pour AggregatedValue. En outre, les utilisateurs peuvent choisir de disposer dans la table de variables en plus de la valeur AggregatedValue comme ordinateur, nœud, etc., en utilisant les données de la table qui peuvent être triées.

Par exemple, supposons que la règle d’alerte de journal Mesure métrique soit configurée comme suit :
- requête : `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- période de 6 heures
- seuil de 50
- logique d’alerte de trois violations consécutives
- Aggregate Upon sélectionné en tant que $table

Ainsi, puisque dans la commande nous avons utilisé résumer... par et fourni deux variables : timestamp & $table ; le service d'alerte choisit $table pour « Aggregate Upon » - triez la table des résultats par champ : $table - comme indiqué ci-dessous et examinez la valeur AggregatedValue pour chaque type de table (comme availabilityResults) afin de détecter la présence de 3 violations consécutives ou plus.

   ![Exécution de la requête Mesure métrique avec plusieurs valeurs](./media/monitor-alerts-unified/LogMMQuery.png)

« Aggregate Upon » correspondant à $table, les données sont triées sur la colonne $table (comme en ROUGE), puis nous regroupons et examinons les types du champ « Aggregate Upon » (autrement dit) $table – par exemple : les valeurs pour availabilityResults seront considérées en tant que tracé/entité (comme mis en surbrillance en orange). Dans cette valeur de tracé/entité, le service d’alerte vérifie trois violations consécutives se produisant (comme indiqué en vert) pour lesquelles l'alerte sera déclenchée pour la valeur de table « availabilityResults ». De même, si pour toute autre valeur de $table trois violations consécutives sont observées, une autre notification d’alerte sera déclenchée ; le service d’alerte triant automatiquement les valeurs d'un tracé/d'une entité (comme indiqué en orange) par heure.

Maintenant, supposons que la règle d’alerte de journal ait été modifiée et que la requête correspondait à `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` avec le reste de la configuration identique avant d’inclure la logique d’alerte pour trois violations consécutives. Dans ce cas, l’option « Aggregate Upon » est par défaut : timestamp. Une seule valeur étant fournie dans la requête pour résumer... par (autrement dit) timestamp, comme dans l'exemple précédent, au terme de l'exécution, le résultat correspond à celui illustré ci-dessous. 

   ![Exécution de la requête Mesure métrique avec valeur singulière](./media/monitor-alerts-unified/LogMMtimestamp.png)

« Aggregate Upon » correspondant à timestamp, les données sont triées sur la colonne timestamp (comme indiqué en ROUGE), puis nous effectuons un regroupement par timestamp, par exemple : les valeurs pour `2018-10-17T06:00:00Z` seront considérées en tant que tracé/entité (comme mis en surbrillance en orange). Dans cette valeur de tracé/entité, le service d'alerte ne détectera aucune violation consécutive (la valeur timestamp ne présentant qu'une entrée) et dès lors, l'alerte ne sera jamais déclenchée. Dans ce cas, l'utilisateur doit :
- Ajouter une variable factice ou une variable existante (par exemple, $table) au tri correctement effectué à l’aide du champ « Aggregate Upon » configuré
- (Ou) Reconfigurer la règle d’alerte pour utiliser plutôt la logique d’alerte basée sur *violation totale* en conséquence
 
## <a name="log-alert-fired-unnecessarily"></a>Alerte de journal déclenchée inutilement
Les informations développées ci-dessous portent sur certaines raisons pour lesquelles une [règle d'alerte de journal configurée dans Azure Monitor](alert-log.md) peut être déclenchée lorsqu'elle apparaît dans les [alertes Azure](monitoring-alerts-managing-alert-states.md) alors que vous ne vous y attendiez pas.

### <a name="alert-triggered-by-partial-data"></a>Alerte déclenchée par des données partielles
L'analytique alimentant Log Analytics et Application Insights est sujette à des délais d'ingestion et de traitement et dès lors, au moment où une requête d'alerte de journal est exécutée, il peut arriver qu'aucune donnée ne soit disponible ou que seules quelques données soient disponibles. Pour plus d’informations, consultez [Durée d’ingestion de données dans Log Analytics](../log-analytics/log-analytics-data-ingestion-time.md).

Selon la configuration de la règle d’alerte, elle peut mal se déclencher en l'absence partielle ou totale de données dans les journaux au moment de son exécution. Si tel est le cas, il est recommandé de modifier la requête d'alerte ou la configuration. *Par exemple, si la règle d’alerte de journal est configurée pour se déclencher lorsque le nombre de résultats d'une requête analytique est inférieur à 5, en l'absence de données (zéro enregistrement) ou de résultats partiels (un enregistrement) la règle d'alerte se déclenchera. Passé le délai d'ingestion, lorsque la même requête est exécutée dans Analytics, la requête avec toutes les données peut fournir 10 enregistrements.*

### <a name="alert-query-output-misunderstood"></a>Incompréhension des résultats de la requête d'alerte
En matière d'alertes de journal, la logique d'alerte est fournie par l'utilisateur via une requête Analytics. La requête Analytics fournie peut utiliser différentes fonctions Big Data et mathématiques pour créer des constructions spécifiques. Le service d’alerte exécutera la requête fournie par le client aux intervalles spécifiés avec les données pendant la période spécifiée. Le service d'alerte apporte quelques modifications à la requête fournie selon le type d'alerte choisi et la même chose peut être observée dans la section « Requête à exécuter » de l'écran Configurer la logique du signal, comme illustré ci-dessous : ![Requête à exécuter](./media/monitor-alerts-unified/LogAlertPreview.png)
 
Le contenu affiché dans la section **Requête à exécuter** correspond à ce que le service d'alerte de journal exécutera. L'utilisateur peut exécuter la requête indiquée ainsi que l'intervalle de temps via le [portail Analytics](../log-analytics/log-analytics-log-search-portals.md) ou l'[API Analytics](https://docs.microsoft.com/rest/api/loganalytics/) s'il souhaite comprendre avant la création de l'alerte, les résultats qu'elle pourra produire.
 
## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [alertes de journal dans les alertes Azure ](monitor-alerts-unified-log.md)
* En savoir plus sur [Application Insights](../application-insights/app-insights-analytics.md)
* En savoir plus sur [Log Analytics](../log-analytics/log-analytics-queries.md). 