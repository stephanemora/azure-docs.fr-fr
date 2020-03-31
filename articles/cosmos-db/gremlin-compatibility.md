---
title: Compatibilité Azure Cosmos DB Gremlin avec les fonctionnalités TinkerPop
description: Documentation de référence sur les problèmes de compatibilité du moteur Graph
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 581bc813ca27067b1f27ab9866a45df3084dbbcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644730"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Compatibilité Azure Cosmos DB Gremlin
Le moteur Azure Cosmos DB Graph suit de près la spécification des étapes de traversée [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps), mais avec quelques différences.

## <a name="behavior-differences"></a>Différences de comportement

* Le moteur Azure Cosmos DB Graph fonctionne ***d’abord en largeur*** de traversée tandis que TinkerPop Gremlin fonctionne d’abord en profondeur. Ce comportement permet d'obtenir de meilleures performances dans un système horizontalement évolutif comme Cosmos DB. 

## <a name="unsupported-features"></a>Fonctionnalités non prises en charge

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** est une spécification pour les traversées de graphe indépendante du langage de programmation. Cosmos DB Graph ne la prend pas encore en charge. Utilisez `GremlinClient.SubmitAsync()` et passez la traversée en tant que chaîne de texte.

* La cardinalité des ensembles ***`property(set, 'xyz', 1)`*** n’est pas prise en charge. Utilisez `property(list, 'xyz', 1)` à la place. Pour plus d’informations, consultez [Vertex properties with TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties) (Propriétés de vertex avec TinkerPop).

* ***`atch()`*** permet d'interroger des graphiques à l'aide d’une correspondance déclarative de modèles. Cette fonctionnalité n’est pas disponible.

* Les ***objets en tant que propriétés*** sur les sommets ou arêtes ne sont pas pris en charge. Les propriétés peuvent uniquement être des types primitifs ou des tableaux.

* Le ***tri par propriétés de tableau*** `order().by(<array property>)` n’est pas pris en charge. Seul est pris en charge le tri par types primitifs.

* Les ***types JSON non primitifs*** ne sont pas pris en charge. Utilisez les types `string`, `number` ou `true`/`false`. Les valeurs `null` ne sont pas prises en charge. 

* Le sérialiseur ***GraphSONv3*** n’est pas pris en charge actuellement. Utilisez les classes de sérialiseur, de lecteur et d’enregistreur `GraphSONv2` dans la configuration de la connexion.

* Les **fonctions et les expressions lambda** ne sont pas prises en charge actuellement. Cela comprend les fonctions `.map{<expression>}`, `.by{<expression>}` et `.filter{<expression>}`. Pour plus d’informations et pour découvrir comment les réécrire à l’aide d’étapes Gremlin, consultez [A Note on Lambdas](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas) (Remarque sur les lambdas).

* Les ***transactions*** ne sont pas prises en charge en raison de la nature distribuée du système.  Configurez un modèle d’accès concurrentiel optimiste approprié sur le compte Gremlin pour « lire vos propres écrits », et utilisez l’accès concurrentiel optimiste pour résoudre les conflits d’écritures.

## <a name="next-steps"></a>Étapes suivantes
* Visitez la page [Cosmos DB User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db) pour partager vos commentaires et aider l'équipe à se concentrer sur les fonctionnalités qui sont importantes pour vous.
