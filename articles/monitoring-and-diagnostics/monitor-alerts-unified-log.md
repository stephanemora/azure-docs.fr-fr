---
title: Alertes de journal dans Azure Monitor - Alertes | Microsoft Docs
description: Déclenchez des e-mails, des notifications, des URL de sites web d’appel (webhooks) ou une automatisation lorsque les conditions de requête analytique que vous spécifiez sont remplies pour Alertes Azure.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: vinagara
ms.openlocfilehash: 8bf534177e8236a7d72d6dfdd4612b5f6f492b17
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34057319"
---
# <a name="log-alerts-in-azure-monitor---alerts"></a>Alertes de journal dans Azure Monitor - Alertes 
Cet article fournit des détails sur les alertes de journal, qui sont l’un des types d’alertes pris en charge dans les nouvelles [Alertes Azure](monitoring-overview-unified-alerts.md), et qui permettent aux utilisateurs d’utiliser la plateforme d’analyse d’Azure comme base pour la génération d’alertes. Pour plus d’informations sur les Alertes Métrique à l’aide des Journaux, reportez-vous à la rubrique [Alertes Métrique en quasi temps réel](monitoring-near-real-time-metric-alerts.md)


Une alerte de journal consiste en des règles de recherche dans les journaux créées pour [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) ou [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events).


## <a name="log-search-alert-rule---definition-and-types"></a>Règle d’alerte de recherche dans les journaux - Définition et types

Des règles de recherche dans les journaux sont créées par Alertes Azure pour exécuter automatiquement des requêtes de journal spécifiées à intervalles réguliers.  Si les résultats de la requête de journal répondent à des critères particuliers, un enregistrement d’alerte est généré. La règle peut alors exécuter automatiquement une ou plusieurs actions à l’aide de [groupes d’actions](monitoring-action-groups.md). 

Les règles de recherche dans les journaux sont définies par les détails suivants :
- **Requête de journal**.  La requête qui s’exécute chaque fois que la règle d’alerte est déclenchée.  Les enregistrements renvoyés par cette requête permettent de déterminer si une alerte a été créée. Une requête *Azure Application Insights* peut également inclure des [appels entre applications](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery), pour autant que l’utilisateur dispose de droits d’accès aux applications externes. 

    > [!IMPORTANT]
    > La prise en charge des [requêtes entre applications pour Application Insights](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery) est en préversion. La fonctionnalité et l’expérience utilisateur sont susceptibles de changer. L’utilisation de [requêtes entre espaces de travail](https://dev.loganalytics.io/oms/documentation/3-Using-the-API/CrossResourceQuery) et de [requêtes entre ressources pour Log Analytics](../log-analytics/log-analytics-cross-workspace-search.md) n’est **pas prise en charge** actuellement dans les Alertes Azure.

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

#### <a name="example"></a>Exemples
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
    > Une fonction Bin doit être utilisée dans la requête pour spécifier l’intervalle. Étant donné qu’une commande bin() peut entraîner des intervalles inégaux, une alerte convertit automatiquement une commande bin en commande bin_at avec l’heure appropriée lors de l’exécution, pour garantir des résultats avec un point fixe.
    
- **Seuil** : le seuil des règles d’alerte Mesure métrique est défini par une valeur d’agrégation et un nombre de violations.  Si un point de données de la recherche dans les journaux dépasse cette valeur, elle est considérée comme une violation.  Si le nombre de violations pour un objet dans les résultats dépasse la valeur spécifiée, une alerte est créée pour cet objet.

#### <a name="example"></a>Exemples
Prenons le scénario suivant : vous souhaitez créer une alerte si le taux d’utilisation du processeur d’un ordinateur dépasse 90 % à trois reprises en l’espace de 30 minutes.  Il conviendrait de créer une règle d’alerte paramétrée comme suit :  

- **Requête :** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5 m), Computer<br>
- **Période :** 30 minutes<br>
- **Fréquence des alertes :** 5 minutes<br>
- **Valeur d’agrégation :** supérieure à 90<br>
- **Déclencheur d’alerte basé sur :** nombre total de violations supérieur à 2<br>

La requête créerait une valeur moyenne pour chaque ordinateur à intervalles de 5 minutes.  Cette requête serait exécutée toutes les 5 minutes pour les données collectées au cours des 30 minutes précédentes.  Des exemples de données sont indiqués ci-dessous pour trois ordinateurs.

