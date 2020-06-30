---
title: Introduction à la prise en charge des notebooks Jupyter intégrés dans Azure Cosmos DB (préversion)
description: Découvrez comment vous pouvez utiliser la prise en charge des notebooks Jupyter intégrés dans Azure Cosmos DB pour exécuter des requêtes de manière interactive.
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/19/2020
author: deborahc
ms.author: dech
ms.openlocfilehash: 53725e7d4c39405e7ba47f8915e7444ce6a2167a
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2020
ms.locfileid: "85118447"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Prise en charge des notebooks Jupyter intégrés dans Azure Cosmos DB (préversion)

Jupyter Notebook est une application web open source qui vous permet de créer et de partager des documents contenant du code en direct, des équations, des visualisations et du texte narratif. 

Les notebooks Jupyter intégrés à Azure Cosmos DB sont directement intégrés au portail Azure et vos comptes Azure Cosmos DB, ce qui les rend pratiques et faciles à utiliser. Les développeurs, les scientifiques des données, les ingénieurs et les analystes peuvent utiliser les notebooks Jupyter pour effectuer une exploration des données, un nettoyage des données, des transformations de données, des simulations numériques, une modélisation statistique, une visualisation des données et du Machine Learning.

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png" alt-text="Virtualisations de notebooks Jupyter dans Azure Cosmos DB":::

Azure Cosmos DB prend en charge les notebooks C# et Python pour toutes les API, y compris Core (SQL), Cassandra, Gremlin, Table et API pour MongoDB. À l’intérieur du notebook, vous pouvez tirer parti des commandes et fonctionnalités intégrées qui facilitent la création de ressources Azure Cosmos DB, le chargement de données, ainsi que la consultation et la visualisation de vos données dans Azure Cosmos DB. 

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png" alt-text="Prise en charge des notebooks Jupyter dans Azure Cosmos DB":::

## <a name="benefits-of-jupyter-notebooks"></a>Avantages des notebooks Jupyter

À l’origine, les notebooks Jupyter ont été développés pour les applications de science des données écrites en Python et R. Toutefois, ils peuvent être utilisés de différentes façons pour différents types de projets, notamment :

**Visualisation des données :** Les notebooks Jupyter vous permettent de visualiser des données sous la forme d’un notebook partagé qui restitue un jeu de données sous forme graphique. Vous pouvez créer des visualisations, apporter des modifications interactives au code partagé et au jeu de données, et partager les résultats.

**Partage du code :** Les services comme GitHub fournissent des méthodes pour partager du code, mais ils sont en grande partie non interactifs. Avec un notebook Jupyter, vous pouvez voir le code, l’exécuter, puis afficher les résultats directement dans le portail Azure.

**Interactions en direct avec le code :** Le code d’un notebook Jupyter est dynamique : vous pouvez le modifier et exécuter en temps réel des mises à jour de façon incrémentielle. Vous pouvez également incorporer des contrôles utilisateur (par exemple, des curseurs ou des champs d’entrée de texte) à utiliser comme sources d’entrée pour le code, comme démonstrations ou comme preuves de concepts (POC).

**Documentation des exemples de code et des résultats de l’exploration des données :** Si vous avez un extrait de code et souhaitez expliquer ligne par ligne son fonctionnement, vous pouvez l’incorporer dans un notebook Jupyter. Vous pouvez ajouter l’interactivité et la documentation en même temps.

**Commandes intégrées pour Azure Cosmos DB :** Les commandes magiques intégrées à Azure Cosmos DB facilitent l’interaction avec votre compte. Vous pouvez utiliser des commandes comme %%upload et %%sql pour charger des données dans un conteneur et les interroger à l’aide de la [syntaxe d’API SQL](sql-query-getting-started.md). Vous n’avez pas besoin d’écrire du code personnalisé supplémentaire.

**Environnement « tout dans un emplacement unique » :** Les notebooks Jupyter combinent du code, du texte enrichi, des images, des vidéos, des animations, des équations mathématiques, des tracés, des cartes, des figures interactives, des widgets et des interfaces graphiques utilisateur dans un document unique.

## <a name="components-of-a-jupyter-notebook"></a>Composants d’un notebook Jupyter

Les notebooks Jupyter peuvent inclure plusieurs types de composants, chacun étant organisé en blocs discrets ou cellules :

**Texte et code HTML :** il est possible d’insérer à tout moment dans le document du texte brut ou du texte annoté dans la syntaxe Markdown afin de générer du code HTML. Le style CSS peut également être inclus inline ou ajouté au modèle utilisé pour générer le notebook.

**Code et sortie :** les notebooks Jupyter prennent en charge le code Python et C#. Les résultats du code exécuté s’affichent immédiatement après les blocs de code. De plus, les blocs de code peuvent être exécutés plusieurs fois dans l’ordre de votre choix.

**Visualisations :** vous pouvez générer le graphisme et les graphiques à partir du code à l’aide de modules tels que Matplotlib, Plotly ou Bokeh, entre autres. Comme pour la sortie, ces visualisations s’affichent inline en regard du code qui les génère. Comme pour la sortie, ces visualisations s’affichent inline en regard du code qui les génère.

**Multimédia :** étant donné que les notebooks Jupyter sont basés sur la technologie web, ils peuvent afficher tous les types de contenu multimédia pris en charge par une page web. Vous pouvez les inclure dans un notebook en tant qu’éléments HTML, ou vous pouvez les générer programmatiquement à l’aide du module `IPython.display`.

**Données :** vous pouvez importer les données des conteneurs Azure Cosmos et les résultats des requêtes programmatiquement dans un notebook Jupyter. Utilisez les commandes magiques intégrées pour charger ou interroger des données dans Azure Cosmos DB. 

## <a name="next-steps"></a>Étapes suivantes

Pour bien démarrer avec les notebooks Jupyter intégrés dans Azure Cosmos DB, consultez les articles suivants :

* [Activer les notebooks dans un compte Azure Cosmos](enable-notebooks.md)
* [Utiliser les fonctionnalités et commandes des notebooks Python](use-python-notebook-features-and-commands.md)
* [Utiliser les fonctionnalités et commandes des notebooks C#](use-csharp-notebook-features-and-commands.md)