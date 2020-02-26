---
title: Aide-mémoire sur KQL
description: Liste de fonctions KQL utiles, avec leurs définitions et des exemples de syntaxe.
author: yossi-karp
ms.author: v-yokarp
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: b53890afd10a3aee131ab3897d01e6b6fdf261a6
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429237"
---
# <a name="kql-quick-reference"></a>Aide-mémoire sur KQL

Cet article vous présente une liste de fonctions accompagnées de leur description pour vous aider à bien démarrer avec le langage de requête Kusto.

| Opérateur/Fonction                               | Description                           | Syntaxe                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Filtre/Recherche/Condition**                      |**_Rechercher des données pertinentes à l’aide du filtrage ou de la recherche_** |                      |
| [where](/azure/kusto/query/whereoperator.md)                      | Filtre à l’aide d’un prédicat spécifique           | `T | where Predicate`                         |
| [where contains/has](/azure/kusto/query/whereoperator.md)        | `Contains`: recherche une correspondance de sous-chaîne <br> `Has`: recherche un mot spécifique (meilleures performances)  | `T | where col1 contains/has "[search term]"`|
| [search](/azure/kusto/query/searchoperator.md)                    | Recherche la valeur dans toutes les colonnes de la table | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [take](/azure/kusto/query/takeoperator.md)                        | Retourne le nombre spécifié d’enregistrements. À utiliser pour tester une requête<br>**_Note_**  : `_take`_ et `_limit`_ sont synonymes. | `T | take NumberOfRows` |
| [case](/azure/kusto/query/casefunction.md)                        | Ajoute une instruction de condition, qui est semblable aux if/then/elseif des autres systèmes. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](/azure/kusto/query/distinctoperator.md)                | Produit une table avec la combinaison distincte des colonnes fournies de la table d’entrée. | `distinct [ColumnName], [ColumnName]` |
| **Date/Heure**                                   |**_Opérations qui utilisent les fonctions de date et d’heure_**               |                          |
|[ago](/azure/kusto/query/agofunction.md)                           | Retourne le décalage par rapport au moment où la requête a été exécutée. Par exemple, `ago(1h)` signifie une heure avant la lecture de l’horloge actuelle. | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction.md)  | Retourne des données dans [différents formats de date](/azure/kusto/query/format-datetimefunction.md#supported-formats). | `format_datetime(datetime , format)` |
| [bin](/azure/kusto/query/binfunction.md)                          | Arrondit toutes les valeurs d’une plage de temps et les regroupe | `bin(value,roundTo)` |
| **Créer/Supprimer des colonnes**                   |**_Ajouter ou supprimer des colonnes dans une table_** |                                                    |
| [print](/azure/kusto/query/printoperator.md)                      | Génère une seule ligne avec une ou plusieurs expressions scalaires | `print [ColumnName =] ScalarExpression [',' ...]` |
| [project](/azure/kusto/query/projectoperator.md)                  | Sélectionne les colonnes à inclure dans l’ordre spécifié | `T | project ColumnName [= Expression] [, ...]` <br> ou <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](/azure/kusto/query/projectawayoperator.md)         | Sélectionne les colonnes à exclure de la sortie | `T | project-away ColumnNameOrPattern [, ...]` |
| [extend](/azure/kusto/query/extendoperator.md)                    | Crée une colonne calculée et l’ajoute au jeu de résultats | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Trier et agréger un jeu de données**                 |**_Restructurer les données en les triant ou en les regroupant de façon logique_**|                  |
| [sort](/azure/kusto/query/sortoperator.md)                        | Trie les lignes de la table d’entrée selon une ou plusieurs colonnes, dans l’ordre croissant ou décroissant | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [top](/azure/kusto/query/topoperator.md)                          | Retourne les N premières lignes du jeu de données lorsque celui-ci est trié à l’aide de `by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [summarize](/azure/kusto/query/summarizeoperator.md)              | Regroupe les lignes en fonction du regroupement de colonnes `by` et calcule les agrégations sur chaque groupe | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](/azure/kusto/query/countoperator.md)                       | Compte les enregistrements dans la table d’entrée (par exemple, T)<br>Cet opérateur est le raccourci de `summarize count() `| `T | count` |
| [join](/azure/kusto/query/joinoperator.md)                        | Fusionne les lignes de deux tables pour former une nouvelle table en mettant en correspondance les valeurs des colonnes spécifiées de chaque table. Prend en charge la gamme complète des types de jointures : `flouter`, `inner`, `innerunique`, `leftanti`, `leftantisemi`, `leftouter`, `leftsemi`, `rightanti`, `rightantisemi`, `rightouter`, `rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [union](/azure/kusto/query/unionoperator.md)                      | Prend deux tables ou plus et retourne toutes leurs lignes | `[T1] | union [T2], [T3], …` |
| [range](/azure/kusto/query/rangeoperator.md)                      | Génère une table avec une suite arithmétique de valeurs | `range columnName from start to stop step step` |
| **Mettre en forme les données**                                 | **_Restructurer les données à afficher de manière utile_** | |
| [lookup](/azure/kusto/query/lookupoperator.md)                    | Étend les colonnes d’une table de faits avec les valeurs recherchées dans une table de dimension | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-expand](/azure/kusto/query/mvexpandoperator.md)               | Transforme des tableaux dynamiques en lignes (expansion multivaleur) | `T | mv-expand Column` |
| [parse](/azure/kusto/query/parseoperator.md)                      | évalue une expression de chaîne et analyse sa valeur dans une ou plusieurs colonnes calculées. À utiliser pour structurer des données non structurées. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-series](/azure/kusto/query/make-seriesoperator.md)          | Crée une série de valeurs agrégées spécifiées le long d’un axe spécifié | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [let](/azure/kusto/query/letstatement.md)                         | Lie un nom à des expressions qui peuvent référencer sa valeur liée. Les valeurs peuvent être des expressions lambda permettant de créer des fonctions ad hoc dans le cadre de la requête. Utilisez `let` pour créer des expressions sur des tables dont les résultats ressemblent à une nouvelle table. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Généralités**                                     | **_Opérations et fonctions diverses_** | |
| [invoke](/azure/kusto/query/invokeoperator.md)                    | Exécute la fonction sur la table qu’elle reçoit comme entrée. | `T | invoke function([param1, param2])` |
| [evaluate pluginName](/azure/kusto/query/evaluateoperator.md)     | Évalue les extensions de langage de requête (plug-ins) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Visualisation**                               | **_Opérations qui affichent les données dans un format graphique_** | |
| [render](/azure/kusto/query/renderoperator.md) | Affiche les résultats sous la forme d’une sortie graphique | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