![Résultats de l’exemple de requête](./media/monitor-alerts-unified/metrics-measurement-sample-graph.png)

Dans cet exemple, des alertes distinctes seraient créées pour srv02 et srv03 dans la mesure où le seuil de 90 % a été dépassé 3 fois au cours de la période.  Si le **déclencheur d’alerte** était remplacé par **Consécutif**, une alerte serait créée uniquement pour srv03, car il a dépassé le seuil sur 3 échantillons consécutifs.


## <a name="log-search-alert-rule---creation-and-modification"></a>Règle d’alerte de recherche dans les journaux - Création et modification

Une alerte de journal, ainsi que sa règle d’alerte de recherche dans les journaux, peut être affichée, créée ou modifiée à partir de :
- Portail Azure
- API REST (y compris via PowerShell)
- Modèles Azure Resource Manager

### <a name="azure-portal"></a>Portail Azure
Depuis l’introduction des [nouvelles alertes Azure](monitoring-overview-unified-alerts.md), les utilisateurs peuvent gérer tous les types d’alertes dans le portail Azure à partir d’un emplacement unique et en suivant des étapes similaires. Pour en savoir plus, voir [utilisation des nouvelles alertes Azure](monitor-alerts-unified-usage.md).

Les utilisateurs peuvent également perfectionner leurs requêtes dans la plateforme Analytics de leur choix dans Azure, puis les *importer pour les utiliser dans Alertes en enregistrant la requête*. Procédure à suivre :
- *Pour Application Insights* : accédez au portail Analytics, puis validez la requête et ses résultats. Enregistrez-la ensuite sous un nom unique dans *Requêtes partagées*.
- *Pour Log Analytics* : accédez à Recherche dans les journaux, puis validez la requête et ses résultats. Enregistrez-la ensuite sous un nom unique dans n’importe quelle catégorie.

Lorsque vous [créez une alerte de journal dans Alertes ](monitor-alerts-unified-usage.md), la requête enregistrée est alors répertoriée en tant que type de signal **Journal (Requête enregistrée)** ; comme illustré dans l’exemple ci-dessous : ![Requête enregistrée importée dans Alertes](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

> [!NOTE]
> L’utilisation de la commande **Journal (Requête enregistrée)** permet d’effectuer une importation dans Alerts. Par conséquent, les modifications effectuées par la suite dans Analytics ne seront pas reflétées dans les règles d’alerte de recherche dans les journaux, et inversement.

### <a name="rest-apis"></a>API REST
Les API fournies pour les alertes de journal sont de type RESTful, et accessibles via l’API REST Azure Resource Manager. Elles sont donc accessibles via PowerShell, ainsi d’autres options permettant de tirer parti des API.

Pour plus de détails, ainsi que des exemples d’utilisation de l’API REST, voir les informations suivantes :
- [API REST d’alerte Log Analytics](../log-analytics/log-analytics-api-alerts.md) : pour créer et gérer les règles d’alerte de recherche dans les journaux pour Azure Log Analytics.
- [API REST de règles de requête planifiée Azure Monitor](https://docs.microsoft.com/en-us/rest/api/monitorr/scheduledqueryrules/) : pour créer et gérer des règles d’alerte de recherche dans les journaux pour Azure Application Insights.

### <a name="azure-resource-manager-template"></a>Modèle Azure Resource Manager
Les utilisateurs peuvent également profiter de la souplesse d’[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) pour créer et mettre à jour des ressources, pour la création ou la mise à jour d’alertes de journal.

Pour plus de détails, ainsi que des exemples d’utilisation des modèles Resource Manager, voir les informations suivantes :
- [Gestion des recherches et alertes enregistrées](monitor-alerts-unified-log-template.md#managing-log-alert-on-log-analytics) pour les alertes de journal basées sur Azure Log Analytics
- [Règle de requête planifiée](monitor-alerts-unified-log-template.md#managing-log-alert-on-application-insights) pour les alertes de journal basées sur Azure Application Insights
 

## <a name="next-steps"></a>Étapes suivantes
* Comprendre les [alertes de journal dans Azure](monitor-alerts-unified-log-webhook.md).
* Découvrir les nouvelles [Alertes Azure](monitoring-overview-unified-alerts.md).
* En savoir plus sur [Application Insights](../application-insights/app-insights-analytics.md).
* En savoir plus sur [Log Analytics](../log-analytics/log-analytics-overview.md).    
