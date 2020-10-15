---
title: Requêtes de recherche dans les journaux d’activité Azure Monitor | Microsoft Docs
description: Cet article fournit un didacticiel pour commencer à utiliser la recherche dans des requêtes de journal Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/06/2018
ms.openlocfilehash: e13f4abc37e348759e7d0b8a2f7d890c82fe0d15
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77660238"
---
# <a name="search-queries-in-azure-monitor-logs"></a>Requêtes de recherche dans les journaux d’activité Azure Monitor
Les requêtes de journal Azure Monitor peuvent commencer par un nom de table ou une commande de recherche. Ce tutoriel couvre les requêtes basées sur la recherche. Chaque méthode présente des avantages.

Les requêtes basées sur une table commencent par définir l’étendue de la requête, et ont donc tendance à être plus efficaces que les requêtes de recherche. Les requêtes de recherche étant moins structurées, elles constituent le meilleur choix pour rechercher une valeur spécifique dans des colonnes ou tables. La commande **search** peut rechercher la valeur spécifiée dans toutes les colonnes d’une table donnée ou de toutes les tables. La quantité de données traitées pouvant être énorme, ces requêtes peuvent prendre plus de temps et retourner des jeux de résultats très longs.

## <a name="search-a-term"></a>Rechercher un terme
La commande **search** est généralement utilisée pour rechercher un terme spécifique. Dans l’exemple suivant, le terme « error » est recherché dans toutes les colonnes de toutes les tables :

```Kusto
search "error"
| take 100
```

Bien qu’elles soient faciles à utiliser, les requêtes non délimitées comme celle montrée ci-dessus ne sont pas efficaces et sont susceptibles de retourner de nombreux résultats non pertinents. Une bonne pratique consisterait à effectuer une recherche dans la table appropriée, voire dans une colonne spécifique.

### <a name="table-scoping"></a>Étendue de table
Pour rechercher un terme dans une table spécifique, ajoutez `in (table-name)` juste après l’opérateur **search** :

```Kusto
search in (Event) "error"
| take 100
```

Ou dans plusieurs tables :
```Kusto
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>Étendue de table et de colonne
Par défaut, la commande **search** évalue toutes les colonnes du jeu de données. Pour rechercher uniquement une colonne spécifique (nommée *Source* dans l’exemple ci-dessous), utilisez la syntaxe suivante :

```Kusto
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> Si vous utilisez `==` au lieu de `:`, les résultats incluent les enregistrements dans lesquels la colonne *Source* a la valeur exacte « error » et, dans ce cas précis, l’utilisation de « : » inclut les enregistrements où *Source* a des valeurs telles que « error code 404 » ou « Error ».

## <a name="case-sensitivity"></a>Sensibilité à la casse
Par défaut, la recherche de termes n’est pas sensible à la casse, donc si vous recherchez « dns », vous obtenez des résultats comme « DNS », « dns » ou « Dns ». Pour que la recherche soit sensible à la casse, utilisez l’option `kind` :

```Kusto
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>Utilisation de caractères génériques
La commande **search** prend en charge les caractères génériques, au début, à la fin ou au milieu d’un terme.

Pour rechercher les termes qui commencent par « win » :
```Kusto
search in (Event) "win*"
| take 100
```

Pour rechercher les termes qui se terminent par « .com » :
```Kusto
search in (Event) "*.com"
| take 100
```

Pour rechercher les termes qui contiennent « www » :
```Kusto
search in (Event) "*www*"
| take 100
```

Pour rechercher les termes qui commencent par « corp » et se terminent par « .com », tels que « corp.mydomain.com » :

```Kusto
search in (Event) "corp*.com"
| take 100
```

Vous pouvez également récupérer tout le contenu d’une table à l’aide d’un simple caractère générique : `search in (Event) *`, mais cela équivaut à écrire simplement `Event`.

> [!TIP]
> Même si vous pouvez utiliser `search *` pour obtenir toutes les colonnes de chaque table, nous vous recommandons de toujours définir l’étendue de vos requêtes sur des tables spécifiques. Les requêtes non délimitées peuvent prendre un certain temps et retourner des résultats trop nombreux.

## <a name="add-and--or-to-search-queries"></a>Ajouter *and* / *or* aux requêtes de recherche
Utilisez **and** pour rechercher les enregistrements qui contiennent plusieurs termes :

```Kusto
search in (Event) "error" and "register"
| take 100
```

Utilisez **or** pour obtenir les enregistrements qui contiennent au moins un des termes :

```Kusto
search in (Event) "error" or "register"
| take 100
```

Si vous avez plusieurs conditions de recherche, vous pouvez les combiner dans la même requête à l’aide de parenthèses :

```Kusto
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

Le résultat de cet exemple comprend les enregistrements qui contiennent le terme « error », et également qui contiennent le terme « register » ou dont une des chaînes commence par « marshal ».

## <a name="pipe-search-queries"></a>Compléter les requêtes de recherche
Comme toute autre commande, la commande **search** peut être complétée de manière à filtrer, trier et agréger les résultats de la recherche. Par exemple, pour obtenir le nombre d’enregistrements de la table *Event* qui contiennent « win » :

```Kusto
search in (Event) "win"
| count
```




## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus de didacticiels sur le [site consacré au langage de requête Kusto](/azure/kusto/query/).
