---
title: Requêtes avancées dans Azure Monitor | Microsoft Docs
description: Cet article fournit un tutoriel qui explique comment écrire des requêtes dans Azure Monitor à l’aide du portail Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 3d228c62cd2d1bcb7f4515cd698186e2ebcbe929
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77670285"
---
# <a name="writing-advanced-queries-in-azure-monitor"></a>Écriture de requêtes avancées dans Azure Monitor

> [!NOTE]
> Vous devez suivre [Bien démarrer avec Azure Monitor Log Analytics](get-started-portal.md) et [Bien démarrer avec les requêtes](get-started-queries.md) avant d’effectuer cette leçon.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>Réutilisation du code avec let
Utilisez `let` pour affecter des résultats à une variable et y faire référence ultérieurement dans la requête :

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

Vous pouvez également affecter des valeurs constantes aux variables. Cette opération prend en charge une méthode permettant de configurer des paramètres pour les champs que vous devez modifier chaque fois que vous exécutez la requête. Modifiez ces paramètres selon vos besoins. Par exemple, pour calculer l’espace disque libre et la mémoire disponible (en centiles), dans une fenêtre de temps donnée :

```Kusto
let startDate = datetime(2018-08-01T12:55:02);
let endDate = datetime(2018-08-02T13:21:35);
let FreeDiskSpace =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Logical Disk" and CounterName=="Free Megabytes"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
let FreeMemory =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Memory" and CounterName=="Available MBytes Memory"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
union FreeDiskSpace, FreeMemory
```

Il est ainsi facile de modifier l’heure de début ou de fin la prochaine fois que vous exécutez la requête.

### <a name="local-functions-and-parameters"></a>Paramètres et fonctions locales
Utilisez des instructions `let` pour créer des fonctions qui peuvent être utilisées dans la même requête. Par exemple, définissez une fonction qui prend un champ datetime (au format UTC) et le convertit en un format des États-Unis standard. 

```Kusto
let utc_to_us_date_format = (t:datetime)
{
  strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
  bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
};
Event 
| where TimeGenerated > ago(1h) 
| extend USTimeGenerated = utc_to_us_date_format(TimeGenerated)
| project TimeGenerated, USTimeGenerated, Source, Computer, EventLevel, EventData 
```

## <a name="print"></a>Print
`print` retourne une table avec une seule colonne et une seule ligne, qui affiche le résultat d’un calcul. Cette commande est souvent utilisée dans les cas où vous avez besoin d’un calcul simple. Par exemple, pour trouver l’heure actuelle en PST et ajouter une colonne avec EST :

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Datatable
`datatable` vous permet de définir un ensemble de données. Vous fournissez un schéma et un ensemble de valeurs, puis dirigez la table dans tous les autres éléments de requête. Par exemple, pour créer une table de l’utilisation de mémoire RAM et calculer la valeur moyenne par heure :

```Kusto
datatable (TimeGenerated: datetime, usage_percent: double)
[
  "2018-06-02T15:15:46.3418323Z", 15.5,
  "2018-06-02T15:45:43.1561235Z", 20.2,
  "2018-06-02T16:16:49.2354895Z", 17.3,
  "2018-06-02T16:46:44.9813459Z", 45.7,
  "2018-06-02T17:15:41.7895423Z", 10.9,
  "2018-06-02T17:44:23.9813459Z", 24.7,
  "2018-06-02T18:14:59.7283023Z", 22.3,
  "2018-06-02T18:45:12.1895483Z", 25.4
]
| summarize avg(usage_percent) by bin(TimeGenerated, 1h)
```

Les constructions Datatable sont également très utiles lors de la création d’une table de recherche. Par exemple, pour mapper des données de table comme des ID d’événement de la table _SecurityEvent_ aux types d’événements listés ailleurs, créez une table de recherche avec les types d’événements à l’aide de `datatable` et joignez ce datatable aux données _SecurityEvent_ :

```Kusto
let eventCodes = datatable (EventID: int, EventType:string)
[
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4672, "Access",
    4624, "Account activity",
    4799, "Access management",
    4798, "Access management",
    5059, "Key operation",
    4648, "A logon was attempted using explicit credentials",
    4768, "Access management",
    4662, "Other",
    8002, "Process action",
    4904, "Security event management",
    4905, "Security event management",
];
SecurityEvent
| where TimeGenerated > ago(1h) 
| join kind=leftouter (
  eventCodes
) on EventID
| project TimeGenerated, Account, AccountType, Computer, EventType
```

## <a name="next-steps"></a>Étapes suivantes
Reportez-vous à d’autres leçons sur l’utilisation du [langage de requête Kusto](/azure/kusto/query/) avec des données de journal Azure Monitor :

- [Opérations de chaîne](string-operations.md)
- [Opérations de date et d’heure](datetime-operations.md)
- [Fonctions d’agrégation](aggregations.md)
- [Agrégations avancées](advanced-aggregations.md)
- [JSON et structures de données](json-data-structures.md)
- [Joins](joins.md)
- [Graphiques](charts.md)
