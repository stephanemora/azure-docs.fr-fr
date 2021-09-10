---
title: Concepts de projection
titleSuffix: Azure Cognitive Search
description: Enregistrez et mettez en forme vos données enrichies provenant du pipeline d’indexation d’enrichissement par IA dans une base de connaissances pour les utiliser dans des scénarios autres que ceux de recherche en texte intégral.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 5b28540f30c23abc4ba1d58f6984524984f2c001
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524778"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>« Projections » de base de connaissances dans Recherche cognitive Azure

Les projections, un élément de la [base de connaissances](knowledge-store-concept-intro.md), sont des vues de documents enrichis qui peuvent être enregistrées dans une mémoire physique à des fins d’exploration des connaissances. Une projection vous permet de « projeter » vos données dans une forme qui répond à vos besoins, en conservant les relations afin que les outils tels que Power BI puissent lire les données sans effort supplémentaire.

Les projections peuvent être tabulaires, où les données sont articulées en lignes et en colonnes dans Stockage Table Azure, des objets JSON stockés dans Stockage Blob Azure ou encore des images binaires également stockées dans Stockage Blob. Vous pouvez définir plusieurs projections de vos données pendant leur enrichissement. Des projections multiples sont utile lorsque vous souhaitez que les mêmes données soient mises en forme différemment pour des cas d’utilisation individuels.

La base de connaissances prend en charge trois types de projections :

+ **Tables** : Pour les données qui sont mieux représentées sous forme de lignes et de colonnes, les projections de table vous permettent de définir une forme schématisée ou une projection dans le stockage Table. Seuls les objets JSON valides peuvent être projetés en tant que tables. Dans la mesure où un document enrichi peut contenir des nœuds qui ne sont pas des objets JSON nommés, vous ajouterez une [compétence Modélisateur ou utiliserez la modélisation inlined](knowledge-store-projection-shape.md) dans une compétence pour créer un JSON valide. 

+ **Objets** : Lorsque vous avez besoin d’une représentation JSON de vos données et enrichissements, utilisez des projections d’objets pour enregistrer la sortie sous forme de blobs. Comme pour les projections de table, seuls les objets JSON valides peuvent être projetés en tant qu’objets, et la modélisation peut vous aider à le faire.

+ **Fichiers** : Quand vous devez enregistrer les images extraites des documents, les projections de fichiers vous permettent d’enregistrer les images normalisées dans un stockage d’objets blob.

Pour voir des projections définies en contexte, consultez [Créer une base de connaissances avec REST](knowledge-store-create-rest.md).

## <a name="basic-pattern"></a>Modèle de base

Les projections sont un ensemble de collections complexes sous une définition `knowledgeStore` dans un objet ensemble de compétences. Chaque ensemble de tables, d’objets et de fichiers constitue un *groupe de projets*. Vous pouvez avoir plusieurs groupes si les exigences de stockage incluent la prise en charge de différents outils et scénarios. Au sein d’un même groupe, vous pouvez avoir plusieurs tables, objets et fichiers. 

