---
title: Bien démarrer avec les requêtes de journal dans Azure Monitor | Microsoft Docs
description: Cet article fournit un tutoriel pour commencer à écrire des requêtes de journal dans Azure Monitor.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/20/2021
ms.openlocfilehash: b7ac389da66d68cd0fdddf15b46ddcee6ff537f6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248246"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Bien démarrer avec les requêtes de journal dans Azure Monitor

> [!NOTE]
> Vous pouvez effectuer cet exercice dans votre propre environnement si vous collectez des données à partir d’au moins une machine virtuelle. Pour les autres scénarios, utilisez notre [environnement de démonstration](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), qui comporte de nombreux exemples de données.  
>
> Si vous savez déjà comment interroger en langage de requête Kusto, mais que vous avez besoin de créer rapidement des requêtes utiles basées sur les types de ressources, consultez le volet des exemples de requêtes enregistrées dans l’article [Utiliser des requêtes dans Azure Monitor Log Analytics](../logs/queries.md).

Dans ce didacticiel, vous allez apprendre à écrire des requêtes de journal dans Azure Monitor. Cet article vous montre comment :

- Comprendre la structure d’une requête.
- Trier les résultats d’une requête.
- Filtrer les résultats d’une requête.
- Spécifier un intervalle de temps.
- Sélectionner les champs à inclure dans les résultats.
- Définir et utiliser des champs personnalisés.
- Agréger et regrouper des résultats.

Pour obtenir un didacticiel sur l’utilisation de Log Analytics dans le portail Azure, consultez [Bien démarrer avec Azure Monitor Log Analytics](./log-analytics-tutorial.md).

Pour plus d’informations sur les requêtes de journal dans Azure Monitor, consultez [Vue d’ensemble des requêtes de journal dans Azure Monitor](../logs/log-query-overview.md).

Voici une vidéo de tutoriel :

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="write-a-new-query"></a>Écrire une nouvelle requête

Les requêtes peuvent commencer par un nom de table ou la commande *search*. Il est judicieux de commencer par un nom de table, dans la mesure où il définit une étendue précise pour la requête et améliore les performances de la requête et la pertinence des résultats.

> [!NOTE]
> Le langage de requête Kusto, qui est utilisé par Azure Monitor, est sensible à la casse. Les mots clés du langage sont généralement écrits en minuscules. Quand vous utilisez des noms de tables ou de colonnes dans une requête, veillez à utiliser la casse adéquate, comme indiqué dans le volet Schéma.

### <a name="table-based-queries"></a>Requêtes basées sur une table

Azure Monitor organise les données de journal en tables, chacune composée de plusieurs colonnes. Toutes les tables et colonnes sont affichées dans le volet Schéma de Log Analytics sur le portail Analytics. Identifiez une table qui vous intéresse et jetez un œil à une partie des données :

```Kusto
SecurityEvent
| take 10
```

La requête précédente retourne 10 résultats à partir de la table *SecurityEvent*, sans ordre spécifique. Il s’agit d’une méthode courante pour avoir un aperçu d’une table et comprendre sa structure et son contenu. Examinons la façon dont elle est construite :

* La requête commence par le nom de table *SecurityEvent*, qui définit l’étendue de la requête.
* Le caractère barre verticale (|) sépare les commandes. Ainsi, la sortie de la première commande sert d’entrée à la suivante. Vous pouvez enchaîner un nombre quelconque d’éléments avec le caractère barre verticale.
* Le caractère barre verticale est suivi de la commande **take**, qui retourne un nombre spécifique d’enregistrements arbitraires à partir de la table.

Nous pourrions en fait exécuter la requête même sans ajouter `| take 10`. La commande serait toujours valide, mais pourrait retourner jusqu’à 10 000 résultats.

### <a name="search-queries"></a>Requêtes de recherche

Les requêtes de recherche sont moins structurées et généralement mieux adaptées pour rechercher des enregistrements qui incluent une valeur spécifique dans une de leurs colonnes :

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Cette requête recherche dans la table *SecurityEvent* les enregistrements qui contiennent l’expression « Cryptographic ». De ces enregistrements, dix sont retournés et affichés. Si vous omettez la partie `in (SecurityEvent)` et exécutez simplement `search "Cryptographic"`, la recherche parcourt *toutes* les tables, opération qui peut prendre plus de temps et être moins efficace.

