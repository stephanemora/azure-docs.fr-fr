---
title: Actions webhook pour les alertes de journal dans Alertes Azure
description: Cet article décrit comment à une règle d’alerte de journal à l’aide de journal analytique espace de travail ou application insights, transmet les données en tant que webhook HTTP et les détails des différentes personnalisations possibles.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 2307fa985c88608d80400c8951c47b9f20caa1dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515525"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Actions webhook pour les règles d’alerte de journal
Quand une [alerte de journal est créée dans Azure](alerts-log.md), vous avez l’option de la [configuration à l’aide de groupes d’actions](action-groups.md) pour exécuter une ou plusieurs actions.  Cet article décrit les différentes actions webhook disponibles et les détails de la configuration du webhook personnalisé basé sur JSON.


## <a name="webhook-actions"></a>Actions de webhook

Les actions de webhook permettent d’appeler un processus externe par le biais d’une simple requête HTTP POST.  Le service appelé doit prendre en charge les webhooks et déterminer comment il doit utiliser toute charge utile qu’il reçoit.    

Les propriétés requises par les actions webhook sont décrites dans le tableau suivant :

| Propriété | Description |
|:--- |:--- |
| URL du webhook |URL du webhook. |
| Charge utile JSON personnalisée |Charge utile personnalisée à envoyer avec le webhook, lorsque vous choisissez cette option au moment de la création de l’alerte. Détails disponibles dans [Gérer les alertes de journal](alerts-log.md) |

> [!NOTE]
> L’utilisation du bouton Afficher le Webhook avec l’option *Inclure une charge utile Json personnalisée pour webhook* pour Alerte du journal affiche un exemple de charge utile de webhook pour la personnalisation fournie. Il ne contient aucune donnée réelle et représentative du schéma JSON utilisé pour les alertes de journal. 

Les webhooks incluent une URL et une charge utile au format JSON qui correspond aux données envoyées au service externe.  Par défaut, la charge utile comprend les valeurs du tableau suivant :  Vous pouvez choisir de remplacer cette charge utile par une charge utile personnalisée de votre choix.  Dans ce cas, vous pouvez reprendre les variables de chacun des paramètres indiquées dans le tableau pour inclure les valeurs correspondantes dans votre charge utile personnalisée.


| Paramètre | Variable | Description |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Nom de la règle d’alerte. |
| Severity |#severity |Gravité définie pour l’alerte de journal déclenchée. |
| AlertThresholdOperator |#thresholdoperator |Opérateur de seuil de la règle d’alerte.  *Supérieur à* ou *Inférieur à*. |
| AlertThresholdValue |#thresholdvalue |Valeur de seuil de la règle d’alerte. |
| LinkToSearchResults |#linktosearchresults |Lien vers le portail Analytics qui retourne les enregistrements de la requête ayant créé l’alerte. |
| ResultCount |#searchresultcount |Nombre d’enregistrements dans les résultats de recherche. |
| Heure de fin de l’intervalle de recherche |#searchintervalendtimeutc |Heure de fin de la requête au format UTC, format - mm/jj/aaaa HH:mm:ss AM/PM. |
| Intervalle de recherche |#searchinterval |Fenêtre de temps de la règle d’alerte, format - HH:mm:ss. |
| Heure de début de l’intervalle de recherche |#searchintervalstarttimeutc |Heure de début de la requête au format UTC, format - mm/jj/aaaa HH:mm:ss AM/PM. 
| SearchQuery |#searchquery |Requête de recherche de journal utilisée par la règle d’alerte. |
| SearchResults |"IncludeSearchResults": true|Enregistrements retournés par la requête en tant que tableau JSON, limités aux 1 000 premiers enregistrements ; si "IncludeSearchResults": true est ajouté dans la définition de webhook JSON personnalisé en tant que propriété de niveau supérieur. |
| WorkspaceID |#workspaceid |ID de votre espace de travail Log Analytics. |
| ID de l'application |#applicationid |ID de votre application Application Insights. |
| Identifiant d’abonnement |#subscriptionid |ID de l’abonnement Azure utilisé avec Application Insights. 

> [!NOTE]
> LinkToSearchResults passe des paramètres comme SearchQuery, Search Interval StartTime et Search Interval End Time dans l’URL au portail Azure pour les afficher dans la section Analytics. Portail Azure a l’URI de la taille limite d’environ 2 000 caractères et sera *pas* ouvrir le lien fourni dans les alertes, si les valeurs de paramètres dépassent la limite de ladite. Les utilisateurs peuvent entrer manuellement des détails pour afficher les résultats dans le portail Analytics ou utiliser [l’API REST Application Insights Analytics](https://dev.applicationinsights.io/documentation/Using-the-API) ou [l’API REST Log Analytics](/rest/api/loganalytics/) pour récupérer les résultats par programmation. 

Par exemple, vous pouvez spécifier la charge utile personnalisée suivante qui inclut un paramètre unique appelé *text*.  Le service appelé par ce webhook s’attendrait à recevoir ce paramètre.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Cette charge utile peut donner ce qui suit quand elle est envoyée au webhook.

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Comme toutes les variables dans un webhook personnalisé doivent être spécifiées dans un boîtier JSON comme « #searchinterval », le webhook résultant aura également des données de variable à l’intérieur d’un boîtier comme « 00:05:00 ».

Pour inclure les résultats de la recherche dans une charge utile personnalisée, vérifiez que **IncudeSearchResults** est défini comme une propriété de niveau supérieur dans la charge utile json. 

## <a name="sample-payloads"></a>Exemples de charges utiles
Cette section présente une exemple de charge utile de webhook pour les alertes de journal, notamment lorsque la charge utile est standard et quand elle est personnalisée.

### <a name="standard-webhook-for-log-alerts"></a>Webhook standard pour les alertes de journal 
Ces deux exemples indiquaient une charge utile fictive ne comprenant que deux colonnes et deux lignes.

#### <a name="log-alert-for-azure-log-analytics"></a>Alerte de journal pour Azure Log Analytics
Voici un exemple de charge utile pour une action standard webhook *sans utiliser d’option Json personnalisée* pour des alertes de journal basées sur une analytique des journaux d’activité.

```json
{
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
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
        },
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Warning"
 }
 ```

> [!NOTE]
> Valeur du champ gravité peut changer si vous avez [basculé votre préférence de l’API](alerts-log-api-switch.md) pour les alertes de journal sur le journal Analytique.


#### <a name="log-alert-for-azure-application-insights"></a>Alerte de journal pour Azure Application Insights
Voici un exemple de charge utile pour un webhook standard *sans option Json personnalisée* en cas d’utilisation pour des alertes de journal basées sur des informations d’application.
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
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
        },
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://analytics.applicationinsights.io/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Alerte de journal avec charge utile JSON personnalisée
Par exemple, pour créer une charge utile personnalisée qui inclut uniquement le nom de l’alerte et les résultats de recherche, vous pouvez utiliser ce qui suit : 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Voici un exemple de charge utile pour une action de webhook personnalisée pour une alerte de journal quelconque.
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
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
- En savoir plus sur les [alertes de journal dans les alertes Azure ](alerts-unified-log.md)
- Comprendre la [gestion des alertes de journal dans Azure](alerts-log.md)
- Créer et gérer des [groupes d’actions dans Azure](action-groups.md)
- En savoir plus sur [Application Insights](../../azure-monitor/app/analytics.md)
- En savoir plus sur [enregistrer des requêtes](../log-query/log-query-overview.md). 

