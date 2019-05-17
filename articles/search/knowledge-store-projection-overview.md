---
title: Utilisation de projections dans un magasin de base de connaissances (version préliminaire) - recherche Azure
description: Enregistrer et mettre en forme vos données enrichies du pipeline d’indexation intelligence artificielle pour une utilisation dans les scénarios de recherche
manager: eladz
author: vkurpad
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: vikurpad
ms.custom: seomay2019
ms.openlocfilehash: f1c7278909557dc92f86c5dfc1f190fddf33f607
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540804"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-search"></a>Utilisation de projections dans un magasin de base de connaissances dans recherche Azure

> [!Note]
> Magasin de base de connaissances est en version préliminaire et les a pas été conçu pour la production. Le [API REST version 2019-05-06-Preview](search-api-preview.md) fournit cette fonctionnalité. Il n’existe aucune prise en charge du SDK .NET pour l’instant.
>

Recherche Azure permet l’enrichissement de contenu via des compétences cognitives d’intelligence artificielle et de compétences personnalisées en tant que partie de l’indexation. Enrichissements de structurer vos documents et facilitent la recherche plus efficace. Dans de nombreux cas, les documents enrichis sont utiles pour les scénarios de recherche, par exemple pour l’exploration de données de base de connaissances.

Les projections, un composant de [store de la base de connaissances](knowledge-store-concept-intro.md), sont des vues de documents enrichis qui peuvent être enregistrés dans le stockage physique pour à des fins d’exploration de données de base de connaissances. Une projection vous permet de « projet » vos données dans une forme qui s’aligne avec vos besoins, en conservant les relations afin que les outils tels que Power BI peuvent lire les données sans aucun effort supplémentaire. 

Projections peuvent être sous forme de tableau avec les données stockées dans des lignes et colonnes dans le stockage Table Azure, ou les objets JSON stockés dans le stockage Blob Azure. Vous pouvez définir plusieurs projections de vos données comme il est en cours enrichie. Cela est utile lorsque vous souhaitez que les mêmes données mises en forme différemment pour les cas d’une utilisation individuelle. 

Le magasin de la base de connaissances prend en charge deux types de projections :

+ **Tables** : Pour les données qui constitue la meilleure représentation en tant que lignes et colonnes, projections de table permettent de définir une forme schématisée ou une projection dans le stockage Table. 

+ **Objets**: Lorsque vous avez besoin d’une représentation JSON de vos données et l’enrichissement, les projections de l’objet sont enregistrées en tant qu’objets BLOB.

Pour voir des projections définies dans le contexte, parcourez [la prise en main avec le magasin de la base de connaissances](knowledge-store-howto.md)

## <a name="projection-groups"></a>Groupes de projection

Dans certains cas, vous devrez vos données enrichies dans différentes formes pour répondre à différents objectifs de projet. Le magasin de la base de connaissances vous permet de définir plusieurs groupes de projections. Groupes de projection ont les principales caractéristiques de l’exclusivité mutuelle et parenté suivantes.

### <a name="mutually-exclusivity"></a>Exclusivité mutuellement

Tout le contenu projeté dans un seul groupe est indépendant des données projetées dans d’autres groupes de projection. Cela implique que vous pouvez avoir les mêmes données mises en forme différemment, mais répété dans chaque groupe de projection. 

Une seule contrainte appliquée dans les groupes de projection est l’exclusivité mutuelle de types de projection avec un groupe de projection. Vous pouvez uniquement définir des projections de table ou de projections d’objet dans un groupe unique. Si vous souhaitez que les tables et les objets, définir un groupe de projection pour les tables et un deuxième groupe de projection pour les objets.

### <a name="relatedness"></a>Parenté

Tout le contenu projeté dans un groupe de projection unique conserve les relations au sein des données. Les relations sont basées sur une clé générée, et chaque nœud enfant conserve une référence au nœud parent. Relations ne couvrent pas les groupes de projection et les tables ou les objets créés dans un groupe de projection n’ont aucune relation aux données générées dans d’autres groupes de projection.

## <a name="input-shaping"></a>Entrée mise en forme
Obtention de vos données dans la bonne forme ou la structure est l’utilisation des clés effectif, qu’il s’agisse de tables ou objets. La possibilité de mettre en forme ou une structure de vos données en fonction de la manière dont vous souhaitez accéder et l’utiliser est une fonctionnalité clé exposée en tant que le **modélisateur** compétence dans les compétences dont dispose.  

Projections sont plus faciles à définir lorsque vous avez un objet dans l’arborescence d’enrichissement qui correspondent au schéma de la projection. La mise à jour [compétence de modélisateur](cognitive-search-skill-shaper.md) vous permet de composer un objet à partir de différents nœuds de l’arborescence d’enrichissement et les parents sous un nouveau nœud. Le **modélisateur** compétence vous permet de définir des types complexes avec des objets imbriqués.

Lorsque vous avez une nouvelle forme définie qui contienne tous les éléments que vous avez besoin pour projeter des, vous pouvez maintenant utiliser cette forme comme source pour vos projections ou en tant qu’entrée à une autre compétence.