En général, un seul groupe est utilisé, mais l’exemple suivant en présente deux pour illustrer le modèle lorsque plusieurs groupes existent.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [],
            "objects": [],
            "files": []
        }, 
        {
            "tables": [],
            "objects": [],
            "files": []
        }
    ]
}
```

### <a name="projection-groups"></a>Groupes de projections

Il est utile de disposer de plusieurs ensembles de combinaisons table-objet-fichier pour prendre en charge différents scénarios. Vous pouvez utiliser un ensemble pour la conception et le débogage d’un ensemble de compétences, en capturant la sortie pour un examen plus approfondi, tandis qu’un deuxième ensemble collecte la sortie utilisée pour une application en ligne et qu’un troisième est utilisé pour les charges de travail de science des données.

Les groupes de projection disposent des principales caractéristiques d’exclusivité mutuelle et de parenté suivantes. 

| Principe | Description |
|-----------|-------------|
| Exclusivité mutuelle | Tout le contenu projeté dans un même groupe est indépendant des données projetées dans d’autres groupes de projections. Cette indépendance implique que les mêmes données peuvent être mises en forme différemment, mais répétées dans chaque groupe de projections. Chaque groupe obtient des données de la même source (arborescence d’enrichissement), mais il est totalement isolé de la combinaison table-objet-fichier de tout groupe de projection appairé.|
| Parenté | Les projections prennent en charge la parenté au sein du groupe. Au sein d’un groupe, le contenu d’une table est lié au contenu d’un objet ou d’un fichier. Entre les types (tables, objets et fichiers) d’un même groupe, les relations sont préservées lorsqu’un seul nœud d’une arborescence d’enrichissement (par exemple, `/document/translated_text`) est projeté sur différentes tables et différents objets. À l’intérieur des tableaux, les relations sont basées sur une clé générée, et chaque nœud enfant conserve une référence au nœud parent. Par exemple, imaginez un scénario dans lequel vous avez un document contenant des images et du texte. Vous pouvez projeter le texte vers des tables ou des objets, et les images vers des fichiers dont l’URL figure dans une colonne/propriété dans ces tables ou objets.|

La décision de créer des groupes de projections supplémentaires est souvent basée sur les exigences en matière de représentation des données. Vous pouvez le faire si vous souhaitez des relations différentes entre les données. Dans un ensemble, les données sont liées, en supposant que ces relations existent et peuvent être détectées. Si vous créez des ensembles supplémentaires, les documents de chaque groupe ne sont jamais liés. Voici un exemple de ce que pourrait donner une utilisation de plusieurs groupes de projection si vous souhaitez que les mêmes données soient projetées pour une utilisation avec votre système en ligne, qu’elles soient représentées de manière spécifique, et que les mêmes données soient projetées pour une utilisation dans un pipeline de science des données représenté différemment.

<!-- ## Knowledge Store composition

The knowledge store consists of an annotation cache and projections. The *cache* is used by the service internally to cache the results from skills and track changes. A *projection* defines the schema and structure of the enrichments that match your intended use.

Within Azure Storage, projections can be articulated as tables, objects, or files.

+ As an object, the projection maps to Blob storage, where the projection is saved to a container, within which are the objects or hierarchical representations in JSON for scenarios like a data science pipeline.

+ As a table, the projection maps to Table storage. A tabular representation preserves relationships for scenarios like data analysis or export as data frames for machine learning. The enriched projections can then be easily imported into other data stores. 

You can create multiple projections in a knowledge store to accommodate various constituencies in your organization. A developer might need access to the full JSON representation of an enriched document, while data scientists or analysts might want granular or modular data structures shaped by your skillset.

For instance, if one of the goals of the enrichment process is to also create a dataset used to train a model, projecting the data into the object store would be one way to use the data in your data science pipelines. Alternatively, if you want to create a quick Power BI dashboard based on the enriched documents the tabular projection would work well. -->

## <a name="table-projections"></a>Projections de table

Les projections de table sont recommandées pour les scénarios qui nécessitent une exploration des données, comme l’analyse avec Power BI. La définition de tables est une liste de tables que vous souhaitez projeter. Chaque table requiert trois propriétés :

+ tableName : Nom de la table dans Stockage Table Azure.

+ generatedKeyName : Nom de colonne de la clé qui identifie de façon unique cette ligne.

+ source : Nœud de l’arborescence d’enrichissement, source de vos enrichissements. Ce nœud est généralement la sortie d’une compétence Modélisateur qui définit la forme de la table. Les tables comportent des lignes et des colonnes, et la modélisation est le mécanisme par lequel les lignes et les colonnes sont spécifiées. Vous pouvez utiliser une [compétence Modélisateur ou des modélisations inlined](knowledge-store-projection-shape.md). La compétence Modélisateur produit un JSON valide, mais il peut s’agir de la sortie de n’importe quelle compétence, tant que le JSON est valide. 

Comme illustré dans cet exemple, les expressions et entités clés sont modélisées dans différentes tables et contiennent une référence au parent (MainTable) pour chaque ligne. 

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [
        { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
        { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
        { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
      ]
    },
    {
      "objects": [ ]
    },
    {
      "files": [ ]
    }
  ]
}
```

Le nœud d’enrichissement spécifié dans « source » peut être découpé en plusieurs tables. La référence à « EnrichedShape » est la sortie d’une compétence Modélisateur (non illustrée). Les entrées de cette compétence déterminent la composition de la table et les lignes qui la remplissent. Les clés générées et les champs communs de chaque table constituent la base des relations entre les tables.

## <a name="object-projections"></a>Projections d’objet

Les projections d’objet sont des représentations JSON de l’arborescence d’enrichissement pouvant provenir de n’importe quel nœud. Dans de nombreux cas, la même compétence de modélisateur que celle qui crée une projection de table peut être utilisée pour générer une projection d’objet. 

La génération d’une projection d’objet nécessite quelques attributs spécifiques à un objet :

+ storageContainer : Conteneur de blobs dans lequel les objets seront enregistrés

+ source : Chemin d’accès au nœud de l’arborescence d’enrichissement qui est la racine de la projection

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "hotelreviews", 
          "source": "/document/hotel"
        }
      ]
    },
    {
        "files": [ ]
    }
  ]
}
```

## <a name="file-projections"></a>Projections de fichier

Les projections de fichier agissent uniquement sur la collection `normalized_images`, mais sont autrement similaires aux projections d’objet dans la mesure où elles sont enregistrées dans un conteneur de blobs, avec un préfixe de dossier correspondant à la valeur encodée en base64 de l’ID du document. 

Les projections de fichiers ne peuvent pas partager le même conteneur que les projections d’objets. Elles doivent être projetées vers un conteneur distinct. Les projections de fichier ont les mêmes propriétés que les projections d’objet :

+ storageContainer : Conteneur de blobs dans lequel les objets seront enregistrés

+ source : Chemin d’accès au nœud de l’arborescence d’enrichissement qui est la racine de la projection

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [ ]
    },
    {
        "files": [
              {
              "storageContainer": "ReviewImages",
              "source": "/document/normalized_images/*"
            }
        ]
    }
  ]
}
```

