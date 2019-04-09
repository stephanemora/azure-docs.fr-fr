---
title: Indexation dans Azure Cosmos DB
description: Comprendre le fonctionnement de l’indexation dans Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: ecf53251020ce1b639a5bf8da65f5d31ff699db9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59265693"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Vue d’ensemble de l’indexation dans Azure Cosmos DB

Azure Cosmos DB est une base de données indépendante des schémas qui vous permet d’itérer rapidement sur votre application sans avoir à vous soucier de la gestion des schémas ou des index. Par défaut, Azure Cosmos DB indexe automatiquement tous les éléments dans votre conteneur sans demander de schéma ni d’index secondaire aux développeurs.

## <a name="items-as-trees"></a>Éléments sous forme d’arborescences

En projetant les éléments dans un conteneur en tant que documents JSON et qui les représente sous forme d’arborescences, Azure Cosmos DB normalise la structure et les valeurs d’instance entre les éléments dans le concept unificateur d’un **dynamiquement encodé structure de chemin d’accès** . Dans cette représentation, chaque contrôle label dans un document JSON, qui inclut les noms de propriétés et leurs valeurs, devient un nœud de l’arborescence. Les feuilles de l’arborescence contient les valeurs réelles et les nœuds intermédiaires contiennent les informations de schéma. L’illustration suivante représente les arborescences créés pour deux éléments (1 et 2) dans un conteneur Azure Cosmos :

![Représentation sous forme d’arborescence de deux éléments différents dans un conteneur Azure Cosmos](./media/index-overview/indexing-as-tree.png)

Un nœud racine de pseudo est créé en tant que parent pour les nœuds eux-mêmes correspondant aux étiquettes dans le document JSON en dessous. Les structures de données imbriquées déterminent la hiérarchie dans l’arborescence. Des nœuds artificiels intermédiaires étiquetés avec des valeurs numériques (par exemple, 0, 1,...) sont utilisés pour représenter les énumérations et les index de tableau.

## <a name="index-paths"></a>Chemins d’accès de l’index

Azure Cosmos DB projette les éléments dans un conteneur Azure Cosmos en tant que documents JSON et index sous forme d’arborescences. Vous pouvez ensuite paramétrer les stratégies d’index pour les chemins d’accès dans l’arborescence. Vous pouvez choisir d’inclure ou d’exclure des chemins dans l’indexation. Il peut en résulter de meilleures performances d’écriture et un plus petit stockage des index pour les scénarios où les modèles de requête sont connus à l’avance. Pour plus d’informations, consultez [chemins d’Index](index-paths.md).

## <a name="indexing-under-the-hood"></a>Indexation : Sous le capot

Base de données Azure Cosmos s’applique *l’indexation automatique* aux données, où chaque chemin d’accès dans une arborescence est indexé, sauf si vous configurez pour exclure certains chemins d’accès.

La base de données Azure Cosmos utilise une structure de données d’index inversé afin de stocker les informations de chaque élément et de faciliter une représentation efficace pour l’interrogation. L’arborescence d’index est un document qui est construit avec l’union de toutes les arborescences qui représentent les éléments individuels dans un conteneur. L’arborescence d’index augmente au fil du temps, comme les nouveaux éléments sont ajoutés ou des éléments existants sont mis à jour dans le conteneur. Contrairement à l’indexation de base de données relationnelle, Azure Cosmos DB ne redémarre pas l’indexation à partir de zéro, comme les nouveaux champs sont introduits. Nouveaux éléments sont ajoutés à la structure d’index existante. 

Chaque nœud de l’arborescence d’index est une entrée d’index contenant les valeurs d’étiquette et la position, appelés le *terme*et les ID d’éléments, appelés les *validations*. Les validations dans les accolades (par exemple {1,2}) dans la figure de l’index inversé correspondent aux éléments tels que *Document1* et *Document2* contenant la valeur de l’étiquette donnée. Le fait de traiter les étiquettes de schéma et les valeurs d’instance uniformément est que tout est compressé dans un index de grande taille. Une valeur d’instance qui se trouve toujours dans les feuilles n’est pas répétée ; elle peut figurer dans différents rôles entre les éléments, comporter des étiquettes de schéma différentes, mais il s’agit de la même valeur. L’illustration suivante montre l’indexation inversé pour deux éléments différents :

![Indexation vue de l’intérieur, index inversé](./media/index-overview/inverted-index.png)

> [!NOTE]
> L’index inversé peut sembler similaire aux structures d’indexation utilisées dans un moteur de recherche dans le domaine de la récupération d’informations. Avec cette méthode, Azure Cosmos DB vous permet de rechercher dans votre base de données n’importe quel élément, quelle que soit sa structure de schéma.

Pour le chemin normalisé, l’index encode la voie directe d’un bout à l’autre, de la racine à la valeur, avec les informations de type de la valeur. Le chemin d’accès et la valeur sont codées pour fournir différents types d’indexation (plage, spatiaux, etc.). L’encodage de la valeur est conçu pour fournir une valeur unique ou une composition d’un ensemble de chemins.

## <a name="querying-with-indexes"></a>Interrogation avec des index

L’index inversé permet à une requête d’identifier les documents qui correspondent au prédicat de requête rapidement. En traitant le schéma et les valeurs d’instance uniformément en termes de chemins d’accès, l’index inversé est également une arborescence. Par conséquent, l’index et les résultats peuvent être sérialisés dans un document JSON valide et retournés comme documents eux-mêmes, car ils sont retournés dans la représentation sous forme d’arborescence. Cette méthode permet de traiter de manière récursive les résultats pour des requêtes supplémentaires. L’image suivante illustre un exemple d’indexation dans une requête de pointage :  

![Exemple de requête de pointage](./media/index-overview/index-point-query.png)

Pour une requête de plage, *GermanTax* est un [fonction définie par l’utilisateur](stored-procedures-triggers-udfs.md#udfs) exécuté en tant que partie du traitement des requêtes. La fonction définie par l’utilisateur est toute fonction JavaScript inscrite, qui peut fournir une logique de programmation riche intégrée dans la requête. L’image suivante illustre un exemple d’indexation dans une requête de plage :

![Exemple de requête de plage](./media/index-overview/index-range-query.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail l’indexation dans les articles suivants :

- [Stratégie d’indexation](index-policy.md)
- [Types d’index](index-types.md)
- [Chemins d’accès de l’index](index-paths.md)
- [Comment gérer la stratégie d’indexation](how-to-manage-indexing-policy.md)
