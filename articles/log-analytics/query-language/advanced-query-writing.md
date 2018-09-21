---
title: Requêtes avancées dans Azure Log Analytics | Microsoft Docs
description: Cet article fournit un tutoriel qui explique comment écrire des requêtes dans Log Analytics à l’aide du portail Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: f7594b7d1eb7d41508be435cdd0a6203433727c1
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603054"
---
# <a name="writing-advanced-queries-in-log-analytics"></a>Écriture de requêtes avancées dans Log Analytics

> [!NOTE]
> Vous devez suivre [Bien démarrer avec le portail Analytics](get-started-analytics-portal.md) et [Bien démarrer avec les requêtes](get-started-queries.md) avant d’effectuer cette leçon.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>Réutilisation du code avec let
Utilisez `let` pour affecter des résultats à une variable et y faire référence ultérieurement dans la requête :

```KQL
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

Vous pouvez également affecter des valeurs constantes aux variables. Cette opération prend en charge une méthode permettant de configurer des paramètres pour les champs que vous devez modifier chaque fois que vous exécutez la requête. Modifiez ces paramètres selon vos besoins. Par exemple, pour calculer l’espace disque libre et la mémoire disponible (en centiles), dans une fenêtre de temps donnée :

```KQL
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

```KQL
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

## <a name="functions"></a>Fonctions
Vous pouvez enregistrer une requête avec un alias de fonction pour que d’autres requêtes puissent la référencer. Par exemple, la requête standard suivante retourne toutes les mises à jour de sécurité manquantes signalées le dernier jour :

```KQL
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Vous pouvez enregistrer cette requête en tant que fonction et lui donner un alias tel que _security_updates_last_day_. Vous pouvez ensuite l’utiliser dans une autre requête pour rechercher les mises à jour de sécurité nécessaires relatives à SQL :

```KQL
security_updates_last_day | where Title contains "SQL"
```

Pour enregistrer une requête en tant que fonction, sélectionnez le bouton **Enregistrer** dans le portail et remplacez **Enregistrer sous** par _Fonction_. L’alias de fonction peut contenir des lettres, des chiffres ou des traits de soulignement, mais doit commencer par une lettre ou un trait de soulignement.

> [!NOTE]
> L’enregistrement d’une fonction est possible dans les requêtes Log Analytics, mais pas dans les requêtes Application Insights pour l’instant.


## <a name="print"></a>Print
`print` retourne une table avec une seule colonne et une seule ligne, qui affiche le résultat d’un calcul. Cette commande est souvent utilisée dans les cas où vous avez besoin d’un calcul simple. Par exemple, pour trouver l’heure actuelle en PST et ajouter une colonne avec EST :

```KQL
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Datatable
`datatable` vous permet de définir un ensemble de données. Vous fournissez un schéma et un ensemble de valeurs, puis dirigez la table dans tous les autres éléments de requête. Par exemple, pour créer une table de l’utilisation de mémoire RAM et calculer la valeur moyenne par heure :

```KQL
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

```KQL
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
Consultez d’autres leçons pour l’utilisation du langage de requête Log Analytics :

- [Opérations de chaîne](string-operations.md)
- [Opérations de date et d’heure](datetime-operations.md)
- [Fonctions d’agrégation](aggregations.md)
- [Agrégations avancées](advanced-aggregations.md)
- [JSON et structures de données](json-data-structures.md)
- [Jointures](joins.md)
- [Graphiques](charts.md)