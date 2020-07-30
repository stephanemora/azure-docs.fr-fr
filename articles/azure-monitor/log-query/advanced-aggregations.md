---
title: Agrégations dans les requêtes de journal Azure Monitor | Microsoft Docs
description: Décrit certaines des options d’agrégation les plus avancées disponibles pour les requêtes de journal Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: dba058dce09e958a2ae769d927a5569fb3e42113
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324554"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Agrégations dans les requêtes de journal Azure Monitor | Microsoft Docs

> [!NOTE]
> Avant de suivre cette leçon, vous devriez suivre la leçon [Agrégations dans des requêtes Azure Monitor](./aggregations.md).

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Cet article décrit certaines des options d’agrégation les plus avancées disponibles pour les requêtes Azure Monitor.

## <a name="generating-lists-and-sets"></a>Génération de listes et d’ensembles
Vous pouvez utiliser `makelist` pour déplacer des données selon l’ordre des valeurs dans une colonne particulière. Par exemple, vous voulez explorer l’ordre le plus courant dans lequel les événements se produisent sur vos ordinateurs. Vous pouvez avant tout déplacer les données selon l’ordre des EventID sur chaque ordinateur. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` génère une liste dans l’ordre dans lequel les données lui ont été passées. Pour trier les événements du plus ancien au plus récent, utilisez `asc` dans l’instruction d’ordre au lieu de `desc`. 

Il est également utile de créer une liste de valeurs distinctes seulement. Il s’agit d’un _ensemble_ qui peut être généré avec `makeset` :

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

Comme `makelist`, `makeset` fonctionne également avec des données ordonnées et génère les tableaux selon l’ordre des lignes qui lui sont passées.

## <a name="expanding-lists"></a>Extension de listes
L’opération inverse de `makelist` ou `makeset` est `mvexpand`, qui étend une liste de valeurs sur des lignes séparées. L’extension peut porter sur n’importe quel nombre de colonnes dynamiques, JSON et de tableau. Par exemple, vous pouvez rechercher dans la table *Heartbeat* des solutions envoyant des données à partir d’ordinateurs qui ont envoyé une pulsation dans la dernière heure :

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Computer | Solutions | 
|--------------|----------------------|
| computer1 | "security", "updates", "changeTracking" |
| computer2 | "security", "updates" |
| computer3 | "antiMalware", "changeTracking" |
| ... | ... |

Utilisez `mvexpand` pour afficher chaque valeur dans une ligne distincte au lieu d’une liste séparée par des virgules :

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Computer | Solutions | 
|--------------|----------------------|
| computer1 | "security" |
| computer1 | "updates" |
| computer1 | "changeTracking" |
| computer2 | "security" |
| computer2 | "updates" |
| computer3 | "antiMalware" |
| computer3 | "changeTracking" |
| ... | ... |


Vous pouvez ensuite utiliser à nouveau `makelist` pour regrouper les éléments et afficher cette fois la liste des ordinateurs par solution :

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Solutions | list_Computer |
|--------------|----------------------|
| "security" | ["computer1", "computer2"] |
| "updates" | ["computer1", "computer2"] |
| "changeTracking" | ["computer1", "computer3"] |
| "antiMalware" | ["computer3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>Gestion des compartiments manquants
Une application utile de `mvexpand` est la nécessité de renseigner les valeurs par défaut pour les compartiments manquants. Par exemple, supposons que vous recherchez la durée de fonctionnement d’un ordinateur particulier en explorant sa pulsation. Vous voulez aussi voir la source de la pulsation qui se trouve dans la colonne _Catégorie_. En règle générale, nous utiliserions une simple instruction de synthèse comme suit :

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| Category | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Agent direct | 2017-06-06T17:00:00Z | 15 |
| Agent direct | 2017-06-06T18:00:00Z | 60 |
| Agent direct | 2017-06-06T20:00:00Z | 55 |
| Agent direct | 2017-06-06T21:00:00Z | 57 |
| Agent direct | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

Dans ces résultats, cependant, le compartiment associé à "2017-06-06T19:00:00Z" est manquant, car il n’existe pas de données de pulsation pour cette heure. Utilisez la fonction `make-series` pour affecter une valeur par défaut aux compartiments vides. Cette opération génère une ligne pour chaque catégorie avec deux colonnes de tableau supplémentaires, une pour les valeurs et une pour les intervalles de temps correspondants :

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Category | count_ | TimeGenerated |
|---|---|---|
| Agent direct | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

Le troisième élément du tableau *count_* est égal à 0 comme prévu, et il existe un horodatage correspondant de "2017-06-06T19:00:00.0000000Z" dans le tableau _TimeGenerated_. Ce format de tableau est cependant difficile à lire. Utilisez `mvexpand` pour étendre les tableaux et produire le même format de sortie que celui généré par `summarize` :

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| Category | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Agent direct | 2017-06-06T17:00:00Z | 15 |
| Agent direct | 2017-06-06T18:00:00Z | 60 |
| Agent direct | 2017-06-06T19:00:00Z | 0 |
| Agent direct | 2017-06-06T20:00:00Z | 55 |
| Agent direct | 2017-06-06T21:00:00Z | 57 |
| Agent direct | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Restriction des résultats à un ensemble d’éléments : `let`, `makeset`, `toscalar`, `in`
Un scénario courant consiste à sélectionner les noms de certaines entités spécifiques selon un ensemble de critères, puis à filtrer un autre jeu de données selon cet ensemble d’entités. Par exemple, vous pouvez rechercher les ordinateurs qui sont connus comme ayant des mises à jour manquantes et identifier les adresses IP auxquelles ces ordinateurs font appel :


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="next-steps"></a>Étapes suivantes

Reportez-vous à d’autres leçons sur l’utilisation du [langage de requête Kusto](/azure/kusto/query/) avec des données de journal Azure Monitor :

- [Opérations de chaîne](string-operations.md)
- [Opérations de date et d’heure](datetime-operations.md)
- [Fonctions d’agrégation](aggregations.md)
- [Agrégations avancées]()
- [JSON et structures de données](json-data-structures.md)
- [Joins](joins.md)
- [Graphiques](charts.md)

