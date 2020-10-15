---
title: Compatibilité Azure Cosmos DB Gremlin avec les fonctionnalités TinkerPop
description: Documentation de référence sur les problèmes de compatibilité du moteur Graph
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 7257246f618e3028534f3ebd60eaf6f94a3a4720
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092505"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Compatibilité Azure Cosmos DB Gremlin
Le moteur Azure Cosmos DB Graph suit de près la spécification des étapes de traversée [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps), mais avec quelques différences au niveau de l’implémentation, spécifiques à Azure Cosmos DB. Pour connaître la liste des étapes Gremlin prises en charge, consultez l’article [Prise en charge du protocole câble d’API Gremlin](gremlin-support.md).

## <a name="behavior-differences"></a>Différences de comportement

* Le moteur Azure Cosmos DB Graph fonctionne ***d’abord en largeur*** de traversée tandis que TinkerPop Gremlin fonctionne d’abord en profondeur. Ce comportement permet d'obtenir de meilleures performances dans un système horizontalement évolutif comme Cosmos DB. 

## <a name="unsupported-features"></a>Fonctionnalités non prises en charge

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** est une spécification pour les traversées de graphe indépendante du langage de programmation. Cosmos DB Graph ne la prend pas encore en charge. Utilisez `GremlinClient.SubmitAsync()` et passez la traversée en tant que chaîne de texte.

* La cardinalité des ensembles ***`property(set, 'xyz', 1)`*** n’est pas prise en charge. Utilisez `property(list, 'xyz', 1)` à la place. Pour plus d’informations, consultez [Vertex properties with TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties) (Propriétés de vertex avec TinkerPop).

* L’***étape `match()`*** n’est pas disponible actuellement. Cette étape fournit des fonctions d’interrogation déclarative.

* Les ***objets en tant que propriétés*** sur les sommets ou arêtes ne sont pas pris en charge. Les propriétés peuvent uniquement être des types primitifs ou des tableaux.

* Le ***tri par propriétés de tableau*** `order().by(<array property>)` n’est pas pris en charge. Seul est pris en charge le tri par types primitifs.

* Les ***types JSON non primitifs*** ne sont pas pris en charge. Utilisez les types `string`, `number` ou `true`/`false`. Les valeurs `null` ne sont pas prises en charge. 

* Le sérialiseur ***GraphSONv3*** n’est pas pris en charge actuellement. Utilisez les classes de sérialiseur, de lecteur et d’enregistreur `GraphSONv2` dans la configuration de la connexion. Les résultats renvoyés par l'API Azure Cosmos DB Gremlin ne sont pas au format GraphSON. 

* Les **fonctions et les expressions lambda** ne sont pas prises en charge actuellement. Cela comprend les fonctions `.map{<expression>}`, `.by{<expression>}` et `.filter{<expression>}`. Pour plus d’informations et pour découvrir comment les réécrire à l’aide d’étapes Gremlin, consultez [A Note on Lambdas](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas) (Remarque sur les lambdas).

* Les ***transactions*** ne sont pas prises en charge en raison de la nature distribuée du système.  Configurez un modèle d’accès concurrentiel optimiste approprié sur le compte Gremlin pour « lire vos propres écrits », et utilisez l’accès concurrentiel optimiste pour résoudre les conflits d’écritures.

## <a name="known-limitations"></a>Limitations connues

* **Utilisation de l’index pour les requêtes Gremlin avec des étapes `.V()` mi-parcours** : Actuellement, seul le premier appel `.V()` d’une traversée utilisera l’index pour résoudre les filtres ou prédicats qui lui sont associés. Les appels suivants ne consultent pas l’index, ce qui peut augmenter la latence et le coût de la requête.
    
    Dans l’hypothèse de l’indexation par défaut, une requête Gremlin de lecture classique commençant par l’étape `.V()` utiliserait des paramètres dans les étapes de filtrage associées, tels que `.has()` ou `.where()`, pour optimiser le coût et les performances de la requête. Par exemple :

    ```java
    g.V().has('category', 'A')
    ```

    Toutefois, lorsque plusieurs étapes `.V()` sont incluses dans la requête Gremlin, la résolution des données pour la requête peut ne pas être optimale. Utilisez la requête suivante en guise d’exemple :

    ```java
    g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
    ```

    Cette requête retourne deux groupes de sommets en fonction de leur propriété appelée `category`. Dans ce cas, seul le premier appel, `g.V().has('category', 'A')` utilise l’index pour résoudre les sommets en fonction des valeurs de leurs propriétés.

    Une solution de contournement pour cette requête consiste à utiliser des étapes de sous-parcours, telles que `.map()` et `union()`. En voici quelques exemples :

    ```java
    // Query workaround using .map()
    g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

    // Query workaround using .union()
    g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
    ```

    Vous pouvez examiner les performances des requêtes à l’aide de l’[étape Gremlin `executionProfile()`](graph-execution-profile.md).

## <a name="next-steps"></a>Étapes suivantes
* Visitez la page [Cosmos DB User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db) pour partager vos commentaires et aider l'équipe à se concentrer sur les fonctionnalités qui sont importantes pour vous.
