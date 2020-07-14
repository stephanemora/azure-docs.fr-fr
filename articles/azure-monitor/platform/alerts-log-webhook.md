---
title: Actions webhook pour les alertes de journal dans les alertes Azure
description: Cet article explique comment créer une règle d’alerte de journal à l’aide de l’espace de travail Log Analytics ou Application Insights, comment l’alerte envoie les données en tant que webhook HTTP et les détails des différentes personnalisations possibles.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 6c9bacfc4354351cbbf2eb735414ff3334cd7d0a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84323669"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Actions webhook pour les règles d’alerte de journal
Quand une [alerte de journal est créée dans Azure](alerts-log.md), vous avez l’option de la [configuration à l’aide de groupes d’actions](action-groups.md) pour exécuter une ou plusieurs actions. Cet article décrit les différentes actions webhook disponibles et les détails de la configuration du webhook personnalisé basé sur JSON.

> [!NOTE]
> Vous pouvez également utiliser le [schéma d’alerte commun](https://aka.ms/commonAlertSchemaDocs) pour vos intégrations de webhook. Le schéma d’alerte commun offre l’avantage de générer une seule charge utile d’alerte extensible et unifiée sur tous les services d’alerte dans Azure Monitor. Notez que le schéma d’alerte commun n’honore pas l’option JSON personnalisée pour les alertes de journal. Elle défère à la charge utile du schéma d’alerte commun si celle-ci est sélectionnée, quelle que soit la personnalisation que vous pouvez avoir effectuée au niveau des règles d’alerte. [En savoir plus sur les définitions de schéma d’alerte commun.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Actions de webhook

Les actions de webhook permettent d’appeler un processus externe par le biais d’une simple requête HTTP POST. Le service appelé doit prendre en charge les webhooks et déterminer comment il doit utiliser toute charge utile qu’il reçoit.

Les propriétés requises par les actions webhook sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| **URL du webhook** |URL du webhook. |
| **Charge utile JSON personnalisée** |La charge utile personnalisée à envoyer avec le webhook, lorsque vous choisissez cette option au moment de la création de l’alerte. Pour plus d’informations, consultez [Gérer les alertes de journal](alerts-log.md).|

> [!NOTE]
> Le bouton **Afficher le webhook** à côté de l’option **Inclure la charge utile JSON personnalisée pour le webhook** de l’alerte de journal affiche l’exemple de charge utile du webhook pour la personnalisation fournie. Il ne contient aucune donnée réelle et représentative du schéma JSON utilisé pour les alertes de journal. 

Les webhooks incluent une URL et une charge utile au format JSON qui correspond aux données envoyées au service externe. Par défaut, la charge utile comprend les valeurs du tableau suivant. Vous pouvez choisir de remplacer cette charge utile par une charge utile personnalisée de votre choix. Dans ce cas, utilisez les variables de chacun des paramètres indiquées dans le tableau pour inclure les valeurs correspondantes dans votre charge utile personnalisée.


| Paramètre | Variable | Description |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Nom de la règle d’alerte. |
| *Niveau de gravité* |#severity |Gravité définie pour l’alerte de journal déclenchée. |
| *AlertThresholdOperator* |#thresholdoperator |Opérateur de seuil pour la règle d’alerte, qui utilise supérieur à ou inférieur à. |
| *AlertThresholdValue* |#thresholdvalue |Valeur de seuil de la règle d’alerte. |
| *LinkToSearchResults* |#linktosearchresults |Lien vers le portail Analytics qui retourne les enregistrements de la requête ayant créé l’alerte. |
| *LinkToSearchResultsAPI* |#linktosearchresultsapi |Lien vers l’API Analytics qui retourne les enregistrements de la requête ayant créé l’alerte. |
| *LinkToFilteredSearchResultsUI* |#linktofilteredsearchresultsui |Lien vers le portail Analytics qui retourne les enregistrements de la requête filtrés par combinaisons de valeurs de dimensions ayant créé l’alerte. |
| *LinkToFilteredSearchResultsAPI* |#linktofilteredsearchresultsapi |Lien vers l’API Analytics qui retourne les enregistrements de la requête filtrés par combinaisons de valeurs de dimensions ayant créé l’alerte. |
| *ResultCount* |#searchresultcount |Nombre d’enregistrements dans les résultats de recherche. |
| *Heure de fin de l’intervalle de recherche* |#searchintervalendtimeutc |Heure de fin de la requête au format UTC, au format mm/jj/aaaa HH:mm:ss AM/PM. |
| *Intervalle de recherche* |#searchinterval |Fenêtre de temps de la règle d’alerte, au format HH:mm:ss. |
| *Heure de début de l’intervalle de recherche* |#searchintervalstarttimeutc |Heure de début de la requête au format UTC, au format mm/jj/aaaa HH:mm:ss AM/PM. 
| *SearchQuery* |#searchquery |Requête de recherche de journal utilisée par la règle d’alerte. |
| *SearchResults* |"IncludeSearchResults": true|Enregistrements retournés par la requête sous forme de table JSON, limitée aux 1 000 premiers enregistrements. « IncludeSearchResults » : true est ajouté à une définition de webhook JSON personnalisée en tant que propriété de niveau supérieur. |
| *Dimensions* |« IncludeDimension »" : true|Combinaisons de valeurs de dimensions ayant déclenché une alerte en tant que section JSON. « IncludeDimensions » : true est ajouté à une définition de webhook JSON personnalisée en tant que propriété de niveau supérieur. |
| *Type d’alerte*| #alerttype | Le type de règle d’alerte de journal configuré en tant que [Mesure de métriques](alerts-unified-log.md#metric-measurement-alert-rules) ou [Nombre de résultats](alerts-unified-log.md#number-of-results-alert-rules).|
| *WorkspaceID* |#workspaceid |ID de votre espace de travail Log Analytics. |
| *ID d’application* |#applicationid |ID de votre application Application Insights. |
| *Identifiant d’abonnement* |#subscriptionid |ID de votre abonnement Azure utilisé. 

> [!NOTE]
> Les liens fournis transmettent des paramètres, tels que *SearchQuery*, *Search Interval StartTime* et *Search Interval End time*, dans l’URL du portail Azure ou l’API.

Par exemple, vous pouvez spécifier la charge utile personnalisée suivante qui inclut un paramètre unique appelé *text*. Le service appelé par ce webhook s’attendrait à recevoir ce paramètre.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Cette charge utile donne ce qui suit quand elle est envoyée au webhook :

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Étant donné que toutes les variables d’un webhook personnalisé doivent être spécifiées dans un boîtier JSON, comme « #searchinterval », le webhook qui en résulte possède également des données variables dans des boîtiers, comme « 00:05:00 ».

Pour inclure les résultats de la recherche dans une charge utile personnalisée, vérifiez que **IncudeSearchResults** est défini comme une propriété de niveau supérieur dans la charge utile JSON. 

## <a name="sample-payloads"></a>Exemples de charges utiles
Cette section présente des exemples de charge utile pour les webhooks pour les alertes de journal. Les exemples de charge utile incluent des exemples lorsque la charge utile est standard et quand elle est personnalisée.

### <a name="standard-webhook-for-log-alerts"></a>Webhook standard pour les alertes de journal 
Ces deux exemples ont une charge utile fictive ne comprenant que deux colonnes et deux lignes.

#### <a name="log-alert-for-log-analytics"></a>Alerte de journal pour Log Analytics
L’exemple de charge utile suivant est destiné à une action de webhook standard *sans option JSON personnalisée* utilisée pour les alertes basées sur Log Analytics :

```json
{
    "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
    "AlertRuleName": "AcmeRule",
    "SearchQuery": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "LinkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "Description": "log alert rule",
    "Severity": "Warning",
    "AffectedConfigurationItems": [
        "INC-Gen2Alert"
    ],
    "Dimensions": [
        {
            "name": "Computer",
            "value": "INC-Gen2Alert"
        }
    ],
    "SearchResult": {
        "tables": [
            {
                "name": "PrimaryResult",
                "columns": [
                    {
                        "name": "$table",
                        "type": "string"
                    },
                    {
                        "name": "Computer",
                        "type": "string"
                    },
                    {
                        "name": "TimeGenerated",
                        "type": "datetime"
                    }
                ],
                "rows": [
                    [
                        "Fabrikam",
                        "33446677a",
                        "2018-02-02T15:03:12.18Z"
                    ],
                    [
                        "Contoso",
                        "33445566b",
                        "2018-02-02T15:16:53.932Z"
                    ]
                ]
            }
        ]
    },
    "WorkspaceId": "12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
}
 ```

> [!NOTE]
> La valeur du champ « Gravité » peut changer si vous avez [modifié votre préférence API](alerts-log-api-switch.md) pour les alertes de journal sur Log Analytics.


#### <a name="log-alert-for-application-insights"></a>Alerte de journal pour Application Insights
L’exemple de charge utile suivant concerne un webhook standard *sans option JSON personnalisée* lorsqu’il est utilisé pour les alertes de journal basées sur application Insights :
    
```json
{
    "schemaId": "Microsoft.Insights/LogAlert",
    "data": {
        "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
        "AlertRuleName": "AcmeRule",
        "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
        "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
        "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
        "AlertThresholdOperator": "Greater Than",
        "AlertThresholdValue": 0,
        "ResultCount": 2,
        "SearchIntervalInSeconds": 3600,
        "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "Description": null,
        "Severity": "3",
        "Dimensions": [
            {
                "name": "IP",
                "value": "1.1.1.1"
            }
        ],
        "SearchResult": {
            "tables": [
                {
                    "name": "PrimaryResult",
                    "columns": [
                        {
                            "name": "$table",
                            "type": "string"
                        },
                        {
                            "name": "Id",
                            "type": "string"
                        },
                        {
                            "name": "Timestamp",
                            "type": "datetime"
                        }
                    ],
                    "rows": [
                        [
                            "Fabrikam",
                            "33446677a",
                            "2018-02-02T15:03:12.18Z"
                        ],
                        [
                            "Contoso",
                            "33445566b",
                            "2018-02-02T15:16:53.932Z"
                        ]
                    ]
                }
            ]
        },
        "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
        "AlertType": "Metric measurement"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Alerte de journal avec charge utile JSON personnalisée
Par exemple, pour créer une charge utile personnalisée qui inclut uniquement le nom de l’alerte et les résultats de recherche, vous pouvez utiliser ce qui suit : 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Voici un exemple de charge utile pour une action de webhook personnalisée pour une alerte de journal quelconque :
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [alertes de journal dans les alertes Azure ](alerts-unified-log.md).
- Comprendre comment [gérer les alertes de journal dans Azure](alerts-log.md).
- Créer et gérer des [groupes d’actions dans Azure](action-groups.md).
- En savoir plus sur [Application Insights](../../azure-monitor/app/analytics.md).
- Découvrez plus en détail les [requêtes dans les journaux](../log-query/log-query-overview.md). 

