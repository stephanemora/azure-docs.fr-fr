---
title: Indexation dans Azure Cosmos DB
description: Comprendre le fonctionnement de l’indexation dans Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: 9e1ae9faecea67e1abb2a1b08d5641bc25c9f38a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469088"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Vue d’ensemble de l’indexation dans Azure Cosmos DB

Azure Cosmos DB est une base de données indépendante des schémas qui vous permet d’itérer rapidement sur votre application sans avoir à vous soucier de la gestion des schémas ou des index. Par défaut, Azure Cosmos DB indexe automatiquement tous les éléments dans votre conteneur sans demander de schéma ni d’index secondaire aux développeurs.

## <a name="items-as-trees"></a>Éléments sous forme d’arborescences

En projetant des éléments dans un conteneur en tant que documents JSON et en les représentant sous forme d’arborescences, Azure Cosmos DB normalise à la fois la structure et les valeurs d’instance entre les éléments en concept unificateur de **structure de chemin encodée dynamiquement**. Dans cette représentation, chaque étiquette d’un document JSON qui inclut les noms des propriétés et leurs valeurs devient un nœud de l’arborescence. Les feuilles de l’arborescence contiennent des valeurs réelles et les nœuds intermédiaires contiennent les informations de schéma. L’image suivante représente les arborescences créées pour deux éléments (1 et 2) dans un conteneur :

![Représentation sous forme d’arborescence de deux éléments différents dans un conteneur Azure Cosmos](./media/index-overview/indexing-as-tree.png)

Un nœud pseudo-racine est créé comme parent pour les nœuds réels correspondant aux étiquettes dans le document en dessous. Les structures de données imbriquées déterminent la hiérarchie dans l’arborescence. Des nœuds artificiels intermédiaires étiquetés avec des valeurs numériques (par exemple, 0, 1,...) sont utilisés pour représenter les énumérations et les index de tableau.

## <a name="index-paths"></a>Chemins d’accès de l’index

Azure Cosmos DB projette les éléments en tant que documents JSON et les index en tant qu’arborescences. Vous pouvez ensuite paramétrer les stratégies pour les chemins dans l’arborescence. Vous pouvez choisir d’inclure ou d’exclure des chemins dans l’indexation. Il peut en résulter de meilleures performances d’écriture et un plus petit stockage des index pour les scénarios où les modèles de requête sont connus à l’avance. Pour plus d’informations, consultez [Chemins des index](index-paths.md).

## <a name="indexing-under-the-hood"></a>Indexation : Sous le capot

La base de données Azure Cosmos applique une indexation automatique aux données, où chaque chemin d’une arborescence est indexé, sauf si votre configuration en exclut certains.

La base de données Azure Cosmos utilise une structure de données d’index inversé afin de stocker les informations de chaque élément et de faciliter une représentation efficace pour l’interrogation. L’arborescence d’index est un document construit en unissant toutes les arborescences qui représentent des éléments individuels dans le conteneur. L’arborescence d’index augmente à mesure que de nouveaux éléments sont ajoutés ou que des éléments existants sont mis à jour dans le conteneur. Contrairement à l’indexation de base de données relationnelle, Azure Cosmos DB ne redémarre pas l’indexation à partir de zéro quand de nouveaux champs sont introduits ou quand de nouveaux éléments sont ajoutés à la structure existante. 

Chaque nœud de l’arborescence d’index est une entrée d’index contenant les valeurs d’étiquette et de position, appelées terme, et les ID d’élément, appelés nominations. Les nominations dans les accolades (par exemple, {1,2}) dans la figure de l’index inversé correspondent aux éléments tels que Document1 et Document2 contenant la valeur d’étiquette donnée. Le fait de traiter les étiquettes de schéma et les valeurs d’instance de la même manière implique la compression de tous les éléments dans un grand index, ce qu’il est important de signaler. Une valeur d’instance qui se trouve toujours dans les feuilles n’est pas répétée ; elle peut figurer dans différents rôles entre les éléments, comporter des étiquettes de schéma différentes, mais il s’agit de la même valeur. L’image suivante montre l’indexation inversée pour différents éléments :

![Indexation vue de l’intérieur, index inversé](./media/index-overview/inverted-index.png)

> [!NOTE]
> L’index inversé peut sembler similaire aux structures d’indexation utilisées dans un moteur de recherche dans le domaine de la récupération d’informations. Avec cette méthode, Azure Cosmos DB vous permet de rechercher dans votre base de données n’importe quel élément, quelle que soit sa structure de schéma.

Pour le chemin normalisé, l’index encode la voie directe d’un bout à l’autre, de la racine à la valeur, avec les informations de type de la valeur. Le chemin et la valeur sont encodés pour fournir différentes formes d’indexation comme une plage ou des types spatiaux. L’encodage de la valeur est conçu pour fournir une valeur unique ou une composition d’un ensemble de chemins.

## <a name="querying-with-indexes"></a>Interrogation avec des index

L’index inversé permet à une requête d’identifier les documents qui correspondent au prédicat de requête rapidement. En traitant les valeurs d’instance et de schéma de la même manière en termes de chemins, l’index inversé est également une arborescence. Par conséquent, l’index et les résultats peuvent être sérialisés dans un document JSON valide et retournés comme documents eux-mêmes, car ils sont retournés dans la représentation sous forme d’arborescence. Cette méthode permet de traiter de manière récursive les résultats pour des requêtes supplémentaires. L’image suivante illustre un exemple d’indexation dans une requête de pointage :  

![Exemple de requête de pointage](./media/index-overview/index-point-query.png)

Pour une requête de plage, GermanTax est une fonction définie par l’utilisateur exécutée dans le cadre du traitement des requêtes. La fonction définie par l’utilisateur est toute fonction Javascript inscrite qui peut fournir une logique de programmation riche intégrée dans la requête. L’image suivante illustre un exemple d’indexation dans une requête de plage :

![Exemple de requête de plage](./media/index-overview/index-range-query.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail l’indexation dans les articles suivants :

- [Stratégie d’indexation](index-policy.md)
- [Types d’index](index-types.md)
- [Chemins des index](index-paths.md)
- [Guide pratique pour gérer la stratégie d’indexation](how-to-manage-indexing-policy.md)
