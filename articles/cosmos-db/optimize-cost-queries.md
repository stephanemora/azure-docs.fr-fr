---
title: Optimiser les coûts et les unités de requête pour exécuter des requêtes dans Azure Cosmos DB
description: Découvrez comment évaluer les frais liés aux unités d’une requête, et optimiser cette dernière en termes de performances et de coût.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: dd75ad4ed1024292868f113e474fe8b8b73679b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445130"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Optimiser le coût de requête dans Azure Cosmos DB

Azure Cosmos DB propose un ensemble complet d’opérations de base de données, notamment des requêtes relationnelles et hiérarchiques qui opèrent sur les éléments au sein d’un conteneur. Le coût associé à chacune de ces opérations varie en fonction du processeur, des E/S et de la mémoire nécessaires à l’exécution de l’opération. Plutôt que de vous soucier de la gestion des ressources matérielles, vous pouvez considérer une unité de requête comme une mesure unique des ressources nécessaires à l’exécution des opérations de base de données pour répondre à une requête. Cet article décrit comment évaluer les frais liés aux unités d’une requête, et optimiser cette dernière en termes de performances et de coût. 

Les requêtes dans Azure Cosmos DB sont généralement triées de la plus rapide/efficace à la plus lente/moins efficace en prenant en compte le débit. Ce tri s’effectue comme suit :  

* Opération GET sur une clé de partition unique et une clé d’élément.

* Requête avec une clause de filtre au sein d’une clé de partition unique.

* Requête sans clause de filtre d’égalité ou de plage sur une propriété.

* Requête sans filtre.

Les requêtes qui lisent les données à partir d’une ou plusieurs partitions affichent une latence plus élevée et consomment un nombre plus élevé d’unités de requête. Étant donné que chaque partition dispose de l’indexation automatique sur toutes les propriétés, la requête peut être servie efficacement à partir de l’index. Vous pouvez effectuer des requêtes qui utilisent plusieurs partitions plus rapidement à l’aide des options de parallélisme. Pour en savoir plus sur le partitionnement et les clés de partition, consultez [Partitionnement dans Azure Cosmos DB](partitioning-overview.md).

## <a name="evaluate-request-unit-charge-for-a-query"></a>Évaluer les frais liés aux unités d’une requête

Une fois que vous avez stocké des données dans vos conteneurs Azure Cosmos, vous pouvez utiliser l’Explorateur de données du portail Azure pour construire et exécuter vos requêtes. Vous pouvez également obtenir le coût des requêtes à l’aide de l’Explorateur de données. Cette méthode vous donnera une idée des frais réels que coûteront les requêtes et les opérations courantes prises en charge par votre système.

Vous pouvez également obtenir le coût des requêtes de manière programmatique en utilisant les kits de développement logiciel. Pour mesurer les frais généraux d’une opération telle que créer, mettre à jour ou supprimer, examinez l’en-tête `x-ms-request-charge` lorsque vous utilisez l’API REST. Si vous utilisez le Kit de développement logiciel (SDK) .NET ou Java, la propriété `RequestCharge` est la propriété équivalente pour obtenir les frais liés à la requête. Cette propriété est présente dans ResourceResponse ou FeedResponse.

```csharp
// Measure the performance (request units) of writes 
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument); 

Console.WriteLine("Insert of an item consumed {0} request units", response.RequestCharge); 

// Measure the performance (request units) of queries 
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery(); 

while (queryable.HasMoreResults) 
     { 
          FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
          Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge); 
     }
```

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Facteurs influant les frais liés aux unités d’une requête

Les unités d’une requête dépendent de plusieurs facteurs. Par exemple, le nombre d’éléments Azure Cosmos chargés/renvoyés, le nombre de recherches basées sur l’index, les détails sur la durée de compilation de la requête, etc. Azure Cosmos DB veille à ce que la même requête, lorsqu’elle est exécutée sur les mêmes données, coûte toujours le même nombre d’unités de requête, même lorsque les exécutions se répètent. Le profil de requête basé sur les métriques d’exécution vous donne une idée assez précise de la façon dont les unités de requête sont utilisées.  

