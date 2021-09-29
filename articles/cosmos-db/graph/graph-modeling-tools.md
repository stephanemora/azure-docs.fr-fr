---
title: Outils de modélisation de données tiers pour les données de graphe Azure Cosmos DB
description: Cet article décrit différents outils permettant de concevoir le modèle de données Graph.
author: mansha
ms.author: mansha
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/25/2021
ms.reviewer: sngun
ms.openlocfilehash: 224465fe282b086c2158f4d81ee67e5bf7cedc09
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124771780"
---
# <a name="third-party-data-modeling-tools-for-azure-cosmos-db-graph-data"></a>Outils de modélisation de données tiers pour les données de graphe Azure Cosmos DB

[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

S’il est important de concevoir un modèle de données, il est encore plus important de le gérer. Voici un ensemble d’outils de conception visuelle tiers permettant de concevoir et de gérer le modèle de données de graphe.

> [!IMPORTANT] 
> Les solutions mentionnées dans cet article sont fournies à titre d’information uniquement et appartiennent à leurs propriétaires respectifs. Nous recommandons aux utilisateurs d’effectuer une évaluation minutieuse, puis de sélectionner celles qui leur conviennent le mieux.

## <a name="hackolade"></a>Hackolade

Hackolade est un outil de modélisation de données et de conception de schéma pour bases de données NoSQL. Il offre un studio de modélisation de données, qui simplifie la gestion des schémas pour les données au repos et les données en mouvement.

### <a name="how-it-works"></a>Fonctionnement
Cet outil assure la modélisation des données avec des sommets/arêtes et leurs propriétés respectives.  Il prend en charge plusieurs cas d’usage, par exemple :
-   Vous partez d’une page vierge et envisagez différentes options pour créer graphiquement votre modèle Cosmos DB Gremlin.  Vous procédez ensuite à la pro-ingénierie du modèle en fonction de votre instance Azure pour évaluer le résultat, puis vous poursuivez l’évolution.  Vous bénéficiez de tous ces avantages sans écrire une seule ligne de code.
-   Vous effectuez l’ingénierie à rebours d’un graphe existant sur Azure pour comprendre clairement sa structure et pouvoir ainsi interroger efficacement votre graphe.  Vous enrichissez ensuite le modèle de données avec des descriptions, des métadonnées et des contraintes pour produire une documentation. L’outil prend en charge les formats HTML, Markdown et PDF et peut alimenter les systèmes de dictionnaires ou de gouvernance des données d’entreprise.
-   Migration d’une base de données relationnelle vers NoSQL à l’aide de la dénormalisation des structures de données.
-   Intégration à un pipeline CI/CD à l’aide d’une interface de ligne de commande.
-   Collaboration et versioning avec Git.
-   Et bien plus encore…

### <a name="sample"></a>Exemple

L’animation de la Figure 2 illustre l’ingénierie à rebours et l’extraction d’entités à partir d’un SGBDR. Hackolade découvre ensuite des relations à partir des relations de clés étrangères, puis les modifications.

Vous trouverez [ici](https://github.com/Azure-Samples/northwind-ddl-sample/blob/main/nw.sql) un exemple de DDL pour SQL Server comme source.   


:::image type="content" source="./media/graph-modeling-tools/hackolade-screenshot.jpg" alt-text="Graphe":::
**Figure 1 :** graphe (extraction du modèle de données de graphe)

Après la modification du modèle de données, l’outil peut générer le script Gremlin, qui peut inclure un script d’index Cosmos DB personnalisé pour assurer la création d’index optimaux. Reportez-vous à la Figure 2 pour voir le flux complet.

L’image suivante illustre l’ingénierie à rebours à partir d’un SGBDR et Hackolade en action : :::image type="content" source="./media/graph-modeling-tools/cosmos-db-gremlin-hackolade.gif" alt-text="Hackolade en action":::

**Figure 2 :** Hackolade en action (illustration de la conversion d’un modèle de données SQL en Gremlin)
### <a name="useful-links"></a>Liens utiles 
-   [Télécharger une version d’essai gratuite valable 14 jours](https://hackolade.com/download.html)
-   [Planifier une démonstration](https://c.x.ai/pdesmarets)
-  [Obtenir plus de modèles de données](https://hackolade.com/samplemodels.html#cosmosdb)
-  [Documentation Hackolade](https://hackolade.com/help/CosmosDBGremlin.html)

## <a name="next-steps"></a>Étapes suivantes
- [Visualisation des données](./graph-visualization-partners.md)