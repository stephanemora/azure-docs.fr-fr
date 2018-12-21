---
title: Prise en charge de Gremlin dans Azure Cosmos DB
description: Découvrez le langage Gremlin d’Apache TinkerPop. Découvrez les fonctionnalités et les procédures disponibles dans Azure Cosmos DB.
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: overview
ms.date: 01/02/2018
ms.author: lbosq
ms.openlocfilehash: 53ca762232db5e79acdacbb3d52ce05f88dc108b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080399"
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Prise en charge des graphes Azure Cosmos DB Gremlin
Azure Cosmos DB prend en charge le langage de traversée de graphe [d’Apache Tinkerpop](https://tinkerpop.apache.org), [Gremlin](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps). Il s’agit d’une API Gremlin qui permet de créer des entités de graphes et d’effectuer des opérations de requête de graphe. Vous pouvez utiliser le langage Gremlin pour créer des entités de graphes (vertex et bords), modifier les propriétés au sein de ces entités, exécuter des requêtes et traversées et supprimer des entités. 

Azure Cosmos DB apporte des fonctionnalités d’entreprise aux bases de données de graphes. Cela inclut la distribution globale, la mise à l’échelle indépendante du stockage et du débit, les latences prévisibles de quelques millisecondes, l’indexation automatique, les contrats SLA, ainsi que la disponibilité de lecture pour les comptes de base de données couvrant plusieurs régions. Étant donné qu’Azure Cosmos DB prend en charge TinkerPop/Gremlin, vous pouvez facilement migrer des applications écrites à l’aide d’une autre base de données de graphes sans avoir à apporter des modifications de code. En outre, en vertu de la prise en charge de Gremlin, Azure Cosmos DB intègre parfaitement les infrastructures d’analyse compatibles avec TinkerPop comme [Apache Spark GraphX](https://spark.apache.org/graphx/). 

Dans cet article, nous fournissons une procédure pas à pas de Gremlin, et nous énumérons les fonctionnalités de Gremlin et les étapes prises en charge dans l’API Gremlin.

## <a name="gremlin-by-example"></a>Gremlin par l’exemple
Nous allons utiliser un exemple de graphe pour comprendre comment les requêtes peuvent être exprimées dans Gremlin. L’illustration suivante montre une application métier qui gère les données sur les utilisateurs, les centres d’intérêt et les appareils sous la forme d’un graphe.  

![Exemple de base de données montrant des personnes, des appareils et des centres d’intérêt](./media/gremlin-support/sample-graph.png) 

Ce graphe présente les types suivants de vertex (appelés « label » dans Gremlin) :

- Personnes : le graphique comporte trois personnes (Robin, Thomas et Ben).
- Centres d'intérêt : leurs centres d'intérêt, dans cet exemple, le football.
- Appareils : les appareils utilisés par ces personnes.
- Systèmes d'exploitation : systèmes d'exploitation de ces appareils.

Nous représentons les relations entre ces entités à l’aide des types/labels de bord suivants :

- Connaît : par exemple, « Thomas connaît Robin »
- S’intéresse : pour représenter les centres d'intérêt des personnes dans notre graphe, par exemple, « Ben s'intéresse au football »
- ExécuteSE : l'ordinateur portable exécute le système d'exploitation Windows
- Utilise : pour représenter l'appareil qu'une personne utilise. Par exemple, Robin utilise un téléphone Motorola dont le numéro de série est 77

Nous allons exécuter certaines opérations sur ce graphe à l’aide de la [Console Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console). Vous pouvez également effectuer ces opérations à l’aide de pilotes de Gremlin dans la plateforme de votre choix (Java, Node.js, Python ou .NET).  Avant d’examiner ce qui est pris en charge dans Azure Cosmos DB, penchons-nous sur quelques exemples pour vous familiariser avec la syntaxe.

En premier lieu, examinons CRUD. L’instruction Gremlin suivante insère le vertex « Thomas » dans le graphe :

```
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Ensuite, l’instruction Gremlin suivante insère un bord « connaît » entre Thomas et Robin.

```
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

La requête suivante renvoie les vertex « personne » dans l’ordre décroissant de leur prénom :
```
:> g.V().hasLabel('person').order().by('firstName', decr)
```

Les graphes brillent lorsque vous devez répondre aux questions telles que « Quels systèmes d’exploitation les amis de Thomas utilisent-ils ? ». Vous pouvez exécuter cette traversée Gremlin simple pour obtenir cette information à partir du graphe :

```
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```
Nous allons maintenant voir ce qu’Azure Cosmos DB offre aux développeurs Gremlin.

## <a name="gremlin-features"></a>Fonctionnalités de Gremlin
TinkerPop est une norme qui couvre un large éventail de technologies de graphes. Par conséquent, elle dispose de la terminologie standard utilisée pour décrire les fonctionnalités offertes par un fournisseur de graphes. Azure Cosmos DB fournit une base de données de graphes permanente, concurrentielle et accessible en écriture qui peut être partitionnée entre plusieurs serveurs ou clusters. 

Le tableau suivant répertorie les fonctionnalités TinkerPop implémentées par Azure Cosmos DB : 

| Catégorie | Implémentation d’Azure Cosmos DB |  Notes | 
| --- | --- | --- |
| Fonctionnalités de graphe | Fournit la persistance et ConcurrentAccess. Conçu pour prendre en charge les transactions | Les méthodes de l’ordinateur peuvent être implémentées via le connecteur Spark. |
| Fonctionnalités variables | Prend en charge les valeurs booléennes, entières, byte, doubles, flottantes, longues et de chaîne | Prend en charge les types primitifs, et est compatible avec des types complexes via un modèle de données |
| Fonctionnalités de vertex | Prend en charge RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty  | Prend en charge la création, la modification et la suppression de vertex |
| Fonctionnalités de propriétés de vertex | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Prend en charge la création, la modification et la suppression des propriétés vertex |
| Fonctionnalités de bords | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Prend en charge la création, la modification et la suppression de bords |
| Fonctionnalités de propriétés de bords | Properties, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Prend en charge la création, la modification et la suppression de bords |

## <a name="gremlin-wire-format-graphson"></a>Format de câble Gremlin : GraphSON

Azure Cosmos DB utilise le [format GraphSON](https://github.com/thinkaurelius/faunus/wiki/GraphSON-Format) lors du renvoi des résultats des opérations Gremlin. GraphSON est le format standard Gremlin pour représenter des vertex, des bords et des propriétés (propriétés à valeurs uniques et multiples) à l’aide de JSON. 

Par exemple, l’extrait de code suivant montre une représentation sous forme de GraphSON d’un sommet *retourné au client* dans Azure Cosmos DB. 

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

Les propriétés utilisées par GraphSON pour les vertex sont les suivantes :

| Propriété | Description |
| --- | --- |
| id | ID du vertex. Doit être unique (en association avec la valeur de _partition, le cas échéant) |
| label | Le label du vertex. Élément facultatif, utilisé pour décrire le type d’entité. |
| Type | Utilisé pour distinguer les vertex des documents non graphes |
| properties | Sac de propriétés définies par l’utilisateur associé au vertex. Chaque propriété peut avoir plusieurs valeurs. |
| _partition (configurable) | La clé de partition du vertex. Peut être utilisé pour augmenter la taille des instances des graphes sur plusieurs serveurs |
| outE | Contient une liste des bords extérieurs d’un vertex. Permet de stocker les informations de contiguïté avec des vertex pour une exécution rapide des traversées. Les bords sont regroupés en fonction de leurs labels. |

Et le bord contient les informations suivantes pour faciliter la navigation vers d’autres parties du graphe.

| Propriété | Description |
| --- | --- |
| id | L’ID du bord. Doit être unique (en association avec la valeur de _partition, le cas échéant) |
| label | Le label du bord. Cette propriété est facultative, elle est utilisée pour décrire le type de relation. |
| inV | Contient une liste des vertex d’entrée pour un bord. Permet de stocker les informations de contiguïté avec le bord pour une exécution rapide des traversées. Les vertex sont regroupés en fonction de leurs labels. |
| properties | Sac de propriétés définies par l’utilisateur associé au bord. Chaque propriété peut avoir plusieurs valeurs. |

Chaque propriété peut stocker plusieurs valeurs dans un tableau. 

| Propriété | Description |
| --- | --- |
| value | Valeur de la propriété

## <a name="gremlin-steps"></a>Étapes de Gremlin
Nous allons maintenant examiner les étapes Gremlin prises en charge par Azure Cosmos DB. Pour des références complètes sur Gremlin, consultez [Référence TinkerPop](https://tinkerpop.apache.org/docs/current/reference).

| étape | Description | Documentation TinkerPop 3.2 |
| --- | --- | --- |
| `addE` | Ajoute un bord reliant deux vertex | [étape addE](https://tinkerpop.apache.org/docs/current/reference/#addedge-step) |
| `addV` | Ajoute un vertex au graphe | [étape addV](https://tinkerpop.apache.org/docs/current/reference/#addvertex-step) |
| `and` | Fait en sorte que toutes les traversées retournent une valeur | [étape and](https://tinkerpop.apache.org/docs/current/reference/#and-step) |
| `as` | Un modulateur d’étape pour attribuer une variable à la sortie d’une étape | [étape as](https://tinkerpop.apache.org/docs/current/reference/#as-step) |
| `by` | Un modulateur d’étape utilisé avec `group` et `order` | [étape by](https://tinkerpop.apache.org/docs/current/reference/#by-step) |
| `coalesce` | Renvoie la première traversée qui renvoie un résultat | [étape coalesce](https://tinkerpop.apache.org/docs/current/reference/#coalesce-step) |
| `constant` | Renvoie une valeur constante. Utilisé avec `coalesce`| [étape constant](https://tinkerpop.apache.org/docs/current/reference/#constant-step) |
| `count` | Renvoie le nombre de traversées | [étape count](https://tinkerpop.apache.org/docs/current/reference/#count-step) |
| `dedup` | Renvoie les valeurs en supprimant les doublons | [étape dedup](https://tinkerpop.apache.org/docs/current/reference/#dedup-step) |
| `drop` | Supprime les valeurs (vertex/bord) | [étape drop](https://tinkerpop.apache.org/docs/current/reference/#drop-step) |
| `fold` | Agit comme une barrière qui calcule l’agrégation des résultats| [étape fold](https://tinkerpop.apache.org/docs/current/reference/#fold-step) |
| `group` | Regroupe les valeurs basées sur les labels spécifiés| [étape group](https://tinkerpop.apache.org/docs/current/reference/#group-step) |
| `has` | Permet de filtrer les propriétés, les vertex et les bords. Prend en charge `hasLabel`, `hasId`, `hasNot`, et les variantes `has`. | [étape has](https://tinkerpop.apache.org/docs/current/reference/#has-step) |
| `inject` | Injecter des valeurs dans un flux de données| [étape inject](https://tinkerpop.apache.org/docs/current/reference/#inject-step) |
| `is` | Permet d’exécuter un filtre à l’aide d’une expression booléenne | [étape is](https://tinkerpop.apache.org/docs/current/reference/#is-step) |
| `limit` | Permet de limiter le nombre d’éléments dans la traversée| [étape limit](https://tinkerpop.apache.org/docs/current/reference/#limit-step) |
| `local` | Local encapsule une section d’une traversée, similaire à une sous-requête | [étape local](https://tinkerpop.apache.org/docs/current/reference/#local-step) |
| `not` | Permet de produire la négation d’un filtre | [étape not](https://tinkerpop.apache.org/docs/current/reference/#not-step) |
| `optional` | Renvoie le résultat de la traversée spécifiée si elle produit un résultat, sinon renvoie l’élément appelant | [étape optional](https://tinkerpop.apache.org/docs/current/reference/#optional-step) |
| `or` | Garantit qu’au moins l’une des traversées renvoie une valeur | [étape or](https://tinkerpop.apache.org/docs/current/reference/#or-step) |
| `order` | Renvoie les résultats dans l’ordre de tri spécifié | [étape order](https://tinkerpop.apache.org/docs/current/reference/#order-step) |
| `path` | Renvoie le chemin d’accès complet de la traversée | [étape path](https://tinkerpop.apache.org/docs/current/reference/#path-step) |
| `project` | Projette les propriétés sous forme de carte | [étape project](https://tinkerpop.apache.org/docs/current/reference/#project-step) |
| `properties` | Renvoie les propriétés pour les labels spécifiés | [étape properties](https://tinkerpop.apache.org/docs/current/reference/#properties-step) |
| `range` | Filtre la plage de valeurs spécifiée| [étape range](https://tinkerpop.apache.org/docs/current/reference/#range-step) |
| `repeat` | Répète l’étape le nombre de fois spécifié. Permet d’effectuer des boucles | [répétez l’étape](https://tinkerpop.apache.org/docs/current/reference/#repeat-step) |
| `sample` | Permet d’échantillonner les résultats à partir de la traversée | [étape sample](https://tinkerpop.apache.org/docs/current/reference/#sample-step) |
| `select` | Permet de projeter les résultats à partir de la traversée |  [étape select](https://tinkerpop.apache.org/docs/current/reference/#select-step) | |
| `store` | Pour les agrégations non bloquantes de la traversée | [étape store](https://tinkerpop.apache.org/docs/current/reference/#store-step) |
| `tree` | Chemins d’accès d’agrégation à partir d’un vertex dans une arborescence | [étape tree](https://tinkerpop.apache.org/docs/current/reference/#tree-step) |
| `unfold` | Dérouler un itérateur comme une étape| [étape unfold](https://tinkerpop.apache.org/docs/current/reference/#unfold-step) |
| `union` | Fusionner les résultats à partir de plusieurs traversées| [étape union](https://tinkerpop.apache.org/docs/current/reference/#union-step) |
| `V` | Inclut les étapes nécessaires pour les traversées entre les vertex et les bords `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV` et `otherV` pour | [étapes vertex](https://tinkerpop.apache.org/docs/current/reference/#vertex-steps) |
| `where` | Permet de filtrer les résultats à partir de la traversée. Prend en charge les opérateurs `eq`, `neq`, `lt`, `lte`, `gt`, `gte` et `between`  | [étape where](https://tinkerpop.apache.org/docs/current/reference/#where-step) |

Le moteur optimisé pour l’écriture fourni par Azure Cosmos DB prend en charge l’indexation automatique de toutes les propriétés au sein des vertex et des arêtes par défaut. Par conséquent, les requêtes avec des filtres, les requêtes de plage, le tri ou les agrégations sur toutes les propriétés sont traités à partir de l’index et exécutés efficacement. Pour plus d’informations sur la façon dont fonctionne l’indexation dans Azure Cosmos DB, consultez notre article sur [l’indexation indépendante du schéma](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="next-steps"></a>Étapes suivantes
* Commencez par créer une application de graphe [à l’aide de nos kits SDK](create-graph-dotnet.md) 
* Découvrez plus en détail la [prise en charge des graphiques](graph-introduction.md) dans Azure Cosmos DB.
