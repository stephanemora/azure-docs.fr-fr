---
title: Alertes de journal dans Azure Monitor
description: Déclenchez des e-mails, des notifications, des URL de sites web d’appel (webhooks) ou une automatisation lorsque les conditions de requête analytique que vous spécifiez sont remplies pour Alertes Azure.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 2e2db54f4c356a754144e17b11cf25fdf3f12d9f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994001"
---
# <a name="log-alerts-in-azure-monitor"></a>Alertes de journal dans Azure Monitor
Cet article fournit des informations sur les alertes de journal, qui sont l’un des types d’alertes pris en charge dans les [Alertes Azure](monitoring-overview-unified-alerts.md), et qui permettent aux utilisateurs d’utiliser la plateforme d’analyse d’Azure comme base pour la génération d’alertes.

Une alerte de journal consiste en des règles de recherche dans les journaux créées pour [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) ou [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events). Pour en savoir plus sur son utilisation, voir [Création d’alertes de journal dans Azure](alert-log.md)

> [!NOTE]
> Les données de journal populaires d’[Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) sont désormais également disponibles sur la plateforme de métrique dans Azure Monitor. Pour en savoir plus, voir [Alerte de métrique pour les journaux](monitoring-metric-alerts-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Règle d’alerte de recherche dans les journaux - Définition et types

Des règles de recherche dans les journaux sont créées par Alertes Azure pour exécuter automatiquement des requêtes de journal spécifiées à intervalles réguliers.  Si les résultats de la requête de journal répondent à des critères particuliers, un enregistrement d’alerte est généré. La règle peut alors exécuter automatiquement une ou plusieurs actions à l’aide de [groupes d’actions](monitoring-action-groups.md). 

Les règles de recherche dans les journaux sont définies par les détails suivants :
- **Requête de journal**.  La requête qui s’exécute chaque fois que la règle d’alerte est déclenchée.  Les enregistrements renvoyés par cette requête permettent de déterminer si une alerte a été créée. Une requête Analytics peut également inclure des [appels entre applications](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery), entre espaces de travail et [entre ressources](../log-analytics/log-analytics-cross-workspace-search.md), à condition que l’utilisateur dispose de droits d’accès aux applications externes. 

    > [!IMPORTANT]
    > L’utilisateur doit disposer du rôle [Contributeur de surveillance Azure](monitoring-roles-permissions-security.md) afin de pouvoir créer, modifier et mettre à jour des alertes de journal dans Azure Monitor, ainsi que de droits d’accès et d’exécution de requête pour la ou les cibles d’analyse dans la règle ou la requête d’alerte. Si l’utilisateur n’a pas accès à toutes les cibles d’analyse d’une règle ou requête d’alerte, la création de règles peut échouer ou la règle d’alerte de journal va renvoyer des résultats partiels.

- **Période**  Spécifie l’intervalle de temps pour la requête. La requête retourne uniquement les enregistrements qui ont été créés dans cette plage précédant son exécution. La période limite les données extraites pour la requête de journal afin d’empêcher les abus, et contourne toute commande de temps (comme « il y a ») utilisée dans une requête de journal. <br>*Par exemple, si la période est définie sur 60 minutes et la requête exécutée à 13 h 15, seuls les enregistrements créés entre 12 h 15 et 13 h 15 sont retournés pour l’exécution de la requête de journal. Maintenant, si la requête de journal utilise une commande de temps telle que « il y a » (7d), la requête de journal est exécutée uniquement pour les données collectées entre 12 h 15 et 13 h 15, comme si celles-ci n’avaient existé que pendant les 60 minutes passées, et non pendant les sept jours de données spécifiés dans la requête de journal.*
- **Fréquence**.  Spécifie la fréquence à laquelle la requête doit être exécutée. Peut être toute valeur comprise entre 5 minutes et 24 heures. La valeur doit être inférieure ou égale à la période.  Si la valeur est supérieure à la période, vous risquez de manquer des enregistrements.<br>*Par exemple, imaginons une période de 30 minutes associée à une fréquence de 60 minutes.  Si la requête est exécutée à 13 h, les enregistrements entre 12 h 30 et 13 h sont renvoyés.  La requête s’exécute ensuite à 14 h, moment auquel elle renvoie les enregistrements entre 13 h 30 et 14 h.  Les enregistrements créés entre 13 h 00 et 13 h 30 ne sont jamais analysés.*
- **Seuil**.  Les résultats de la recherche dans les journaux sont évalués pour déterminer si une alerte doit être créée.  Le seuil diffère selon le type de règle d’alerte de recherche dans les journaux.

Les règles de recherche dans les journaux, qu’il s’agisse d’[Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) ou d’[Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events), peuvent être de deux types. Chacun de ces types est décrit en détail dans les sections suivantes.

- **[Nombre de résultats](#number-of-results-alert-rules)**. Alerte unique créée lorsque le nombre d’enregistrements renvoyés par la recherche dans les journaux dépasse un nombre spécifié.
- **[Mesure métrique](#metric-measurement-alert-rules)**.  Alerte créée pour chaque objet des résultats de la recherche dans les journaux dont les valeurs dépassent le seuil spécifié.

Les différences entre les types de règles d’alerte sont présentées ci-dessous.

- Une règle d’alerte *Nombre de résultats* crée toujours une alerte unique, tandis que la règle d’alerte *Mesure de métriques* crée une alerte pour chaque objet dépassant le seuil.
- Les règles d’alerte *Nombre de résultats* créent une alerte quand le seuil est dépassé une seule fois. Les règles d’alerte *Mesure métrique* peuvent créer une alerte lorsque le seuil est dépassé un certain nombre de fois au cours d’un intervalle de temps spécifique.

### <a name="number-of-results-alert-rules"></a>Règles d’alerte Nombre de résultats
Les règles d’alerte **Nombre de résultats** créent une alerte unique lorsque le nombre d’enregistrement renvoyés par la requête de recherche dépasse le seuil spécifié. Ce type de règle d’alerte est idéal pour la gestion des événements, par exemple les journaux des événements Windows, les journaux Syslog, la réponse WebApp et les journaux personnalisés.  Vous pouvez créer une alerte quand un événement d’erreur particulier est créé, ou quand plusieurs événements d’erreur sont créés au cours d’une période particulière.

**Seuils** : le seuil pour une règle d’alerte Nombre de résultats est supérieur ou inférieur à une valeur particulière.  Si le nombre d’enregistrements renvoyés par cette recherche dans les journaux satisfait à ce critère, une alerte est créée.

Pour déclencher une alerte sur un événement unique, définissez le nombre de résultats sur une valeur supérieure à 0 et vérifiez si un événement spécifique a été créé depuis la dernière exécution de la requête. Certaines applications peuvent consigner une erreur occasionnelle qui ne doit pas nécessairement déclencher une alerte.  Par exemple, l’application peut recommencer le processus à l’origine de l’événement d’erreur et voir sa nouvelle tentative couronnée de succès.  Dans ce cas, la création de l’alerte ne se justifie que si plusieurs événements sont créés au cours d’une période particulière.  

Dans certains cas, vous pouvez créer une alerte en l’absence d’événement.  Par exemple, un processus peut enregistrer des événements réguliers pour indiquer qu’il fonctionne correctement.  S’il ne consigne aucun de ces événements au cours d’une période particulière, une alerte doit être créée.  Dans ce cas, vous devez définir le seuil sur une valeur **inférieure à 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Exemple d’alerte de journal de type Nombre d’enregistrements
Imaginez que vous cherchiez à savoir à quel moment votre application web renvoie aux utilisateurs une réponse avec le code 500, autrement dit une erreur interne au serveur. Il conviendrait de créer une règle d’alerte paramétrée comme suit :  
- **Requête :** requests | where resultCode == "500"<br>
- **Période :** 30 minutes<br>
- **Fréquence des alertes :** 5 minutes<br>
- **Valeur de seuil :** supérieure à 0<br>

L’alerte exécute alors la requête toutes les 5 minutes, avec 30 minutes de données, pour rechercher tous les enregistrements associés à un code de résultat 500. Si un seul enregistrement est trouvé, elle déclenche l’alerte et lance l’action configurée.

### <a name="metric-measurement-alert-rules"></a>Règles d’alerte Mesure métrique

- Les règles d’alerte **Mesure métrique** créent une alerte pour chaque objet d’une requête dont la valeur dépasse un seuil spécifié.  Elles se distinguent des règles d’alerte **Nombre de résultats** comme suit.
- **Fonction d'agrégation** : détermine le calcul à effectuer et, éventuellement, un champ numérique à agréger.  Par exemple, **count()** renvoie le nombre d’enregistrements dans la requête, et **avg(CounterValue)** renvoie la moyenne du champ CounterValue sur l’intervalle. La fonction d’agrégation dans une requête doit être nommée/appelée AggregatedValue et fournir une valeur numérique. 
- **Champ de groupe** : un enregistrement avec une valeur agrégée est créé pour chaque instance de ce champ, et une alerte peut être générée pour chacun.  Par exemple, si vous souhaitez générer une alerte pour chaque ordinateur, vous pourriez utiliser **by Computer**. 

    > [!NOTE]
    > Pour la mesure de métriques, les règles d’alerte basées sur Application Insights, vous pouvez spécifier le champ de regroupement des données. Pour ce faire, utilisez l’option **Agréger sur** dans la définition de règle.   
    
- **Intervalle** : définit l’intervalle de temps pendant lequel les données sont agrégées.  Par exemple, si vous avez spécifié **5 minutes**, un enregistrement est créé pour chaque instance du champ de groupe agrégé à intervalles de 5 minutes sur la période spécifiée pour l’alerte.

    > [!NOTE]
    > Une fonction Bin doit être utilisée dans la requête pour spécifier l’intervalle. Étant donné qu’une commande bin() peut entraîner des intervalles inégaux, une alerte convertit automatiquement une commande bin en commande bin_at avec l’heure appropriée lors de l’exécution, pour garantir des résultats avec un point fixe. Le type d’alerte de journal Mesure de métriques est conçu pour être utilisé avec les requêtes comportant une commande bin() singulière
    
- **Seuil** : le seuil des règles d’alerte Mesure métrique est défini par une valeur d’agrégation et un nombre de violations.  Si un point de données de la recherche dans les journaux dépasse cette valeur, elle est considérée comme une violation.  Si le nombre de violations pour un objet dans les résultats dépasse la valeur spécifiée, une alerte est créée pour cet objet.

#### <a name="example-of-metric-measurement-type-log-alert"></a>Exemple d’alerte de journal de type Mesure de métriques
Prenons le scénario suivant : vous souhaitez créer une alerte si le taux d’utilisation du processeur d’un ordinateur dépasse 90 % à trois reprises en l’espace de 30 minutes.  Il conviendrait de créer une règle d’alerte paramétrée comme suit :  

- **Requête :** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer<br>
- **Période :** 30 minutes<br>
- **Fréquence des alertes :** 5 minutes<br>
- **Valeur agrégée :** supérieure à 90<br>
- **Déclencheur d’alerte basé sur :** nombre total de violations supérieur à 2<br>

La requête créerait une valeur moyenne pour chaque ordinateur à intervalles de 5 minutes.  Cette requête serait exécutée toutes les 5 minutes pour les données collectées au cours des 30 minutes précédentes.  Des exemples de données sont indiqués ci-dessous pour trois ordinateurs.

![Résultats de l’exemple de requête](./media/monitor-alerts-unified/metrics-measurement-sample-graph.png)

Dans cet exemple, des alertes distinctes seraient créées pour srv02 et srv03 dans la mesure où le seuil de 90 % a été dépassé trois fois au cours de la période.  Si le **déclencheur d’alerte** était remplacé par **Consécutif**, une alerte serait créée uniquement pour srv03, car il a dépassé le seuil sur 3 échantillons consécutifs.

## <a name="log-search-alert-rule---firing-and-state"></a>Règle d’alerte Recherche dans les journaux - déclenchement et état
La règle d’alerte Recherche dans les journaux s’applique sur la logique indiquée par l’utilisateur, conformément à la configuration et à la requête d’analyse personnalisée utilisée. La logique de la condition exacte ou le motif de déclenchement de la règle d’alerte est intégré dans une requête Analytics, qui peut varier pour chaque règle d’alerte de journal. Les alertes Azure contiennent peu d’informations sur la cause première sous-jacente dans les résultats du journal lorsque le seuil de la règle d’alerte de la recherche dans les journaux est atteint ou dépassé. Par conséquent, les alertes de journal sont désignées comme sans état et se déclenchent chaque fois que le résultat de la recherche dans les journaux dépasse le seuil spécifié dans les alertes de journal du type de condition *nombre de résultats* ou *mesure de métriques*. Les règles d’alerte de journal vont continuer à se déclencher, tant que la condition d’alerte est remplie par le résultat de la requête d’analyse personnalisée indiquée, sans que l’alerte ait été résolue. Étant donné que la logique de la cause première exacte de l’échec de surveillance est masquée dans la requête d’analyse fournie par l’utilisateur, les alertes Azure ne permettent pas de conclure si le non-respect du seuil par le résultat de la recherche dans les journaux indique que le problème a été résolu.

Imaginons maintenant que nous disposions d’une règle d’alerte de journal appelée *Contoso-Log-Alert*, conformément à la configuration utilisée dans l’[exemple fourni pour l’alerte de journal de type Nombre de résultats](#example-of-number-of-records-type-log-alert). 
- À 13 h 05, lorsque Contoso-Log-Alert a été exécutée par les alertes Azure, le résultat de la recherche dans les journaux a généré 0 enregistrement. Ce résultat est inférieur au seuil et ne déclenche donc pas d’alerte. 
- Lors de l’itération suivante, à 13 h10, lorsque Contoso-Log-Alert a été exécutée par les alertes Azure, le résultat de la recherche dans les journaux a renvoyé 5 enregistrements. Ce résultat dépasse le seuil et déclenche l’alerte, puis déclenche le [groupe d’actions](monitoring-action-groups.md) associé. 
- À 13 h15, lorsque Contoso-Log-Alert a été exécutée par les alertes Azure, le résultat de la recherche dans les journaux a renvoyé 2 enregistrements. Ce résultat dépasse le seuil et déclenche l’alerte, puis déclenche le [groupe d’actions](monitoring-action-groups.md) associé.
- À 13 h 20, lorsque Contoso-Log-Alert a été exécutée par l’alerte Azure, le résultat de la recherche dans les journaux a généré à nouveau 0 enregistrement. Ce résultat est inférieur au seuil et ne déclenche donc pas d’alerte.

Mais dans ce scénario, à 13 h 15, les alertes Azure ne peuvent pas déterminer si les problèmes sous-jacents observés à 13 h 10 persistent et qu’il n’y a pas de nouvelles pannes. Comme la requête fournie par l’utilisateur est susceptible de prendre en compte des enregistrements antérieurs, les alertes Azure ne sont pas fiables. Pour plus de prudence, Contoso-Log-Alert se déclenche à nouveau à 13 h 15 par le biais du [groupe d’actions](monitoring-action-groups.md) configuré. Enfin, à 13 h 20, lorsque aucun enregistrement n’est visible, les alertes Azure ne peuvent pas avoir l’assurance que la cause des enregistrements a été résolue. Par conséquent, Contoso-Log-Alert ne va pas passer à l’état Résolu dans le tableau de bord Azure Alert et/ou les notifications envoyées indiquant la résolution de l’alerte.


## <a name="pricing-and-billing-of-log-alerts"></a>Tarification et facturation des alertes de journal
La tarification applicable aux alertes de journal est présentée à la page [Tarification d’Azure Monitor](https://azure.microsoft.com/en-us/pricing/details/monitor/). Dans les factures d’Azure, les Alertes de journal sont représentées comme étant de type `microsoft.insights/scheduledqueryrules` avec :
- Alertes de journal sur Application Insights, affichées avec le nom exact de l’alerte, ainsi que le groupe de ressources et les propriétés de l’alerte
- Alertes de journal sur Log Analytics, affichées avec le nom de l’alerte sous la forme `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`, ainsi que le groupe de ressources et les propriétés de l’alerte

    > [!NOTE]
    > Le nom de l’ensemble des recherches enregistrées, des planifications et des actions créées avec l’API Log Analytics doit être en minuscules. En cas d’utilisation de caractères non valide tels que `<, >, %, &, \, ?, /`, ceux-ci sont remplacés par `_` sur la facture.

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur la [création d’alertes de journal dans Azure](alert-log.md).
* Comprendre les [webhooks dans les alertes de journal dans Azure](monitor-alerts-unified-log-webhook.md).
* En savoir plus sur [Alertes Azure](monitoring-overview-unified-alerts.md).
* En savoir plus sur [Application Insights](../application-insights/app-insights-analytics.md).
* En savoir plus sur [Log Analytics](../log-analytics/log-analytics-overview.md).    
