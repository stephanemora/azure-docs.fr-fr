---
title: Alertes de journal dans Azure Monitor
description: Déclenchez des e-mails, des notifications, des URL de sites web d’appel (webhooks) ou une automatisation lorsque les conditions de requête analytique que vous spécifiez sont remplies pour Alertes Azure.
author: yanivlavi
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 5/31/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: b8cae9f7c43098b713d0d5d8f74e46cb0386600c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75396492"
---
# <a name="log-alerts-in-azure-monitor"></a>Alertes de journal dans Azure Monitor

Cet article fournit des informations sur les alertes de journal, qui sont l’un des types d’alertes pris en charge dans les [Alertes Azure](../../azure-monitor/platform/alerts-overview.md), et qui permettent aux utilisateurs d’utiliser la plateforme d’analyse d’Azure comme base pour la génération d’alertes.

Une alerte de journal consiste en des règles de recherche pour les [journaux Azure Monitor](../../azure-monitor/learn/tutorial-viewdata.md) ou [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events). Pour en savoir plus sur son utilisation, voir [Création d’alertes de journal dans Azure](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> Les données de journal populaires des [journaux Azure Monitor](../../azure-monitor/learn/tutorial-viewdata.md) sont désormais également disponibles sur la plateforme de métrique dans Azure Monitor. Pour la vue de détails, consultez [Alerte de métrique pour les journaux d’activité](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Règle d’alerte de recherche dans les journaux - Définition et types

Des règles de recherche dans les journaux sont créées par Alertes Azure pour exécuter automatiquement des requêtes de journal spécifiées à intervalles réguliers.  Si les résultats de la requête de journal répondent à des critères particuliers, un enregistrement d’alerte est généré. La règle peut alors exécuter automatiquement une ou plusieurs actions à l’aide de [groupes d’actions](../../azure-monitor/platform/action-groups.md). Le rôle [Contributeur de surveillance Azure](../../azure-monitor/platform/roles-permissions-security.md) pour la création, la modification et la mise à jour des alertes de journal peut être nécessaire ainsi que des droits d’accès et d’exécution de requête pour la ou les cibles d’analyse dans la règle ou la requête d’alerte. Au cas où l’utilisateur n’a pas accès à toutes les cibles d’analyse d’une règle ou requête d’alerte, la création de règles peut échouer ou la règle d’alerte de journal va renvoyer des résultats partiels.

Les règles de recherche dans les journaux sont définies par les détails suivants :

- **Requête de journal**.  La requête qui s’exécute chaque fois que la règle d’alerte est déclenchée.  Les enregistrements retournés par cette requête permettent de déterminer si une alerte doit être déclenchée. Une requête d’analytique peut concerner un espace de travail Log Analytics ou une application Application Insights spécifique et même porter sur [plusieurs ressources Log Analytics et Application Insights](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) sous réserve que l’utilisateur dispose de droits d’accès et de requête à toutes les ressources. 
    > [!IMPORTANT]
    > La [requête inter-ressources](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) prend en charge les alertes de journal pour Application Insights et les alertes de journal pour [Log Analytics configurées à l’aide de l’API scheduledQueryRules](../../azure-monitor/platform/alerts-log-api-switch.md) uniquement.

    Certaines combinaisons et commandes d’analytique ne peuvent pas être utilisées dans des alertes de journal ; pour plus de détails, consultez [Requêtes d’alertes de journal dans Azure Monitor](../../azure-monitor/platform/alerts-log-query.md).

- **Période**  Spécifie l’intervalle de temps pour la requête. La requête retourne uniquement les enregistrements qui ont été créés dans cette plage précédant son exécution. La période limite les données extraites pour la requête de journal afin d’empêcher les abus, et contourne toute commande de temps (comme « il y a ») utilisée dans une requête de journal. <br>*Par exemple, si la période est définie sur 60 minutes et la requête exécutée à 13 h 15, seuls les enregistrements créés entre 12 h 15 et 13 h 15 sont retournés pour l’exécution de la requête de journal. Maintenant, si la requête de journal utilise une commande de temps telle que « il y a » (7d), la requête de journal est exécutée uniquement pour les données collectées entre 12 h 15 et 13 h 15, comme si celles-ci n’avaient existé que pendant les 60 minutes passées, et non pendant les sept jours de données spécifiés dans la requête de journal.*

- **Fréquence**.  Spécifie la fréquence à laquelle la requête doit être exécutée. Peut être toute valeur comprise entre 5 minutes et 24 heures. La valeur doit être inférieure ou égale à la période.  Si la valeur est supérieure à la période, vous risquez de manquer des enregistrements.<br>*Par exemple, imaginons une période de 30 minutes associée à une fréquence de 60 minutes.  Si la requête est exécutée à 13 h, les enregistrements entre 12 h 30 et 13 h sont renvoyés.  La requête s’exécute ensuite à 14 h, moment auquel elle renvoie les enregistrements entre 13 h 30 et 14 h.  Les enregistrements créés entre 13 h 00 et 13 h 30 ne sont jamais analysés.*

- **Seuil**.  Les résultats de la recherche dans les journaux sont évalués pour déterminer si une alerte doit être créée.  Le seuil diffère selon le type de règle d’alerte de recherche dans les journaux.

Les règles de recherche dans les journaux, qu’il s’agisse de [journaux Azure Monitor](../../azure-monitor/learn/tutorial-viewdata.md) ou d’[Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events), peuvent être de deux types. Chacun de ces types est décrit en détail dans les sections suivantes.

- **[Nombre de résultats](#number-of-results-alert-rules)** . Alerte unique créée lorsque le nombre d’enregistrements renvoyés par la recherche dans les journaux dépasse un nombre spécifié.
- **[Mesure métrique](#metric-measurement-alert-rules)** .  Alerte créée pour chaque objet des résultats de la recherche dans les journaux dont les valeurs dépassent le seuil spécifié.

Les différences entre les types de règles d’alerte sont présentées ci-dessous.

- Une règle d’alerte *Nombre de résultats* crée toujours une alerte unique, tandis que la règle d’alerte *Mesure de métriques* crée une alerte pour chaque objet dépassant le seuil.
- Les règles d’alerte *Nombre de résultats* créent une alerte quand le seuil est dépassé une seule fois. Les règles d’alerte *Mesure métrique* peuvent créer une alerte lorsque le seuil est dépassé un certain nombre de fois au cours d’un intervalle de temps spécifique.

### <a name="number-of-results-alert-rules"></a>Règles d’alerte Nombre de résultats

Les règles d’alerte **Nombre de résultats** créent une alerte unique lorsque le nombre d’enregistrement renvoyés par la requête de recherche dépasse le seuil spécifié. Ce type de règle d’alerte est idéal pour la gestion des événements, par exemple les journaux des événements Windows, les journaux d’activité Syslog, la réponse WebApp et les journaux d’activité personnalisés.  Vous pouvez créer une alerte quand un événement d’erreur particulier est créé, ou quand plusieurs événements d’erreur sont créés au cours d’une période particulière.

**Seuil** : Le seuil pour les règles d’alerte Nombre de résultats est supérieur ou inférieur à une valeur particulière.  Si le nombre d’enregistrements renvoyés par cette recherche dans les journaux satisfait à ce critère, une alerte est créée.

Pour déclencher une alerte sur un événement unique, définissez le nombre de résultats sur une valeur supérieure à 0 et vérifiez si un événement spécifique a été créé depuis la dernière exécution de la requête. Certaines applications peuvent consigner une erreur occasionnelle qui ne doit pas nécessairement déclencher une alerte.  Par exemple, l’application peut recommencer le processus à l’origine de l’événement d’erreur et voir sa nouvelle tentative couronnée de succès.  Dans ce cas, la création de l’alerte ne se justifie que si plusieurs événements sont créés au cours d’une période particulière.  

Dans certains cas, vous pouvez créer une alerte en l’absence d’événement.  Par exemple, un processus peut enregistrer des événements réguliers pour indiquer qu’il fonctionne correctement.  S’il ne consigne aucun de ces événements au cours d’une période particulière, une alerte doit être créée.  Dans ce cas, vous devez définir le seuil sur une valeur **inférieure à 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Exemple d’alerte de journal de type Nombre d’enregistrements

Imaginez que vous cherchiez à savoir à quel moment votre application web renvoie aux utilisateurs une réponse avec le code 500, autrement dit une erreur interne au serveur. Il conviendrait de créer une règle d’alerte paramétrée comme suit :  

- **Requête :** requests | where resultCode == "500"<br>
- **Période :** 30 minutes<br>
- **Fréquence des alertes :** 5 minutes<br>
- **Valeur de seuil :** Supérieur à 0<br>

L’alerte exécute alors la requête toutes les 5 minutes, avec 30 minutes de données, pour rechercher tous les enregistrements associés à un code de résultat 500. Si un seul enregistrement est trouvé, elle déclenche l’alerte et lance l’action configurée.

### <a name="metric-measurement-alert-rules"></a>Règles d’alerte Mesure métrique

Les règles d’alerte **Mesure métrique** créent une alerte pour chaque objet d’une requête dont la valeur dépasse un seuil spécifié et une condition de déclenchement spécifiée. Contrairement aux règles d’alerte **Nombre de résultats**, les règles d’alerte **Mesure métrique** fonctionnent lorsque le résultat analytique fournit une série chronologique. Elles se distinguent des règles d’alerte **Nombre de résultats** comme suit.

- **Fonction d’agrégation** : Détermine le calcul à effectuer et, éventuellement, un champ numérique à agréger.  Par exemple, **count()** renvoie le nombre d’enregistrements dans la requête, et **avg(CounterValue)** renvoie la moyenne du champ CounterValue sur l’intervalle. La fonction d’agrégation dans une requête doit être nommée/appelée : AggregatedValue et fournir une valeur numérique. 

- **Champ de groupe** : Un enregistrement avec une valeur agrégée est créé pour chaque instance de ce champ, et une alerte peut être générée pour chacun.  Par exemple, si vous souhaitez générer une alerte pour chaque ordinateur, vous pourriez utiliser **by Computer**. Dans le cas où plusieurs champs de groupe sont spécifiés dans une requête d’alerte, l’utilisateur peut spécifier le champ à utiliser pour trier les résultats à l’aide du paramètre **Agréger sur** (metricColumn)

    > [!NOTE]
    > L’option *Agréger sur* (metricColumn) est disponible uniquement pour les alertes de journal de type Mesure de métriques pour Application Insights et pour les alertes de journal pour [Log Analytics configurées à l’aide d’API scheduledQueryRules](../../azure-monitor/platform/alerts-log-api-switch.md).

- **Intervalle** :  Définit l’intervalle de temps pendant lequel les données sont agrégées.  Par exemple, si vous avez spécifié **5 minutes**, un enregistrement est créé pour chaque instance du champ de groupe agrégé à intervalles de 5 minutes sur la période spécifiée pour l’alerte.

    > [!NOTE]
    > Une fonction Bin doit être utilisée dans la requête pour spécifier l’intervalle. Étant donné qu’une commande bin() peut entraîner des intervalles inégaux, une alerte convertit automatiquement une commande bin en commande bin_at avec l’heure appropriée lors de l’exécution, pour garantir des résultats avec un point fixe. Le type d’alerte de journal Mesure de métriques est conçu pour être utilisé avec les requêtes comportant jusqu’à trois instances d’une commande bin().
    
- **Seuil** : Le seuil des règles d’alerte Mesure métrique est défini par une valeur d’agrégation et un nombre de violations.  Si un point de données de la recherche dans les journaux dépasse cette valeur, elle est considérée comme une violation.  Si le nombre de violations pour un objet dans les résultats dépasse la valeur spécifiée, une alerte est créée pour cet objet.

Une configuration incorrecte de l’option *Agréger sur* ou *metricColumn* peut entraîner un mauvais déclenchement des règles d’alerte. Pour plus d’informations, consultez la [section de résolution La règle d’alerte Mesure métrique est incorrecte](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect).

#### <a name="example-of-metric-measurement-type-log-alert"></a>Exemple d’alerte de journal de type Mesure de métriques

Prenons le scénario suivant : vous souhaitez créer une alerte si le taux d’utilisation du processeur d’un ordinateur dépasse 90 % à trois reprises en l’espace de 30 minutes.  Il conviendrait de créer une règle d’alerte paramétrée comme suit :  

- **Requête :** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer<br>
- **Période :** 30 minutes<br>
- **Fréquence des alertes :** 5 minutes<br>
- **Logique d'alerte - Condition et seuil :** Supérieur à 90<br>
- **Champ de groupe (Aggregate-on) :** Computer
- **Déclencher l’alerte selon :** nombre total de violations supérieur à 2<br>

La requête créerait une valeur moyenne pour chaque ordinateur à intervalles de 5 minutes.  Cette requête serait exécutée toutes les 5 minutes pour les données collectées au cours des 30 minutes précédentes. Le champ de groupe choisi (Aggregate-on) correspondant à la colonne « Ordinateur », la valeur AggregatedValue est fractionnée pour différentes valeurs « Ordinateur » et l'utilisation moyenne du processeur de chaque ordinateur est déterminée pour une période de 5 minutes.  À titre d'exemple, le résultat de la requête pour trois ordinateurs se présenterait comme suit.


|TimeGenerated [UTC] |Computer  |AggregatedValue  |
|---------|---------|---------|
|20XX-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20XX-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20XX-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Si le résultat de la requête devait être tracé, il se présenterait comme suit.

![Résultats de l’exemple de requête](media/alerts-unified-log/metrics-measurement-sample-graph.png)

Dans cet exemple, nous voyons, par période de 5 minutes pour chacun des trois ordinateurs - utilisation moyenne du processeur telle que calculée pour 5 minutes. Seuil de 90 violé par srv01 une seule fois à 1:25. En comparaison, srv02 dépasse le seuil de 90 à 1:10, 1:15 et 1:25, alors que srv03 le dépasse de 90 à 1:10, 1:15, 1:20 et 1:30.
L'alerte étant configurée pour se déclencher au-delà de deux violations, nous constatons que seuls srv02 et srv03 répondent aux critères. Ainsi, des alertes distinctes seraient créées pour srv02 et srv03 dans la mesure où le seuil de 90 % a été dépassé deux fois au cours de plusieurs périodes.  Si le paramètre *Déclencher l’alerte selon :* était configuré pour l’option *Violations continues*, une alerte serait déclenchée **uniquement** pour srv03, car il a dépassé le seuil lors de trois périodes consécutives entre 1:10 et 1:20. Et **non** pour srv02, car il a dépassé le seuil lors de deux périodes entre 1:10 et 1:15.

## <a name="log-search-alert-rule---firing-and-state"></a>Règle d’alerte Recherche dans les journaux - déclenchement et état

Les règles d’alerte de recherche dans les journaux fonctionnent uniquement sur la logique que vous intégrez à la requête. Le système d’alerte n’a pas d’autre contexte sur l’état du système, votre intention ou la cause première impliquée par la requête. Par conséquent, les alertes de journal sont désignées comme sans état. Les conditions sont évaluées comme « TRUE » ou « FALSE » chaque fois qu’elles sont exécutées.  Une alerte est déclenchée chaque fois que l’évaluation de la condition d’alerte est « TRUE », qu’elle ait ou non été déclenchée précédemment.    

Examinons ce comportement en action avec un exemple pratique. Supposons que nous disposons d’une règle d’alerte de journal appelée *Contoso-Log-Alert*, configurée comme indiqué dans l’[exemple d’alerte de journal de type Nombre de résultats](#example-of-number-of-records-type-log-alert). La condition est une requête d’alerte personnalisée conçue pour rechercher le code de résultat 500 dans les journaux. Si un plus grand nombre de codes de résultats 500 sont trouvés dans les journaux, la condition de l’alerte est vraie. 

À chaque intervalle ci-dessous, le système d’alertes Azure évalue la condition de l’alerte *Contoso-Log-Alert*.


| Temps    | Nombre d’enregistrements renvoyé par la requête de recherche dans les journaux | Évaluation de la condition de journal | Résultats 
| ------- | ----------| ----------| ------- 
| 13h05 | 0 enregistrement | 0 n’est pas > 0, donc FALSE |  L’alerte ne se déclenche pas. Aucune action n’est appelée.
| 13h10 | 2 enregistrements | 2 > 0 donc TRUE  | L’alerte se déclenche et les groupes d’actions sont appelés. État d’alerte ACTIF.
| 13h15 | 5 enregistrements | 5 > 0 donc TRUE  | L’alerte se déclenche et les groupes d’actions sont appelés. État d’alerte ACTIF.
| 13h20 | 0 enregistrement | 0 n’est pas > 0, donc FALSE |  L’alerte ne se déclenche pas. Aucune action n’est appelée. État d’alerte laissé ACTIF.

En utilisant le cas précédent comme exemple :

À 13h15, les alertes Azure ne peuvent pas déterminer si les problèmes sous-jacents observés à 13h10 persistent et si les enregistrements sont de nouveaux échecs nets ou des répétitions d’échecs plus anciens à 13h10. La requête fournie par l’utilisateur peut ou non prendre en compte les enregistrements précédents et le système ne le sait pas. Le système d’alertes Azure est conçu pour s’exécuter avec prudence et il déclenche l’alerte et les actions associées à nouveau à 13h15. 

À 13h20, lors de la détection de zéro enregistrement avec un code de résultat de 500, les alertes Azure ne peuvent pas être certaines que la cause du code de résultat 500 observé à 13h10 et à 13h15 est désormais résolue. Il ne sait pas si les problèmes d’erreur 500 se reproduiront pour les mêmes raisons. Par conséquent, *Contoso-Log-Alert* ne passe pas à l’état **Résolu** dans le tableau de bord Azure Alert et/ou des notifications ne sont pas envoyées pour indiquer la résolution de l’alerte. Vous seul qui comprenez la condition ou la raison exacte pour la logique incorporée dans la requête Analytics pouvez [marquer l’alerte comme étant fermée](alerts-managing-alert-states.md) en fonction des besoins.

## <a name="pricing-and-billing-of-log-alerts"></a>Tarification et facturation des alertes de journal

La tarification applicable aux alertes de journal est présentée à la page [Tarification d’Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). Dans les factures d’Azure, les Alertes de journal sont représentées comme étant de type `microsoft.insights/scheduledqueryrules` avec :

- Alertes de journal sur Application Insights, affichées avec le nom exact de l’alerte, ainsi que le groupe de ressources et les propriétés de l’alerte
- Alertes de journal sur Log Analytics, affichées avec le nom exact de l’alerte ainsi que le groupe de ressources et les propriétés de l’alerte (création avec l’[API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules))

L’[API Log Analytics héritée](../../azure-monitor/platform/api-alerts.md) présente des actions d’alerte et des planifications dans le cadre de la recherche enregistrée Log Analytics, et non des [ressources Azure](../../azure-resource-manager/management/overview.md) propres. Ainsi, pour activer la facturation pour de telles alertes de journal héritées créées pour Log Analytics à l’aide du portail Microsoft Azure **sans** [basculer vers la nouvelle API](../../azure-monitor/platform/alerts-log-api-switch.md) ou via l’[API Log Analytics héritée](../../azure-monitor/platform/api-alerts.md), des pseudo règles d’alerte masquées sont créées sur `microsoft.insights/scheduledqueryrules` pour la facturation sur Azure. Les pseudo règles d’alerte masquées créées pour la facturation sur `microsoft.insights/scheduledqueryrules` comme indiqué en tant que `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`, de pair avec les propriétés de groupe de ressources et d’alerte.

> [!NOTE]
> Si des caractères non valides tels que `<, >, %, &, \, ?, /` sont présents, ils seront remplacés par `_` dans le nom de la pseudo règle d’alerte masquée et par conséquent, également dans la facture Azure.

Pour supprimer les ressources scheduleQueryRules masquées créées pour la facturation des règles d’alerte à l’aide de l’[API Log Analytics héritée](api-alerts.md), l’utilisateur peut effectuer l’une des opérations suivantes :

- N’importe quel utilisateur peut [changer la préférence d’API pour les règles d’alerte sur l’espace de travail Log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) et sans perte de ses règles d’alerte ou déplacement de surveillance vers l’API [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) conforme à Azure Resource Manager. Ainsi, vous n’avez plus besoin de créer des pseudo règles d’alerte masquées pour la facturation.
- Ou si l’utilisateur ne souhaite pas changer de préférence d’API, l’utilisateur devra **supprimer** la planification d’origine et l’action d’alerte au moyen de l’[API Log Analytics héritée](api-alerts.md) ou supprimer dans le [portail Microsoft Azure la règle d’alerte de journal d’origine](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal)

En outre, pour les ressources scheduleQueryRules masquées qui ont été créées pour la facturation des règles d’alerte à l’aide de l’[API Log Analytics héritée](api-alerts.md), toute opération de modification comme PUT échouera. Comme le type `microsoft.insights/scheduledqueryrules`, les pseudo règles ont pour objectif la facturation des règles d’alerte créées à l’aide de l’[API Log Analytics héritée](api-alerts.md). Toute modification de règle d’alerte doit être effectuée à l’aide de l’[API Log Analytics héritée](api-alerts.md) (ou) l’utilisateur peut [changer de préférence d’API pour les règles d’alerte](../../azure-monitor/platform/alerts-log-api-switch.md) afin d’utiliser l’[API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) à la place.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la [création d’alertes de journal dans Azure](../../azure-monitor/platform/alerts-log.md).
* Comprendre les [webhooks dans les alertes de journal dans Azure](alerts-log-webhook.md).
* En savoir plus sur [Alertes Azure](../../azure-monitor/platform/alerts-overview.md).
* En savoir plus sur [Application Insights](../../azure-monitor/app/analytics.md).
* En savoir plus sur [Log Analytics](../../azure-monitor/log-query/log-query-overview.md).
