---
title: 'Exemple : Modéliser la base de données d’inventaire AdventureWorks'
titleSuffix: Azure Cognitive Search
description: Découvrez comment modéliser des données relationnelles, en les transformant en un jeu de données aplati, pour l’indexation et la recherche en texte intégral dans la Recherche cognitive Azure.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: heidist
ms.openlocfilehash: edb6162724938962df8a7340afea6e930a0b1049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792990"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-cognitive-search"></a>Exemple : Modéliser la base de données d’inventaire AdventureWorks pour la Recherche cognitive Azure

La Recherche cognitive Azure accepte un ensemble de lignes aplati comme entrées du [pipeline d’indexation (ingestion des données)](search-what-is-an-index.md). Si vos données sources proviennent d’une base de données relationnelle SQL Server, cet article montre une approche de la création d’un ensemble de lignes aplati avant l’indexation, en utilisant l’exemple de base de données AdventureWorks comme exemple.

## <a name="about-adventureworks"></a>À propos d’AdventureWorks

Si vous avez une instance SQL Server, vous connaissez peut-être l’[exemple de base de données AdventureWorks](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017). Parmi les tables incluses dans cette base de données figurent cinq tables qui exposent des informations sur les produits.

+ **ProductModel** : nom
+ **Product** : nom, couleur, coût, taille, poids, image, catégorie (chaque ligne est jointe à un ProductModel spécifique)
+ **ProductDescription** : description
+ **ProductModelProductDescription** : paramètres régionaux (chaque ligne joint un ProductModel à un ProductDescription spécifique pour une langue spécifique)
+ **ProductCategory** : nom, catégorie parente

L’objectif de cet exemple est la combinaison de toutes ces données en un ensemble de lignes aplati qui peut être ingéré dans un index de recherche. 

## <a name="considering-our-options"></a>Étude de nos options

L’approche naïve consisterait à indexer toutes les lignes de la table Product (jointe le cas échéant) car la table Product contient les informations les plus spécifiques. Toutefois, cette approche exposerait l’index de recherche à des doublons identifiés dans un jeu de résultats. Par exemple, le modèle Road-650 est disponible dans deux couleurs et six tailles. Une requête pour « road bikes » (vélos de route) serait alors dominée par douze instances du même modèle, différenciées uniquement par la taille et la couleur. Les six autres modèles spécifiques pour la route seraient relégués au Royaume des ténèbres de la recherche : la deuxième page.

  ![Liste de produits](./media/search-example-adventureworks/products-list.png "Liste de produits")
 
Notez que le modèle Road-650 a douze options. Les lignes d’entités de type un-à-plusieurs sont mieux représentées sous la forme de champs à valeurs multiples ou de champs à valeurs pré-agrégées dans l’index de recherche.

La résolution de ce problème n’est pas aussi simple que de déplacer l’index cible vers la table ProductModel. Cette opération ignorerait les données importantes de la table Product qui doivent toujours être représentées dans les résultats de la recherche.

## <a name="use-a-collection-data-type"></a>Utiliser un type de données Collection

La « bonne approche » consiste à utiliser une fonctionnalité de schéma de recherche qui n’a pas de parallèle direct dans le modèle de base de données : **Collection(Edm.String)** . Cette construction est définie dans le schéma d’index de la Recherche cognitive Azure. Un type de données Collection est utilisé quand vous devez représenter une liste de chaînes individuelles plutôt qu’une (seule) chaîne très longue. Si vous avez des tags ou des mots clés, vous devez utiliser un type de données Collection pour ce champ.

En définissant des champs d’index à valeurs multiples de **Collection(Edm.String)** pour « color » (couleur), « size » (taille) et « image », les informations auxiliaires sont conservées pour les facettes et le filtrage sans polluer l’index avec des entrées en double. De même, appliquez des fonctions d’agrégation aux champs Product numériques, en indexant **minListPrice** au lieu de chaque produit unique **listPrice**.

Pour un index avec ces structures, une recherche de « mountain bikes » (vélos tout terrain) afficherait des modèles de vélos distincts, tout en conservant les métadonnées importantes comme la couleur, la taille et le prix le plus bas. La capture d’écran suivante fournit une illustration.

  ![Exemple de recherche de VTT](./media/search-example-adventureworks/mountain-bikes-visual.png "Exemple de recherche de VTT")

## <a name="use-script-for-data-manipulation"></a>Utiliser un script pour la manipulation des données

