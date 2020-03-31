---
title: 'Exemple : Facettes à plusieurs niveaux'
titleSuffix: Azure Cognitive Search
description: Découvrez comment créer des structures à facettes pour les taxonomies à plusieurs niveaux, en créant une structure de navigation imbriquée que vous pouvez inclure dans des pages d’application.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 8672fa0911d1a031205bb3340fa0c03ab9492a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792937"
---
# <a name="example-multi-level-facets-in-azure-cognitive-search"></a>Exemple : Facettes à plusieurs niveaux dans Recherche cognitive Azure

Les schémas Recherche cognitive Azure ne prennent pas en charge de manière explicite les catégories de taxonomies à plusieurs niveaux, mais vous pouvez vous en approcher en manipulant le contenu préalablement à l’indexation, puis en appliquant un traitement spécial aux résultats. 

## <a name="start-with-the-data"></a>Prise en main des données

L’exemple du présent article s’appuie sur un exemple précédent, [Modéliser la base de données d’inventaire AdventureWorks](search-example-adventureworks-modeling.md), pour expliquer les facettes à plusieurs niveaux dans Recherche cognitive Azure.

AdventureWorks présente une taxonomie simple à deux niveaux, avec une relation parent-enfant. Pour les profondeurs de taxonomie à longueur fixe de cette structure, une simple requête de jointure SQL peut être utilisée afin de regrouper la taxonomie :

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![Résultats de la requête](./media/search-example-adventureworks/prod-query-results.png "Résultats de la requête")

## <a name="indexing-to-a-collection-field"></a>Indexation dans un champ Collection

Dans l’index contenant cette structure, créez un champ **Collection(Edm.String)** dans le schéma Recherche cognitive Azure pour stocker ces données, en veillant à ce que les attributs du champ incluent la possibilité de recherche, de filtre, de choix multiples et de récupération.

À présent, lorsque vous indexez du contenu faisant référence à une catégorie de taxonomie spécifique, envoyez la taxonomie sous forme de tableau contenant du texte issu de chaque niveau de la taxonomie. Par exemple, pour une entité avec `ProductCategoryId = 5 (Mountain Bikes)`, envoyez le champ comme `[ "Bikes", "Bikes|Mountain Bikes"]`

Notez l'inclusion de la catégorie parente « Bikes » dans la valeur de catégorie enfant « Mountain Bikes ». Chaque sous-catégorie doit incorporer son chemin d’accès complet relatif à l’élément racine. Le séparateur de caractères est arbitraire, mais doit être cohérent et ne pas apparaître dans le texte source. Le caractère de séparation est utilisé dans le code d’application pour reconstruire l’arborescence de la taxonomie à partir des résultats de la facette.

## <a name="construct-the-query"></a>Construire la requête

Lorsque vous émettez des requêtes, incluez la spécification de facette suivante (où la taxonomie correspond à votre champ de taxonomie à choix multiples) : `facet = taxonomy,count:50,sort:value`

La valeur doit être suffisamment élevée pour renvoyer toutes les valeurs de taxonomie possibles. Les données AdventureWorks contiennent 41 valeurs de taxonomie distinctes, et dès lors, `count:50` suffit.

  ![Filtre à facettes](./media/search-example-adventureworks/facet-filter.png "Filtre à facettes")

## <a name="build-the-structure-in-client-code"></a>Créer la structure dans le code client

Dans le code d'application de votre client, reconstruisez l’arborescence de la taxonomie en divisant chaque valeur de facette sur la barre verticale.

```javascript
var sum = 0
  , categories = {children:{},length:0}
  , results = getSearchResults();
separator = separator || '|';
field = field || 'taxonomy';
results['@search.facets'][field].forEach(function(d) {
  var node = categories;
  var parts = d.value.split(separator);
  sum += d.count;
  parts.forEach(function(c, i) {
    if (!_(node.children).has(c)) {
      node.children[c] = {};
      node.children[c].count = d.count;
      node.children[c].children = {};
      node.children[c].length = 0;
      node.children[c].filter = parts.slice(0,i+1).join(separator);
      node.length++;
    }
    node = node.children[c];
  });
});
categories.count = sum;
```

L'objet **catégories** peut maintenant être utilisé pour restituer une arborescence de taxonomie réductible avec comptage précis :

  ![filtre à facettes à plusieurs niveaux](./media/search-example-adventureworks/multi-level-facet.png "filtre à facettes à plusieurs niveaux")

 
Chaque lien de l’arborescence doit appliquer le filtre connexe. Par exemple :

+ **taxonomy/any**`(x:x eq 'Accessories')` renvoie tous les documents de la branche Accessories
+ **taxonomy/any**`(x:x eq 'Accessories|Bike Racks')` renvoie uniquement les documents présentant une sous-catégorie de Bike Racks sous la branche Accessories.

Cette technique sera amenée à évoluer pour couvrir des scénarios plus complexes, tels que les arborescences de taxonomie plus détaillées et les sous-catégories dupliquées intervenant sous des catégories parentes différentes (par exemple, `Bike Components|Forks` et `Camping Equipment|Forks`).

> [!TIP]
> La vitesse de la requête dépend du nombre de facettes renvoyées. En présence d'ensembles de taxonomie très volumineux, envisagez d’ajouter un champ à choix multiples **Edm.String** pour contenir la valeur de taxonomie de niveau supérieur pour chaque document. Appliquez la même technique que ci-dessus, mais effectuez uniquement la requête de facette de la collection (filtrée sur le champ de taxonomie racine) lorsque l’utilisateur développe un nœud de niveau supérieur. Ou, si un rappel à 100 % n’est pas requis, réduisez simplement le nombre de facettes de manière raisonnable et vérifiez que les entrées de facette sont triées par nombre.

## <a name="see-also"></a>Voir aussi

[Exemple : Modéliser la base de données d’inventaire AdventureWorks pour la Recherche cognitive Azure](search-example-adventureworks-modeling.md)