---
title: Splunk vers Azure Log Analytics | Microsoft Docs
description: Aidez les utilisateurs qui connaissent Splunk à apprendre le langage de requête Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: 61f0cff661c79f994a5b3c20646996f617a31b7e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52885114"
---
# <a name="splunk-to-log-analytics"></a>Splunk vers Log Analytics

Cet article vise à aider les utilisateurs qui connaissent Splunk à apprendre le langage de requête Log Analytics. Des comparaisons directes sont établies entre les deux pour vous permettre de comprendre les principales différences et les similitudes, et éventuellement d’approfondir vos connaissances.

## <a name="structure-and-concepts"></a>Structure et concepts

Le tableau suivant compare les concepts et structures de données entre Splunk et Log Analytics.

 | Concept  | Splunk | Log Analytics |  Commentaire
 | --- | --- | --- | ---
 | Unité de déploiement  | cluster |  cluster |  Log Analytics autorise les requêtes entre clusters arbitraires. Ce n’est pas le cas de Splunk. |
 | Caches de données |  compartiments  |  Stratégies de rétention et mise en cache |  Contrôle la période et le niveau de mise en cache des données. Ce paramètre a un impact direct sur les performances des requêtes et le coût du déploiement. |
 | Partition logique des données  |  index  |  database  |  Permet une séparation logique des données. Les deux implémentations autorisent les unions et les jointures entre ces partitions. |
 | Métadonnées d’événement structurées | N/A | table |  Splunk n’a pas le concept exposé au langage de recherche de métadonnées d’événement. Log Analytics a le concept d’une table, qui comporte des colonnes. Chaque instance d’événement est mappée à une ligne. |
 | Enregistrement de données | événement | ligne |  Changement de terminologie uniquement. |
 | Attribut d’enregistrement de données | field |  colonne |  Dans Log Analytics, cette colonne est prédéfinie dans le cadre de la structure de table. Dans Splunk, chaque événement possède son propre ensemble de champs. |
 | Types | type de données |  type de données |  Les types de données Log Analytics sont plus explicites, car ils sont définis sur les colonnes. Les deux ont la possibilité d’utiliser dynamiquement des types de données et un ensemble de types de données à peu près équivalent, notamment la prise en charge JSON. |
 | Requête et recherche  | recherche | query |  Les concepts sont essentiellement les mêmes entre Log Analytics et Splunk. |
 | Durée d’ingestion des événements | Temps système | ingestion_time() |  Dans Splunk, chaque événement obtient un horodatage système de l’heure à laquelle l’événement a été indexé. Dans Log Analytics, vous pouvez définir une stratégie appelée ingestion_time qui expose une colonne système qui peut être référencée via la fonction ingestion_time(). |

## <a name="functions"></a>Fonctions

Le tableau suivant spécifie les fonctions dans Log Analytics qui sont équivalentes aux fonctions Splunk.

|Splunk | Log Analytics |Commentaire
|---|---|---
|strcat | strcat()| (1) |
|split  | split() | (1) |
|if     | iff()   | (1) |
|tonumber | todouble()<br>tolong()<br>toint() | (1) |
|upper<br>lower |toupper()<br>tolower()|(1) |
| remplacer | replace() | (1)<br> Notez également que si `replace()` prend trois paramètres dans les deux produits, les paramètres sont différents. |
| substr | substring() | (1)<br>Notez également que Splunk utilise des index de base un. Log Analytics note des index de base zéro. |
| tolower |  tolower() | (1) |
| toupper | toupper() | (1) |
| match | matches regex |   (2)  |
| regex | matches regex | Dans Splunk, `regex` est un opérateur. Dans Log Analytics, il s’agit d’un opérateur relationnel. |
| searchmatch | == | Dans Splunk, `searchmatch` permet de rechercher la chaîne exacte.
| random | rand()<br>rand(n) | La fonction de Splunk retourne un nombre compris entre zéro et 2<sup>31</sup>-1. La fonction de Log Analytics retourne un nombre entre 0,0 et 1,0 ou, si un paramètre est fourni, entre 0 et n-1.
| now | now() | (1)
| relative_time | totimespan() | (1)<br>Dans Log Analytics, l’équivalent Splunk de relative_time(datetimeVal, offsetVal) est datetimeVal + totimespan(offsetVal).<br>Par exemple, <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> devient <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) Dans Splunk, la fonction est appelée avec l’opérateur `eval`. Dans Log Analytics, elle est utilisée dans le cadre d’`extend` ou de `project`.<br>(2) Dans Splunk, la fonction est appelée avec l’opérateur `eval`. Dans Log Analytics, elle peut être utilisée avec l’opérateur `where`.


## <a name="operators"></a>Operators

