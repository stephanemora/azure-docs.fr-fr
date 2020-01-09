---
title: Opérateurs utiles dans les requêtes de journal Azure Monitor | Microsoft Docs
description: Fonctions communes à utiliser pour différents scénarios dans des requêtes de journal Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: ff63b9b7027e99c70971230936ed98186c2208e8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75397721"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Opérateurs utiles dans les requêtes de journal Azure Monitor

Le tableau ci-dessous fournit des fonctions communes à utiliser pour différents scénarios dans des requêtes de journal Azure Monitor.

## <a name="useful-operators"></a>Opérateurs utiles

Category                                |Fonction Analytics pertinente
----------------------------------------|----------------------------------------
Alias de colonne et de sélection            |`project`, `project-away`, `extend`
Constantes et tables temporaires          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
Opérateurs de comparaison et de chaîne         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
Fonctions de chaîne courantes                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
Fonctions mathématiques courantes                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Analyse de texte                            |`extract()`, `extractjson()`, `parse`, `split()`
Limitation des résultats                         |`take`, `limit`, `top`, `sample`
Fonctions de date                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
Regroupement et agrégation                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
Jointures et unions                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
Tri, ordre                             |`sort`, `order` 
Objet dynamique (JSON et tableau)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Opérateurs logiques                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Apprentissage automatique                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>Étapes suivantes

- Suivez une leçon [d’écriture de requêtes de journal dans Azure Monitor](get-started-queries.md).
