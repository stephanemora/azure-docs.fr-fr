---
title: Syntaxe des requêtes de recherche Graph
titleSuffix: Azure Machine Learning
description: Procédure d’utilisation de la syntaxe de requête de recherche dans le concepteur Azure Machine Learning pour rechercher des nœuds dans le graphique de pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/24/2021
ms.openlocfilehash: 74cf0b897529e8bb198b6f82a57e187662a4a285
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259220"
---
# <a name="graph-search-query-syntax"></a>Syntaxe des requêtes de recherche Graph

Dans cet article, vous allez découvrir la fonctionnalité de recherche de graphiques dans Azure Machine Learning. 

La recherche de graphiques vous permet de parcourir rapidement un nœud lorsque vous déboguez ou créez un pipeline. Vous pouvez taper le mot clé ou la requête dans la zone d’entrée de la barre d’outils, ou sous l’onglet de recherche dans le panneau gauche pour déclencher la recherche. Tous les résultats correspondants sont mis en surbrillance en jaune dans le canevas, et si vous sélectionnez un résultat dans le panneau gauche, le nœud du canevas est mis en surbrillance en rouge.

![Capture d’écran montrant un exemple d’expérience de recherche de graphiques](media/search/graph-search-0322.png)

La recherche de graphique prend en charge la recherche par mot clé en texte intégral sur le nom de nœud et les commentaires. Vous pouvez également filtrer sur la propriété de nœud, par exemple avec runStatus, durée, computeTarget. La recherche par mot clé est basée sur la requête Lucene. Une requête de recherche complète ressemble à ceci :  

**[[lucene query] | [filter query]]** 

Vous pouvez utiliser une requête Lucene ou une requête de filtre. Pour utiliser les deux, servez-vous du séparateur **|** . La syntaxe de la requête de filtre est plus stricte que la requête Lucene. Par conséquent, si l’entrée du client peut être analysée avec les deux, la requête de filtre sera appliquée.

Par exemple, `data OR model | compute in {cpucluster}` sert à rechercher les nœuds dont le nom ou le commentaire contient `data` ou `model` et dont le calcul est cpucluster.
 

## <a name="lucene-query"></a>Requête Lucene

La recherche de graphique utilise la requête simple Lucene comme syntaxe de recherche en texte intégral sur le nœud « name » et « comment ». Les opérateurs Lucene suivants sont pris en charge :

 
- AND/OR
- Caractère générique correspondant à **?** et opérateurs **\*** .

### <a name="examples"></a>Exemples

- Recherche simple : `JSON Data`

- AND / OR : `JSON AND Validation`

- AND/OR multiple : `(JSON AND Validation) OR (TSV AND Training)`

 
- Caractère générique correspondant : 
    - `machi?e learning`
    - `mach*ing`
 
>[!NOTE]
> Vous ne pouvez pas démarrer une requête Lucene avec un caractère « * ».

##  <a name="filter-query"></a>Requête de filtre

 
Les requêtes de filtres utilisent le modèle suivant :
 
`**[key1] [operator1] [value1]; [key2] [operator1] [value2];**`

 
Vous pouvez utiliser les propriétés de nœud suivantes comme clés :

- runStatus
- compute
- duration
- reuse
- Publier
- tags

Et vous pouvez utiliser les opérateurs suivants :

- Supérieur ou égal à : `>=`
- Inférieur ou égal à : `<=`
- Supérieur à : `>`
- Inférieur à : `<`
- Égal à : `==`
- Contenir : `=`
- NotEqual : `!=`
- Dans : `in`

 
 

### <a name="example"></a>Exemple

- durée > 100 ;
- état dans {failed, NotStarted}
- calcul dans {GPU-cluster}; runStatus dans {Completed}

## <a name="technical-notes"></a>Notes techniques

- La relation entre plusieurs filtres est « AND »
- Si `>=,  >,  <, or  <=` est choisi, les valeurs sont automatiquement converties en type de nombre. Sinon, les types de chaînes sont utilisés pour la comparaison.
- Pour toutes les valeurs de type chaîne, la casse n’est pas sensible en comparaison.
- L’opérateur « In » attend une collection comme valeur, la syntaxe de la collection est `{name1, name2, name3}`
- L’espace sera ignoré entre les mots clés