Les sections suivantes donnent des exemples d’utilisation de différents opérateurs entre Splunk et Log Analytics.

> [!NOTE]
> Pour les besoins de l’exemple suivant, le champ Splunk _rule_ est mappé à une table dans Azure Log Analytics et l’horodatage par défaut de Splunk est mappé à la colonne _ingestion_time()_ de Logs Analytics.

### <a name="search"></a>Recherche
Dans Splunk, vous pouvez omettre le mot clé `search` et spécifier une chaîne sans guillemets. Dans Azure Log Analytics, vous devez démarrer chaque recherche par `find`, une chaîne sans guillemets est un nom de colonne et la valeur de recherche doit être une chaîne entre guillemets. 

| |  | |
|:---|:---|:---|
| Splunk | **search** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Log Analytics | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Filtrer
Les requêtes Azure Log Analytics démarrent à partir d’un jeu de résultats sous forme de tableau, où se trouve le filtre. Dans Splunk, le filtrage est l’opération par défaut sur l’index actuel. Vous pouvez également utiliser l’opérateur `where` dans Splunk, mais ce n’est pas recommandé.

| |  | |
|:---|:---|:---|
| Splunk | **search** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Log Analytics | **where** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>Obtention de n événements/lignes pour inspection 
Azure Log Analytics prend également en charge `take` en tant qu’alias pour `limit`. Dans Splunk, si les résultats sont triés, `head` retourne les n premiers résultats. Dans Azure Log Analytics, la limite n’est pas ordonnée, mais retourne les n premières lignes trouvées.

| |  | |
|:---|:---|:---|
| Splunk | **head** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Log Analytics | **limit** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Obtention des n premiers événements/premières lignes classé(e)s selon un champ/une colonne
Pour les derniers résultats, dans Splunk vous utilisez `tail`. Dans Azure Log Analytics, vous pouvez spécifier la direction de tri avec `asc`.

| |  | |
|:---|:---|:---|
| Splunk | **head** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Log Analytics | **top** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Extension du jeu de résultats avec de nouveaux champs ou de nouvelles colonnes
Splunk a également une fonction `eval`, qui ne doit pas être comparée à l’opérateur `eval`. L’opérateur `eval` dans Splunk et l’opérateur `extend` dans Azure Log Analytics prennent tous deux uniquement en charge les fonctions scalaires et les opérateurs arithmétiques.

| |  | |
|:---|:---|:---|
| Splunk | **eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Log Analytics | **extend** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Renommer 
Azure Log Analytics utilise le même opérateur pour renommer et pour créer un champ. Splunk a deux opérateurs distincts, `eval` et `rename`.

| |  | |
|:---|:---|:---|
| Splunk | **rename** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Log Analytics | **extend** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Mettre en forme les résultats/Projection
Splunk ne semble pas avoir d’opérateur similaire à `project-away`. Vous pouvez utiliser l’interface utilisateur pour filtrer les champs.

| |  | |
|:---|:---|:---|
| Splunk | **table** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Log Analytics | **project**<br>**project-away** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Agrégation
Consultez [Agrégations dans des requêtes Log Analytics](aggregations.md) pour connaître les différentes fonctions d’agrégation.

| |  | |
|:---|:---|:---|
| Splunk | **stats** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Log Analytics | **summarize** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Join
La jointure dans Splunk présente des limitations importantes. La sous-requête a une limite de 10 000 résultats (définie dans le fichier de configuration de déploiement), et il existe un nombre limité de variantes de jointure.

| |  | |
|:---|:---|:---|
| Splunk | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias | join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Log Analytics | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Trier
Dans Splunk, pour trier dans l’ordre croissant, vous devez utiliser l’opérateur `reverse`. Azure Log Analytics prend également en charge la définition de l’emplacement des valeurs null, au début ou à la fin.

| |  | |
|:---|:---|:---|
| Splunk | **sort** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Log Analytics | **order by** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Développement à valeurs multiples
Il s’agit d’un opérateur similaire dans Splunk et Log Analytics.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Log Analytics | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Facettes de résultats, champs intéressants
Dans le portail Log Analytics, seule la première colonne est exposée. Toutes les colonnes sont disponibles via l’API.

| |  | |
|:---|:---|:---|
| Splunk | **fields** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Log Analytics | **facets** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>Dédupliquer
Vous pouvez utiliser `summarize arg_min()` à la place pour inverser l’ordre dans lequel l’enregistrement est choisi.

| |  | |
|:---|:---|:---|
| Splunk | **dedup** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Log Analytics | **summarize arg_max()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>Étapes suivantes

- Suivez une leçon sur [l’écriture de requêtes dans Log Analytics](get-started-queries.md).