> [!IMPORTANT]
> Les requêtes de recherche sont généralement plus lentes que les requêtes basées sur une table car elles doivent traiter plus de données. 

## <a name="sort-and-top"></a>Sort et top
Bien que **take** soit utile pour obtenir quelques enregistrements, les résultats ne sont pas sélectionnés et affichés dans un ordre particulier. Pour obtenir un affichage ordonné, vous pourriez **trier** (sort) en fonction de la colonne par défaut :

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

Cette opération pourrait cependant retourner trop de résultats et également prendre un certain temps. La requête trie *l’intégralité* de la table SecurityEvent en fonction de la colonne **TimeGenerated**. Ensuite, le portail Analytics limite l’affichage à 10 000 enregistrements uniquement. Bien sûr, cette approche n’est pas optimale.

La meilleure façon d’obtenir uniquement les 10 enregistrements les plus récents consiste à utiliser **top**, qui trie la table entière côté serveur, puis retourne les premiers enregistrements :

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

L’ordre décroissant étant l’ordre de tri par défaut, vous omettrez donc généralement l’argument **desc**. Une sortie classique ressemble à ceci :

![Capture d’écran des 10 premiers enregistrements, triés par ordre décroissant.](media/get-started-queries/top10.png)


## <a name="the-where-operator-filtering-on-a-condition"></a>Opérateur where : filtrage sur une condition
Les filtres, comme leur nom l’indique, filtrent les données en fonction d’une condition spécifique. Il s’agit de la méthode la plus courante pour limiter les résultats d’une requête aux informations pertinentes.

Pour ajouter un filtre à une requête, utilisez l’opérateur **where** suivi d’une ou plusieurs conditions. Par exemple, la requête suivante retourne uniquement des enregistrements *SecurityEvent* dans lesquels _Level_ est égal à _8_ :

```Kusto
SecurityEvent
| where Level == 8
```

Quand vous écrivez des conditions de filtre, vous pouvez utiliser les expressions suivantes :

| Expression | Description | Exemple |
|:---|:---|:---|
| == | Vérifier l’égalité<br>(avec respect de la casse) | `Level == 8` |
| =~ | Vérifier l’égalité<br>(sans respect de la casse) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Vérifier l’inégalité<br>(les deux expressions sont identiques) | `Level != 4` |
| *and*, *or* | Requis entre les conditions| `Level == 16 or CommandLine != ""` |

Pour filtrer selon plusieurs conditions, vous pouvez utiliser l’une des approches suivantes :

Utilisez **and**, comme illustré ici :

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

