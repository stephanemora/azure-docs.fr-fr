---
title: Syntaxe des expressions OData pour les filtres et les clauses order by - Recherche Azure
description: Syntaxe des expressions OData pour les filtres et les clauses order by dans les requêtes Recherche Azure.
ms.date: 03/27/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: ab98c3be75fb59603be66ee84e0d288de56cdc91
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648501"
---
# <a name="odata-expression-syntax-for-filters-and-order-by-clauses-in-azure-search"></a>Syntaxe des expressions OData pour les filtres et les clauses order by dans Recherche Azure

Recherche Azure prend en charge un sous-ensemble de la syntaxe des expressions OData pour les expressions **$filter** et **$orderby**. Les expressions de filtre sont évaluées lors de l’analyse de la requête, limitant la recherche à des champs spécifiques ou ajoutant des critères de correspondance utilisés lors du parcours des index. Les expressions order by sont appliquées dans une étape de post-traitement sur un jeu de résultats. Les filtres et les expressions order by sont inclus dans une demande de requête, en respectant une syntaxe OData indépendante de la syntaxe de requête [simple](query-simple-syntax.md) ou [complète](query-lucene-syntax.md) utilisée dans un paramètre de **recherche**. Cet article fournit la documentation de référence pour les expressions OData utilisée dans les filtres et les expressions de tri.

## <a name="filter-syntax"></a>Syntaxe des filtres

Une expression **$filter** peut s’exécute de façon autonome comme requête entièrement exprimée, ou affiner une requête qui a des paramètres supplémentaires. Les exemples suivants illustrent quelques scénarios importants. Dans le premier exemple, le filtre est l’essence de la requête.


```POST
POST /indexes/hotels/docs/search?api-version=2017-11-11
    {
      "filter": "(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'"
    }
```

Un autre cas d’utilisation courant est une facette combinée avec des filtres, où le filtre réduit la surface de la requête en fonction de la sélection dans la navigation de la facette pilotée par l’utilisateur :

```POST
POST /indexes/hotels/docs/search?api-version=2017-11-11
    {
      "search": "test",
      "facets": [ "tags", "baseRate,values:80|150|220" ],
      "filter": "rating eq 3 and category eq 'Motel'"
    }
```

### <a name="filter-operators"></a>Opérateurs des filtres  

- Opérateurs logiques (and, or, not).  

- Expressions de comparaison (`eq, ne, gt, lt, ge, le`). Les comparaisons de chaînes sont sensibles à la casse.  