## <a name="table-projections"></a>Projections de table

Car il permet d’importer facilement, nous vous recommandons de projections de table pour l’exploration de données avec Power BI. En outre, les projections de table autoriser pour la modification de la cardinalité entre la relation de table. 

Vous pouvez projeter un document unique dans votre index en plusieurs tables, en conservant les relations. Lors de la projection à plusieurs tables, la forme complète est projetée dans chaque table, sauf si un nœud enfant est la source d’une autre table dans le même groupe.

### <a name="defining-a-table-projection"></a>Définition d’une projection de table

Lorsque vous définissez une projection de la table dans le `knowledgeStore` élément de vos compétences, commencez par mapper un nœud dans l’arborescence d’enrichissement pour la source de table. En général, ce nœud est le résultat d’une **modélisateur** compétences que vous avez ajouté à la liste des compétences pour produire une forme spécifique dont vous avez besoin pour projeter dans des tables. Le nœud que vous choisissez de projet peut être découpé au projet en plusieurs tables. La définition de tables est une liste de tables que vous souhaitez de projet. 

Chaque table requiert trois propriétés :

+ TableName : Le nom de la table dans le stockage Azure.

+ generatedKeyName: Le nom de colonne de la clé qui identifie de façon unique cette ligne.

+ source : Le nœud de l’arborescence d’enrichissement serez-vous votre enrichissements de. Cela est généralement le résultat d’une mise en forme, mais peut être le résultat d’une des compétences.

Voici un exemple des projections de la table.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
      
    ],
"cognitiveServices": {
… your cognitive services key info
    },

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
          "objects": [
            
          ]
        }
      ]
    }
}
```
Comme illustré dans cet exemple, les expressions clés et les entités sont modélisées dans différentes tables et contient une référence au parent (%{maintable/}) pour chaque ligne. 

L’illustration suivante est une référence à l’exercice jurisprudence dans [la prise en main avec le magasin de la base de connaissances](knowledge-store-howto.md). Dans un scénario où un cas a plusieurs avis parallèles, et chaque avis est enrichie en identifiant les entités qu’il contient, vous pouvez modéliser les projections, comme illustré ici.

![Entités et relations dans les tables](media/knowledge-store-projection-overview/TableRelationships.png "modéliser les relations dans les projections de table")

## <a name="object-projections"></a>Projections de l’objet

Projections de l’objet sont des représentations sous forme de JSON de l’arborescence d’enrichissement peuvent provenir de n’importe quel nœud. Dans de nombreux cas, le même **modélisateur** compétences, ce qui crée une projection de la table peuvent être utilisé pour générer une projection de l’objet. 

```json
{
 
    "name": "your-skillset",
    "skills": [
      …your skills
      
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [
            {
              "storageContainer": "Reviews", 
              "format": "json", 
              "source": "/document/Review", 
              "key": "/document/Review/Id" 
            }
          ]
        }
      ]
    }
}
```

La génération d’une projection d’objet nécessite quelques attributs spécifiques à un objet :

+ storageContainer : Le conteneur dans lequel les objets doit être enregistrés
+ source : Le chemin d’accès au nœud de l’arborescence d’enrichissement est la racine de la projection
+ Clé : Un chemin d’accès qui représente une clé unique pour l’objet à stocker. Il sera utilisé pour créer le nom de l’objet blob dans le conteneur.

## <a name="projection-lifecycle"></a>Cycle de vie de projection

Vos projections ont un cycle de vie qui est lié à la source de données dans votre source de données. Comme vos données sont mis à jour et réindexées, vos projections sont mis à jour avec les résultats de l’enrichissement vérifié que vos projections sont cohérentes avec les données dans votre source de données. Les projections héritent de la stratégie de suppression que vous avez configuré pour votre index. 

## <a name="using-projections"></a>Utilisation de projections

Après l’exécution de l’indexeur, vous pouvez lire les données projetées dans les conteneurs ou les tables que vous avez spécifié par le biais des projections. 

Pour l’analytique, exploration dans Power BI est aussi simple qu’affecter le stockage Table Azure comme source de données. Vous pouvez très facilement créer un ensemble de visualisations sur vos données en exploitant les relations au sein.

Vous pouvez également, si vous avez besoin d’utiliser les données enrichies dans un pipeline de science des données, vous pourriez [charger les données d’objets BLOB dans une trame de données Pandas](../machine-learning/team-data-science-process/explore-data-blob.md).

Enfin, si vous avez besoin d’exporter des données à partir du magasin de la base de connaissances, Azure Data Factory a des connecteurs pour exporter les données et il arrivent dans la base de données de votre choix. 

## <a name="next-steps"></a>Étapes suivantes

Comme prochaine étape, créez votre premier magasin de base de connaissances à l’aide des instructions et des exemples de données.

> [!div class="nextstepaction"]
> [Comment créer un magasin de la base de connaissances](knowledge-store-howto.md).