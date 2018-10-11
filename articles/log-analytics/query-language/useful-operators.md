---
title: Opérateurs utiles dans des requêtes Azure Log Analytics | Microsoft Docs
description: Fonctions communes à utiliser pour différents scénarios dans des requêtes Log Analytics.
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
ms.date: 08/21/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 0b14c13462f15dd90285ed9e37080487324a4d85
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831277"
---
# <a name="useful-operators-in-log-analytics-queries"></a>Opérateurs utiles dans des requêtes Log Analytics

Le tableau ci-dessous fournit des fonctions communes à utiliser pour différents scénarios dans des requêtes Log Analytics.

## <a name="useful-operators"></a>Opérateurs utiles

Catégorie                                |Fonction Analytics pertinente
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

- Suivez une leçon sur [l’écriture de requêtes dans Log Analytics](get-started-queries.md).