- Constantes des types [Entity Data Model](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM) pris en charge (consultez [Types de données pris en charge &#40;Recherche Azure&#41; ](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) pour obtenir la liste des types pris en charge). Les constantes des types collection ne sont pas prises en charge.  

- Références à des noms de champ. Seuls les champs `filterable` peuvent être utilisés dans les expressions de filtre.  

- `any` sans aucun paramètre. Ceci teste si un champ de type `Collection(Edm.String)` contient des éléments.  

- `any` et `all` avec prise en charge limitée des expressions lambda. 
    
  -   `any/all` sont pris en charge sur les champs de type `Collection(Edm.String)`. 
    
  -   `any` peut être utilisé seulement avec des expressions d’égalité simples ou avec une fonction `search.in`. Les expressions simples sont constituées d’une comparaison entre un seul champ et une valeur littérale, par exemple `Title eq 'Magna Carta'`.
    
  -   `all` peut être utilisé seulement avec des expressions d’inégalité simples ou avec une fonction `not search.in`.   

- Fonctions géospatiales `geo.distance` et `geo.intersects`. La fonction `geo.distance` retourne la distance en kilomètres entre deux points, l’un étant un champ et l’autre une constante passée comme partie du filtre. La fonction `geo.intersects` retourne « true » si un point donné se trouve dans un polygone donné, où le point est un champ et où le polygone est spécifié sous la forme d’une constante passée comme partie du filtre.  

  Le polygone est une surface en deux dimensions stockée sous la forme d’une séquence de points définissant un cadre englobant (voir l’exemple ci-dessous). Le polygone doit être fermé, ce qui signifie que le premier point et le dernier doivent être le même. [Les points d’un polygone doivent être dans le sens antihoraire](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

  `geo.distance` retourne la distance en kilomètres dans Recherche Azure. Ceci diffère des autres services qui prennent en charge des opérations géospatiales OData, qui retournent généralement les distances en mètres.  

  > [!NOTE]  
  >  Quand vous utilisez geo.distance dans un filtre, vous devez comparer la distance retournée par la fonction avec une constante en utilisant `lt`, `le`, `gt` ou `ge`. Les opérateurs `eq` et `ne` ne sont pas pris en charge lors de la comparaison de distances. Voici par exemple une utilisation correcte de geo.distance : `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.  

- La fonction `search.in` teste si un champ de type chaîne donné est égal à une des valeurs d’une liste donnée. Elle peut également être utilisée dans any ou dans all pour comparer une seule valeur d’un champ de collection de chaînes avec une liste de valeurs donnée. L’égalité entre le champ et chaque valeur de la liste est déterminée de manière en respectant la casse, de la même façon que pour l’opérateur `eq`. Ainsi, une expression comme `search.in(myfield, 'a, b, c')` équivaut à `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, sauf que `search.in` aboutira à de bien meilleures performances. 

   Le premier paramètre de la fonction `search.in` est la référence du champ de type chaîne (ou une variable de portée sur un champ de collection de chaînes dans le cas où `search.in` est utilisé à l’intérieur d’une expression `any` ou `all`). 
  
   Le deuxième paramètre est une chaîne contenant la liste de valeurs, séparées par des espaces et/ou des virgules. 
  
   Le troisième paramètre est une chaîne où chaque caractère de la chaîne ou le sous-ensemble de cette chaîne est traitée comme un séparateur lors de l’analyse de la liste des valeurs dans le second paramètre. Si vous devez utiliser des séparateurs autres que des espaces et des virgules dans le cas où vos valeurs incluent ces caractères, vous pouvez spécifier un troisième paramètre facultatif pour `search.in`. 

  > [!NOTE]   
  > Certains scénarios nécessitent la comparaison d’un champ à un grand nombre de valeurs de constante. Par exemple, l’implémentation de restrictions de sécurité avec des filtres peut nécessiter de comparer le champ d’ID de document à une liste d’ID pour lesquels l’utilisateur demandeur a un accès en lecture. Dans ce type de scénarios, nous recommandons l’utilisation de la fonction `search.in` au lieu d’une disjonction plus complexe d’expressions d’égalité. Par exemple, utilisez `search.in(Id, '123, 456, ...')` au lieu de `Id eq 123 or Id eq 456 or ....`. 
  >
  > Si vous utilisez `search.in`, vous pouvez vous attendre à des temps de réponse inférieurs à la seconde quand le deuxième paramètre contient une liste de plusieurs centaines ou plusieurs milliers de valeurs. Notez qu’il n’existe pas de limite explicite quant au nombre d’éléments que vous pouvez passer à `search.in`, même si vous êtes néanmoins limité par la taille maximale de la demande. Cependant, la latence augmente en même temps que le nombre de valeurs.

- La fonction `search.ismatch` évalue la requête de recherche comme une partie d’une expression de filtre. Les documents qui correspondent à la requête de recherche sont retournés dans le jeu de résultats. Les surcharges suivantes de cette fonction sont disponibles :
  - `search.ismatch(search)`
  - `search.ismatch(search, searchFields)`
  - `search.ismatch(search, searchFields, queryType, searchMode)`

  où : 
  
  - `search` : la requête de recherche (dans la syntaxe de requête [simple](query-simple-syntax.md) ou [complète](query-lucene-syntax.md)). 
  - `queryType` : « simple » ou « full » (complète) ; la valeur par défaut est « simple ». Spécifie le langage de requête utilisé dans le paramètre `search`.
  - `searchFields` : liste séparée par des virgules de champs de recherche où effectuer des recherches ; par défaut, tous les champs avec possibilité de recherche dans l’index.    
  - `searchMode` : « any » ou « all » ; la valeur par défaut est « any ». Indique si tout ou partie des termes de recherche doivent correspondre pour que le document soit considéré comme une correspondance.

  Tous les paramètres ci-dessus sont équivalents aux [paramètres de la requête de recherche](https://docs.microsoft.com/rest/api/searchservice/search-documents) correspondante.

- La fonction `search.ismatchscoring`, comme la fonction `search.ismatch`, retourne true pour les documents qui correspondent à la requête de recherche passée comme paramètre. La différence est que le score de pertinence des documents correspondant à la requête `search.ismatchscoring` contribue au score global du document, tandis que dans le cas de `search.ismatch`, le score du document n’est pas modifié. Les surcharges suivantes de cette fonction sont disponibles avec des paramètres identiques à ceux de `search.ismatch` :
  - `search.ismatchscoring(search)`
  - `search.ismatchscoring(search, searchFields)`
  - `search.ismatchscoring(search, searchFields, queryType, searchMode)`

  Les fonctions `search.ismatch` et `search.ismatchscoring` sont entièrement orthogonales l’une avec l’autre et avec le reste de l’algèbre du filtre. Cela signifie que les deux fonctions peuvent être utilisées dans la même expression de filtre. 

### <a name="geospatial-queries-and-polygons-spanning-the-180th-meridian"></a>Requêtes géospatiales et polygones couvrant le 180e méridien  
 Pour de nombreuses bibliothèques de requêtes géospatiales, la formulation d’une requête incluant le 180e méridien (près de la ligne de changement de date) est hors limites ou nécessite une solution de contournement, comme diviser le polygone en deux parties, une de chaque côté du méridien.  

 Dans Recherche Azure, les requêtes géospatiales incluant une longitude de 180 degrés fonctionne normalement si la forme de la requête est rectangulaire et que vos coordonnées s’alignent sur une disposition en grille le long de la longitude et de la latitude (par exemple `geo.intersects(location, geography'POLYGON((179 65,179 66,-179 66,-179 65,179 65))'`). Sinon, pour les formes non rectangulaires ou non alignées, envisagez l’approche par division du polygone.  

<a name="bkmk_limits"></a>

## <a name="filter-size-limitations"></a>Limitations de taille des filtres 

 Il existe des limites de taille et de complexité des expressions de filtre que vous pouvez envoyer à Recherche Azure. Les limites sont en gros basées sur le nombre de clauses de votre expression de filtre. Une règle empirique est que, si vous avez des centaines de clauses, vous risquez d’atteindre la limite. Nous vous recommandons de concevoir votre application de telle sorte qu’elle ne génère pas de filtres de taille illimitée.  


## <a name="filter-examples"></a>Exemples de filtres  

 Rechercher tous les hôtels avec un taux de base inférieure à 200 $ qui sont évalués au moins 4 :  

```
$filter=baseRate lt 200.0 and rating ge 4
```

 Rechercher tous les hôtels autres que « Roach Motel » qui ont été rénovés depuis 2010 :  

```
$filter=hotelName ne 'Roach Motel' and lastRenovationDate ge 2010-01-01T00:00:00Z
```

 Rechercher tous les hôtels avec un taux de base inférieur à 200 $ qui ont été rénovés depuis 2010, avec un littéral datetime incluant des informations de fuseau horaire pour l’heure du Pacifique :  

```
$filter=baseRate lt 200 and lastRenovationDate ge 2010-01-01T00:00:00-08:00
```

 Rechercher tous les hôtels avec un parking inclus et où il est interdit de fumer :  

```
$filter=parkingIncluded and not smokingAllowed
```

 \- OR -  

```
$filter=parkingIncluded eq true and smokingAllowed eq false
```

 Rechercher tous les hôtels de catégorie « Luxury » ou incluant un parking et ayant une évaluation de 5 :  

```
$filter=(category eq 'Luxury' or parkingIncluded eq true) and rating eq 5
```

 Rechercher tous les hôtels avec l’étiquette « wifi » (où chaque hôtel a des étiquettes stockées dans un champ Collection(Edm.String)) :  

```
$filter=tags/any(t: t eq 'wifi')
```

 Rechercher tous les hôtels sans l’étiquette « motel » :  

```
$filter=tags/all(t: t ne 'motel')
```

 Rechercher tous les hôtels sans aucune étiquette :  

```
$filter=tags/any()
```

Rechercher tous les hôtels qui n’ont pas les balises :  

```
$filter=not tags/any()
```


 Rechercher tous les hôtels dans les 10 kilomètres d’un point de référence donnée (où l’emplacement est un champ de type Edm.GeographyPoint) :  

```
$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10
```

 Rechercher tous les hôtels dans une fenêtre d’affichage décrite sous forme de polygone (où l’emplacement est un champ de type Edm.GeographyPoint). Notez que le polygone est fermé (les définitions du premier et du dernier point doivent les mêmes) et [les points doivent être listés dans le sens antihoraire](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

```
$filter=geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

 Rechercher tous les hôtels qui n’ont aucune valeur dans le champ « description » ou qui ont pour ce champ une valeur explicitement définie sur null :  

```
$filter=description eq null
```

Rechercher tous les hôtels avec un nom égal à « Roach motel » ou « Hôtel Budget »). Expressions contiennent des espaces, qui est un délimiteur par défaut. Vous pouvez specicfy un autre délimiteur de guillemets simples comme troisième paramètre de chaîne :  

```
$filter=search.in(name, 'Roach motel,Budget hotel', ',')
```

Rechercher tous les hôtels avec un nom égal à « Roach motel » ou « Budget hotel » séparé par « | ») :  

```
$filter=search.in(name, 'Roach motel|Budget hotel', '|')
```

Rechercher tous les hôtels avec l’étiquette « wifi » ou « pool » :  

```
$filter=tags/any(t: search.in(t, 'wifi, pool'))
```

Rechercher une correspondance sur des expressions au sein d’une collection, tels que « SERVIETTE chauffe racks » ou « sèche-cheveux inclus » dans les balises. 

```
$filter=tags/any(t: search.in(t, 'heated towel racks,hairdryer included', ','))
```

Rechercher tous les hôtels sans l’étiquette « motel » ni l’étiquette « cabin » :  

```
$filter=tags/all(t: not search.in(t, 'motel, cabin'))
```

Rechercher les documents avec le mot « waterfront ». Cette requête de filtre est identique à une [demande de recherche](https://docs.microsoft.com/rest/api/searchservice/search-documents) avec `search=waterfront`.

```
$filter=search.ismatchscoring('waterfront')
```

Rechercher les documents avec le mot « hostel » et une évaluation supérieure ou égale à 4, ou les documents avec le mot « motel » et une évaluation égale à 5. Notez que cette demande ne peut pas être exprimée sans la fonction `search.ismatchscoring`.

```
$filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

Rechercher les documents sans le mot « luxury ».

```
$filter=not search.ismatch('luxury') 
```

Rechercher les documents avec la phrase « ocean view » ou une évaluation égale à 5. La requête `search.ismatchscoring` sera exécutée seulement sur les champs `hotelName` et `description`.
Notez que les documents qui correspondent uniquement à la deuxième clause de la disjonction seront également retournés - les hôtels avec une évaluation égale à 5. L’explication est que ces documents ne correspondent à aucune des parties avec score de l’expression : ils sont retournés avec un score égal à zéro.

```
$filter=search.ismatchscoring('"ocean view"', 'description,hotelName') or rating eq 5
```

Rechercher les documents où les termes « hotel » et « airport » sont distants de 5 mots ou moins dans la description de l’hôtel, et où fumer n’est pas autorisé. Cette requête utilise le [langage de requête Lucene complet](query-lucene-syntax.md).

```
$filter=search.ismatch('"hotel airport"~5', 'description', 'full', 'any') and not smokingAllowed 
```

## <a name="order-by-syntax"></a>Syntaxe de la clause Order by

Le paramètre **$orderby** accepte une liste séparée par des virgules de 32 expressions de la forme `sort-criteria [asc|desc]`. Le critère de tri peut être le nom d’un champ `sortable`, ou un appel à la fonction `geo.distance` ou à la fonction `search.score`. Vous pouvez utiliser `asc` ou `desc` pour spécifier explicitement l’ordre de tri. L’ordre de tri par défaut est croissant.

Si plusieurs documents ont les mêmes critères de tri et que la fonction `search.score` n’est pas utilisée (par exemple si vous triez sur un champ numérique `rating` et que trois documents ont une évaluation de 4), les regroupements sont effectués par score des documents en ordre décroissant. Quand les scores des documents sont identiques (par exemple quand aucune requête de recherche en texte intégral n’est spécifiée dans la demande), l’ordre relatif des documents regroupés est indéterminé.
 
Vous pouvez spécifier plusieurs critères de tri. L’ordre des expressions détermine l’ordre de tri final. Par exemple, pour trier par ordre décroissant sur le score, suivi de l’évaluation, la syntaxe est `$orderby=search.score() desc,rating desc`.

La syntaxe pour `geo.distance` dans **$orderby** est la même que dans **$filter**. Lors de l’utilisation de `geo.distance` dans **$orderby**, le champ auquel elle s’applique doit être de type `Edm.GeographyPoint` et être aussi `sortable`.  

La syntaxe pour `search.score` dans **$orderby** est `search.score()`. La fonction `search.score` ne prend aucun paramètre.  
 

## <a name="order-by-examples"></a>Exemples de clause Order by

Tirer les hôtels par ordre croissant sur le tarif de base :

```
$orderby=baseRate asc
```

Trier les hôtels par ordre décroissant sur l’évaluation, puis par ordre croissant sur le tarif de base (rappelez-vous que l’ordre croissant est la valeur par défaut) :

```
$orderby=rating desc,baseRate
```

Trier les hôtels par ordre décroissant sur l’évaluation, puis par ordre croissant sur la distance à partir des coordonnées spécifiées :

```
$orderby=rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

Trier les hôtels par ordre décroissant sur search.score et l’évaluation, puis par ordre croissant sur la distance à partir des coordonnées spécifiées, de sorte qu’entre deux d’hôtels avec une évaluation identique, le plus proche soit listé en premier :

```
$orderby=search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```
<a name="bkmk_unsupported"></a>

## <a name="unsupported-odata-syntax"></a>Syntaxe OData non prise en charge

-   Expressions arithmétiques  

-   Fonctions (à l’exception des fonctions géospatiales distance et intersects)  

-   `any/all` avec les expressions lambda arbitraires  

## <a name="see-also"></a>Voir aussi  

+ [Navigation à facettes dans Azure Search](search-faceted-navigation.md) 
+ [Filtres dans Recherche Azure](search-filters.md) 
+ [Rechercher des documents &#40;API REST du service Recherche Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Syntaxe de requête Lucene](query-lucene-syntax.md)
+ [Syntaxe des requêtes simples dans Recherche Azure](query-simple-syntax.md)   