Dans certains cas, vous pouvez voir une séquence de 200 et 429 réponses, ainsi que des unités de requête variables dans une exécution de requête paginée. En effet, les requêtes s’exécutent aussi rapidement que possible selon les unités de requête disponibles. Une exécution de requête peut être répartie entre plusieurs pages/allers-retours serveur et client. Par exemple, 10 000 éléments peuvent être renvoyés sur plusieurs pages. Chacune de ces pages est facturée en fonction du calcul effectué pour cette page. Lorsque vous calculez la somme de toutes ces pages, vous devez obtenir le même nombre d’unités de requête que vous obtiendriez pour toute la requête.  

## <a name="metrics-for-troubleshooting"></a>Métriques pour la résolution de problèmes

Les performances et le débit consommés par les requêtes ainsi que les fonctions définies par l’utilisateur (UDF) dépendent principalement du corps de la fonction. Pour déterminer facilement le temps d’exécution de la requête dans les UDF et le nombre d’unités de requête consommées, vous pouvez activer les métriques de requête. Si vous utilisez le SDK .net, voici quelques exemples de métriques de requête qu’il renvoie :

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

## <a name="best-practices-to-cost-optimize-queries"></a>Meilleures pratiques pour optimiser le coût des requêtes 

Gardez à l’esprit les meilleures pratiques suivantes lors de l’optimisation du coût des requêtes :

* **Rassembler plusieurs types d’entités**

   Essayez de rassembler plusieurs types d’entités au sein d’un conteneur unique ou d’un petit nombre de conteneurs. Cette méthode génère des avantages non seulement d’un point de vue tarifaire, mais également pour les transactions et l’exécution des requêtes. La portée des requêtes se limite à un seul conteneur. Par ailleurs, les transactions atomiques sur plusieurs enregistrements via des procédures stockées/déclencheurs sont limitées à une clé de partition dans un même conteneur. Le regroupement des entités dans le même conteneur peut réduire le nombre d’allers-retours réseau destinés à résoudre les relations entre les enregistrements. Par conséquent, les performances de bout en bout sont augmentées, les transactions atomiques sur plusieurs enregistrements pour un jeu de données plus volumineux sont possibles, et les coûts sont ainsi réduits. Si le regroupement de plusieurs types d’entités au sein d’un conteneur ou d’un groupe réduit de conteneurs est difficile pour votre scénario (c’est généralement le cas si vous migrez une application existante et ne souhaitez pas apporter des modifications de code), envisagez le provisionnement du débit au niveau de la base de données.  

* **Mesure et réglage pour réduire l’utilisation d’unités de requête par seconde**

   La complexité d’une requête a un impact sur le nombre d’unités de requête (RU) consommées pour une opération. Le nombre de prédicats, la nature des prédicats, le nombre de fonctions définies par l’utilisateur et la taille du jeu de données sources. Tous ces facteurs ont une influence sur le coût des opérations d’une requête. 

   Les frais renvoyés dans l’en-tête de requête indiquent le coût d’une requête donnée. Par exemple, si une requête renvoie 1 000 éléments de 1 Ko, le coût de l’opération est 1 000. Par conséquent, en une seconde, le serveur honore uniquement deux requêtes avant de limiter le taux de requêtes suivantes. Pour plus d’informations, accédez à l’article [Unités de requête](request-units.md) et à la calculatrice d’unités de requête. 

## <a name="next-steps"></a>Étapes suivantes

Pour continuer à développer vos connaissances sur l’optimisation des coûts dans Azure Cosmos DB, consultez les articles suivants :

* En savoir plus sur la [manière dont fonctionne la tarification dans Azure Cosmos DB](how-pricing-works.md)
* En savoir plus sur l’[optimisation pour le développement et le test](optimize-dev-test.md)
* En savoir plus sur les [factures Azure Cosmos DB](understand-your-bill.md)
* En savoir plus sur l’[optimisation du coût du débit](optimize-cost-throughput.md)
* En savoir plus sur l’[optimisation du coût de stockage](optimize-cost-storage.md)
* En savoir plus sur l’[optimisation du coût des lectures et écritures](optimize-cost-reads-writes.md)
* En savoir plus sur l’[optimisation du coût des comptes Azure Cosmos sur plusieurs régions](optimize-cost-regions.md)
* En savoir plus sur la [capacité de réserve Azure Cosmos DB](cosmos-db-reserved-capacity.md)