Enchaînez plusieurs éléments **where** avec le caractère barre verticale, comme illustré ici :

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Les valeurs pouvant avoir différents types, vous devrez peut-être les caster pour effectuer des comparaisons sur le type correct. Par exemple, la colonne *Level* de la table SecurityEvent est de type String ; vous devez donc la caster en un type numérique tel que *int* ou *long* avant de pouvoir lui appliquer des opérateurs numériques, comme illustré ici : `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Spécifier un intervalle de temps

### <a name="use-the-time-picker"></a>Utiliser le sélecteur d’heure

Le sélecteur d’heure se trouve près du bouton **Exécuter** et indique que notre recherche ne porte que sur les enregistrements générés au cours des dernières 24 heures uniquement. Il s’agit de l’intervalle de temps par défaut appliqué à toutes les requêtes. Pour obtenir uniquement les enregistrements générés au cours de la dernière heure, sélectionnez _Dernière heure_ et réexécutez la requête.

![Capture d’écran du sélecteur d’heure et de sa liste de commandes d’intervalle de temps.](media/get-started-queries/timepicker.png)


### <a name="add-a-time-filter-to-the-query"></a>Ajouter un filtre de temps à la requête

Vous pouvez également définir votre propre intervalle de temps en ajoutant un filtre de temps à la requête. Il est préférable de placer le filtre de temps immédiatement après le nom de la table : 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

Dans le filtre temporel précédent, `ago(30m)` signifie « il y a 30 minutes », ce qui signifie que cette requête renvoie uniquement les enregistrements des 30 dernières minutes (par exemple sous la forme 30m). Les autres unités de temps incluent les jours (par exemple, 2d) et les secondes (par exemple, 10s).


## <a name="use-project-and-extend-to-select-and-compute-columns"></a>Utilisez Project et Extend pour sélectionner et calculer des colonnes

Utilisez **project** pour sélectionner les colonnes à inclure dans les résultats :

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

L’exemple ci-dessus génère le résultat suivant :

![Capture d’écran de la liste des résultats de la requête « project ».](media/get-started-queries/project.png)

Vous pouvez également utiliser **project** pour renommer des colonnes et en définir de nouvelles. L’exemple suivant utilise **project** pour effectuer les opérations suivantes :

* Sélectionner uniquement les colonnes d’origine *Computer* et *TimeGenerated*.
* Afficher la colonne *Activity* en tant que *EventDetails*.
* Créer une colonne nommée *EventCode*. La fonction **substring()** est utilisée pour obtenir uniquement les quatre premiers caractères du champ Activity.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

Vous pouvez utiliser **extend** pour conserver toutes les colonnes d’origine dans le jeu de résultats et en définit de nouvelles. La requête suivante utilise **extend** pour ajouter la colonne *EventCode*. Cette colonne peut ne pas s’afficher à la fin des résultats de la table, auquel cas vous devrez développer les détails d’un enregistrement pour l’afficher.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="use-summarize-to-aggregate-groups-of-rows"></a>Utiliser summarize pour agréger des groupes de lignes
Utilisez **summarize** pour identifier des groupes d’enregistrements, en fonction d’une ou plusieurs colonnes, et leur appliquer des agrégations. L’utilisation la plus courante de **summarize** est *count*, qui retourne le nombre de résultats contenus dans chaque groupe.

La requête suivante passe en revue tous les enregistrements *Perf* générés au cours de la dernière heure, les regroupe par *ObjectName* et compte les enregistrements dans chaque groupe : 

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Parfois, il est judicieux de définir les groupes au moyen de plusieurs dimensions. Chaque combinaison unique de ces valeurs définit un groupe distinct :

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Une autre utilisation courante consiste à effectuer des calculs mathématiques ou statistiques sur chaque groupe. L’exemple qui suit calcule la moyenne de *CounterValue* pour chaque ordinateur :

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Malheureusement, les résultats de cette requête n’ont aucune signification dans la mesure où nous avons combiné différents compteurs de performances. Pour que les résultats de cette requête soient plus explicites, calculez la moyenne séparément pour chaque combinaison de *CounterName* et *Computer* :

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Résumer en fonction d’une colonne d’heure
Le regroupement des résultats peut également reposer sur une colonne de temps ou une autre valeur continue. Cependant, une simple agrégation `by TimeGenerated` créerait des groupes pour chaque milliseconde de la plage de temps, car ce sont des valeurs uniques. 

Pour créer des groupes basés sur des valeurs continues, il convient de diviser la plage en unités gérables à l’aide de **bin**. La requête suivante analyse les enregistrements *Perf* qui mesurent la mémoire disponible (*Available MBytes*) sur un ordinateur spécifique. Elle calcule la valeur moyenne pour chaque période de 1 heure, au cours des 7 derniers jours :

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Pour rendre la sortie plus claire, vous décidez de l’afficher sous la forme d’un graphique de temps, qui indique la mémoire disponible au fil du temps :

![Capture d’écran affichant les valeurs d’une mémoire de requête dans le temps.](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Étapes suivantes

- Pour en apprendre davantage sur l’utilisation des données de chaîne dans une requête de journal, consultez [Utilisation de chaînes dans des requêtes de journal Azure Monitor](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations).
- Pour en apprendre davantage sur l’agrégation de données dans une requête de journal, consultez [Agrégations avancées dans des requêtes de journal Azure Monitor](/azure/data-explorer/write-queries#advanced-aggregations).
- Pour découvrir comment joindre des données provenant de plusieurs tables, consultez [Jointures dans les requêtes de journal Azure Monitor](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins).
- Procurez-vous la documentation sur l’intégralité du langage de requête Kusto dans [Informations de référence sur le langage KQL](/azure/kusto/query/).