## <a name="projection-shaping"></a>Modélisation de la projection

Les projections sont plus simples à définir lorsque vous avez un objet dans l’arborescence d’enrichissement qui correspond au schéma de la projection, qu’il s’agisse de tables ou d’objets. La possibilité de modéliser ou de structurer vos données en fonction de l’utilisation prévue est généralement obtenue en ajoutant une [compétence Modélisateur](cognitive-search-skill-shaper.md) à votre ensemble de compétences. Les formes produites par une compétence Modélisateur sont utilisées comme `source` pour une projection, mais elles peuvent également être utilisées comme entrée pour une autre compétence.

En termes simples, pour les projections de table, la compétence Modélisateur détermine les colonnes ou les champs de vos tables. Les entrées de la compétence Modélisateur sont constituées de nœuds dans une arborescence d’enrichissement. La sortie est une structure que vous spécifiez dans la projection de table. Dans l’exemple suivant, une projection de table appelée « mytableprojection » se compose des entrées, telles que spécifiées par la compétence Modélisateur.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
          "name": "reviews_text",
          "source": "/document/reviews_text"
        }, 
        {
          "name": "reviews_title",
          "source": "/document/reviews_title"
        },
        {
          "name": "reviews_username",
          "source": "/document/reviews_username"
        },
    ],
    "outputs": [
      {
        "name": "output",
        "targetName": "mytableprojection"
      }
    ]
}
```

La compétence Modélisateur vous permet de composer un objet à partir de différents nœuds de l’arborescence d’enrichissement et de les apparenter sous un nouveau nœud. Elle vous permet également de définir des types complexes avec des objets imbriqués. Pour voir quelques exemples, consultez la documentation sur la [compétence Modélisateur](cognitive-search-skill-shaper.md).

## <a name="projection-slicing"></a>Découpage de la projection

Dans un groupe de projection de table, un seul nœud de l’arborescence d’enrichissement peut être divisé en plusieurs tables associées, de sorte que chaque table contienne une catégorie de données. Cela peut être utile pour l’analyse, où vous pouvez contrôler si et comment les données associées sont agrégées.

Pour créer plusieurs tables enfant, commencez par la table parent, puis créez des tables supplémentaires qui s’appuient sur la source du parent. Dans cet exemple, « KeyPhrases » et « Entities » prennent des tranches de « /document/EnrichedShape ».

```json
"tables": [
  { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
  { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
  { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
]
```

Lors de la projection dans plusieurs tables, la forme complète est projetée dans chaque table, sauf si un nœud enfant est la source d’une autre table du même groupe. Si vous ajoutez une projection dont le chemin source est un nœud enfant d’une projection existante, le nœud enfant est divisé à partir du nœud parent et projeté dans la nouvelle table ou le nouvel objet associés. Cette technique vous permet de définir un nœud unique dans une compétence Modélisateur qui peut être la source de toutes vos projections.

## <a name="projection-lifecycle"></a>Cycle de vie de projection

Les projections ont un cycle de vie qui est lié aux données sources de votre source de données. Lorsque les données sources sont mises à jour et réindexées, les projections sont mises à jour avec les résultats des enrichissements, ce qui garantit que vos projections sont cohérentes avec les données de votre source de données. Toutefois, les projections sont également stockées de manière indépendante dans Stockage Azure. Elles ne seront pas supprimées lorsque l’indexeur ou le service de recherche lui-même sera supprimé. 

## <a name="using-projections"></a>Utilisation de projections

Après l’exécution de l’indexeur, vous pouvez lire les données projetées dans les conteneurs ou les tables que vous avez spécifiés par le biais de projections.

Pour l’analyse, l’exploration dans Power BI est aussi simple que de définir le Stockage Table Azure comme la source de données. Vous pouvez facilement créer un ensemble de visualisations sur vos données en utilisant les relations incluses.

Si vous devez utiliser les données enrichies dans un pipeline de science des données, vous pouvez également [charger les données d’objets Blob dans un dataframe Pandas](/azure/architecture/data-science-process/explore-data-blob).

Enfin, si vous devez exporter vos données de la base de connaissances, Azure Data Factory comprend des connecteurs pour exporter les données et les placer dans la base de données de votre choix. 

## <a name="next-steps"></a>Étapes suivantes

À l’étape suivante, créez votre première base de connaissances à l’aide d’exemples de données et d’instructions.

> [!div class="nextstepaction"]
> [Créer une base de connaissances avec REST](knowledge-store-create-rest.md).

Pour en savoir plus sur les concepts avancés tels que le découpage, la modélisation inlined et les relations, consultez [Définir des projections dans une base de connaissances](knowledge-store-projections-examples.md).
