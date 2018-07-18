---
title: Comment interroger des données de graphique dans Azure Cosmos DB ? | Microsoft Docs
description: Apprendre à interroger des données de graphique dans Azure Cosmos DB
services: cosmos-db
author: luisbosquez
manager: kfile
editor: ''
tags: ''
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: ad38976f439c399d839d6c5ee9dcd6ade7726c71
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081905"
---
# <a name="tutorial-query-azure-cosmos-db-graph-api-by-using-gremlin"></a>Tutoriel : Interroger Azure Cosmos DB à l’aide de l’API Graph et de Gremlin

L’[API Graph](graph-introduction.md) d’Azure Cosmos DB prend en charge les requêtes [Gremlin](https://github.com/tinkerpop/gremlin/wiki). Cet article fournit des exemples de documents et de requêtes pour vous aider à démarrer. L’article relatif à la [prise en charge de Gremlin](gremlin-support.md) comporte des informations de référence détaillées sur Gremlin.

Cet article décrit les tâches suivantes : 

> [!div class="checklist"]
> * Interrogation des données avec Gremlin

## <a name="prerequisites"></a>Prérequis

Pour le bon fonctionnement de ces requêtes, vous devez disposer d’un compte Azure Cosmos DB et de données de graphique dans le conteneur. Cela n’est pas le cas ? Lancez le [démarrage rapide de 5 minutes](create-graph-dotnet.md) ou le [tutoriel destiné aux développeurs](tutorial-query-graph.md) pour créer un compte et alimenter votre base de données. Vous pouvez exécuter les requêtes suivantes avec la [console Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) ou avec votre pilote Gremlin favori.

## <a name="count-vertices-in-the-graph"></a>Compter le nombre de vertex du graphique

L’extrait de code suivant montre comment compter le nombre de vertex du graphique :

```
g.V().count()
```

## <a name="filters"></a>Filtres

Vous pouvez appliquer des filtres à l’aide des étapes `has` et `hasLabel` de Gremlin et les associer à l’aide de `and`, `or`, et `not` pour créer des filtres plus complexes. Azure Cosmos DB propose une indexation sans schéma de toutes les propriétés au sein de vos vertex et degrés pour les requêtes rapides :

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>Projection

Vous pouvez projeter certaines propriétés dans les résultats de requête à l’aide de l’étape `values` :

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>Rechercher des vertex et des bords associés

Jusqu’ici, nous avons seulement abordé les opérateurs de requête fonctionnant dans n’importe quelle base de données. Les graphiques sont rapides et efficaces pour les opérations de traversée lorsque vous avez besoin d’accéder aux vertex et bords associés. Recherchons à présent tous les amis de Thomas. Nous suivons pour cela l’étape `outE` de Gremlin pour rechercher toutes les bords externes de Thomas afin de se diriger vers les vertex internes de ces bords en suivant l’étape `inV` de Gremlin :

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

La requête suivante effectue deux sauts pour rechercher tous les « amis des amis de Thomas », en appelant `outE` et `inV` deux fois. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

Vous pouvez créer des requêtes plus complexes et implémenter une logique de traversée de graphique puissante à l’aide de Gremlin, y compris en mixant des expressions de filtre, en exécutant des boucles à l’aide de l’étape `loop` et en mettant en œuvre la navigation conditionnelle à l’aide de l’étape `choose`. En savoir plus sur ce que la [prise en charge de Gremlin](gremlin-support.md) vous permet de faire !

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez :

> [!div class="checklist"]
> * Effectuer des interrogations à l’aide de Graph 

Vous pouvez maintenant passer à la section Concepts pour plus d’informations sur Cosmos DB.

> [!div class="nextstepaction"]
> [Diffusion mondiale](distribute-data-globally.md) 