Malheureusement, ce type de modélisation ne peut pas être facilement obtenu par le biais d’instructions SQL autonomes. À la place, utilisez un script NodeJS simple pour charger les données, puis mappez-le dans des entités JSON destinées à la recherche.

Le mappage final de recherche dans les bases de données ressemble à ceci :

+ model (Edm.String: searchable, filterable, retrievable) from "ProductModel.Name"
+ description_en (Edm.String: searchable) from "ProductDescription" pour le modèle où culture=’en’
+ color (Collection(Edm.String): searchable, filterable, facetable, retrievable) : valeurs uniques de « Product.Color » pour le modèle
+ size (Collection(Edm.String): searchable, filterable, facetable, retrievable) : valeurs uniques de « Product.Size » pour le modèle
+ image (Collection(Edm.String): retrievable) : valeurs uniques de « Product.ThumbnailPhoto » pour le modèle
+ minStandardCost (Edm.Double: filterable, facetable, sortable, retrievable) : minimum cumulé de tout « Product.StandardCost » pour le modèle
+ minListPrice (Edm.Double: filterable, facetable, sortable, retrievable) : minimum cumulé de tout « Product.ListPrice » pour le modèle
+ minWeight (Edm.Double: filterable, facetable, sortable, retrievable) : minimum cumulé de tout « Product.Weight » pour le modèle
+ products (Collection(Edm.String): searchable, filterable, retrievable) : valeurs uniques de « Product.Name » pour le modèle

Après avoir joint la table ProductModel à Product et ProductDescription, utilisez [lodash](https://lodash.com/) (ou Linq en C#) pour transformer rapidement le jeu de résultats :

```javascript
var records = queryYourDatabase();
var models = _(records)
  .groupBy('ModelName')
  .values()
  .map(function(d) {
    return {
      model: _.first(d).ModelName,
      description: _.first(d).Description,
      colors: _(d).pluck('Color').uniq().compact().value(),
      products: _(d).pluck('ProductName').uniq().compact().value(),
      sizes: _(d).pluck('Size').uniq().compact().value(),
      images: _(d).pluck('ThumbnailPhotoFilename').uniq().compact().value(),
      minStandardCost: _(d).pluck('StandardCost').min(),
      maxStandardCost: _(d).pluck('StandardCost').max(),
      minListPrice: _(d).pluck('ListPrice').min(),
      maxListPrice: _(d).pluck('ListPrice').max(),
      minWeight: _(d).pluck('Weight').min(),
      maxWeight: _(d).pluck('Weight').max(),
    };
  })
  .value();
```

Le code JSON obtenu ressemble à ce qui suit :

```json
[
  {
    "model": "HL Road Frame",
    "colors": [
      "Black",
      "Red"
    ],
    "products": [
      "HL Road Frame - Black, 58",
      "HL Road Frame - Red, 58",
      "HL Road Frame - Red, 62",
      "HL Road Frame - Red, 44",
      "HL Road Frame - Red, 48",
      "HL Road Frame - Red, 52",
      "HL Road Frame - Red, 56",
      "HL Road Frame - Black, 62",
      "HL Road Frame - Black, 44",
      "HL Road Frame - Black, 48",
      "HL Road Frame - Black, 52"
    ],
    "sizes": [
      "58",
      "62",
      "44",
      "48",
      "52",
      "56"
    ],
    "images": [
      "no_image_available_small.gif"
    ],
    "minStandardCost": 868.6342,
    "maxStandardCost": 1059.31,
    "minListPrice": 1431.5,
    "maxListPrice": 1431.5,
    "minWeight": 961.61,
    "maxWeight": 1043.26
  }
]
```

Enfin, voici la requête SQL permettant de retourner le jeu d’enregistrements initial. J’ai utilisé le module NPM [mssql](https://www.npmjs.com/package/mssql) pour charger les données dans mon application NodeJS.

```T-SQL
SELECT
  m.Name as ModelName,
  d.Description,
  p.Name as ProductName,
  p.*
FROM 
  SalesLT.ProductModel m
INNER JOIN 
  SalesLT.ProductModelProductDescription md
  ON m.ProductModelId = md.ProductModelId
INNER JOIN 
  SalesLT.ProductDescription d
  ON md.ProductDescriptionId = d.ProductDescriptionId
LEFT JOIN 
  SalesLT.product p
  ON m.ProductModelId = p.ProductModelId
WHERE
  md.Culture='en'
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Exemple : Taxonomies de facettes à plusieurs niveaux dans la Recherche cognitive Azure](search-example-adventureworks-multilevel-faceting.md)