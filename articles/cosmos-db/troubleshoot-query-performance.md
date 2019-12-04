---
title: Diagnostiquer et résoudre des problèmes de requête lors de l’utilisation d’Azure Cosmos DB
description: Apprenez à identifier, diagnostiquer et résoudre les problèmes de requête SQL Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: fd8e80c7cd7cb71e4e0418d970cf2f328f1a3d79
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184713"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Résoudre les problèmes de performance des requêtes pour Azure Cosmos DB
Cet article explique comment identifier, diagnostiquer et résoudre les problèmes de requête SQL Azure Cosmos DB. Afin d'obtenir des performances optimales pour les requêtes Azure Cosmos DB, suivez les étapes de dépannage ci-dessous. 

## <a name="collocate-clients-in-same-azure-region"></a>Colocaliser des clients dans la même région Azure 
Pour obtenir la latence la plus faible possible, l’application appelante doit être située dans la même région Azure que le point de terminaison Azure Cosmos DB configuré. Pour obtenir la liste des régions disponibles, consultez l’article [Régions Azure](https://azure.microsoft.com/global-infrastructure/regions/#services).

## <a name="check-consistency-level"></a>Vérifier le niveau de cohérence
Le [niveau de cohérence](consistency-levels.md) peut avoir un impact sur les performances et les frais. Assurez-vous que votre niveau de cohérence est adapté au scénario donné. Pour en savoir plus, consultez [Choisir le niveau de cohérence](consistency-levels-choosing.md).

## <a name="log-the-executed-sql-query"></a>Journaliser la requête SQL exécutée 

Vous pouvez journaliser la requête SQL exécutée dans un compte de stockage ou dans la table du journal de diagnostics. Les [journaux de requête SQL via des journaux de diagnostics](monitor-cosmos-db.md#diagnostic-settings) vous permettent de journaliser la requête obfusquée dans le compte de stockage de votre choix. Cela vous permet d’examiner les journaux et de trouver la requête qui utilise le plus grand nombre d’unités de requête. Plus tard, vous pouvez utiliser l’ID d’activité pour faire correspondre la requête réelle dans QueryRuntimeStatistics. La requête est obfusquée pour des raisons de sécurité et les noms des paramètres de requête et leurs valeurs dans les clauses WHERE sont différents des noms et valeurs réels. Vous pouvez utiliser la journalisation dans le compte de stockage pour garder la conservation à long terme des requêtes exécutées.  

## <a name="log-query-metrics"></a>Métriques de requête de journal

Utilisez `QueryMetrics` pour corriger les requêtes lentes ou coûteuses. 

  * Définissez `FeedOptions.PopulateQueryMetrics = true` pour inclure `QueryMetrics` dans la réponse.
  * La classe `QueryMetrics` comporte une fonction `.ToString()` surchargée qui peut être invoquée pour obtenir la représentation sous forme de chaîne de `QueryMetrics`. 
  * Les métriques peuvent être utilisées, entre autres, pour obtenir les informations suivantes : 
  
      * si l’exécution d’un composant spécifique du pipeline de requête a pris un temps anormalement long (de l'ordre de quelques centaines de millisecondes ou plus). 

          * Observez `TotalExecutionTime`.
          * Si la valeur `TotalExecutionTime` de la requête est inférieure au temps d'exécution de bout en bout, alors le temps est passé côté client ou sur le réseau. Vérifiez que le client et la région Azure sont colocalisés.
      
      * En cas de faux positifs dans les documents analysés (si le nombre de documents en sortie est beaucoup moins élevé que le nombre de documents récupérés).  

          * Observez `Index Utilization`.
          * `Index Utilization` = (Nombre de documents retournés / Nombre de documents chargés)
          * Si le nombre de documents retournés est très inférieur au nombre de documents chargés, les faux positifs sont analysés.
          * Limitez le nombre de documents récupérés à l'aide de filtres plus spécifiques.  

      * La façon dont les allers-retours individuels sont facturés (voir la valeur `Partition Execution Timeline` dans la représentation sous forme de chaîne de `QueryMetrics`). 
      * Si la requête a entraîné des frais de demande élevés. 

Pour plus de détails, lisez l’article [How to get SQL query execution metrics](profile-sql-api-query.md) (Comment obtenir les métriques d'exécution de requêtes SQL).
      
## <a name="tune-query-feed-options-parameters"></a>Optimiser les requêtes via les paramètres Options de flux 
Les performances des requêtes peuvent être optimisées via les paramètres [Options de flux](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet) des requêtes. Essayez de définir les options ci-dessous :

  * Définissez d’abord `MaxDegreeOfParallelism` sur -1, puis comparez les performances avec différentes valeurs. 
  * Définissez d’abord `MaxBufferedItemCount` sur -1, puis comparez les performances avec différentes valeurs. 
  * Définissez `MaxItemCount` sur -1.

Lorsque vous comparez les performances de différentes valeurs, essayez des valeurs telles que 2, 4, 8 ou 16.
 
## <a name="read-all-results-from-continuations"></a>Lire tous les résultats des continuations
Si vous pensez que vous n’avez pas obtenu tous les résultats, videz complètement la continuation. En d’autres termes, continuez la lecture des résultats pendant que le jeton de continuation interrompt plusieurs documents.

Le vidage complet peut être effectué à l’aide de l’un des modèles suivants :

  * Continuer le traitement des résultats alors que la continuation n’est pas vide.
  * Continuer le traitement alors que la requête génère des résultats. 

    ```csharp
    // using AsDocumentQuery you get access to whether or not the query HasMoreResults
    // If it does, just call ExecuteNextAsync until there are no more results
    // No need to supply a continuation token here as the server keeps track of progress
    var query = client.CreateDocumentQuery<Family>(collectionLink, options).AsDocumentQuery();
    while (query.HasMoreResults)
    {
        foreach (Family family in await query.ExecuteNextAsync())
        {
            families.Add(family);
        }
    }
    ```

## <a name="choose-system-functions-that-utilize-index"></a>Choisir les fonctions système qui utilisent l’index
Si l’expression peut être convertie en une plage de valeurs de chaîne, elle peut utiliser l’index. 

Voici la liste des fonctions de chaîne qui peuvent utiliser l’index : 
    
  * STARTSWITH(str_expr, str_expr) 
  * LEFT(str_expr, num_expr) = str_expr 
  * SUBSTRING(str_expr, num_expr, num_expr) = str_expr, but only if first num_expr is 0 
    
    Voici quelques exemples de requêtes : 
    
    ```sql

    -- If there is a range index on r.name, STARTSWITH will utilize the index while ENDSWITH won't 
    SELECT * 
    FROM c 
    WHERE STARTSWITH(c.name, 'J') AND ENDSWITH(c.name, 'n')

    ```
    
    ```sql

    -- LEFT will utilize the index while RIGHT won't 
    SELECT * 
    FROM c 
    WHERE LEFT(c.name, 2) = 'Jo' AND RIGHT(c.name, 2) = 'hn'

    ```

  * Évitez les fonctions système dans le filtre (ou la clause WHERE) qui ne sont pas desservies par l'index. Contains, Upper et Lower sont exemples de fonctions système.
  * Dans la mesure du possible, écrivez les requêtes pour utiliser un filtre sur la clé de partition.
  * Pour obtenir des requêtes performantes, évitez d'appeler UPPER/LOWER dans le filtre. Normalisez plutôt la casse des valeurs lors de l'insertion. Pour chacune des valeurs, insérez la valeur avec la casse souhaitée, ou insérez à la fois la valeur originale et la valeur avec la casse souhaitée. 

    Par exemple :
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    Dans ce cas, enregistrez « JOE » en majuscules ou à la fois la valeur d’origine « Joe » et « JOE ». 
    
    Si la casse des données JSON est normalisée, la requête devient :
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    La seconde requête sera plus performante car elle ne nécessite aucune transformation sur chacune des valeurs afin de comparer les valeurs à « JOE ».

Pour plus d’informations sur les fonctions système, lisez l’article [Fonctions système](sql-query-system-functions.md).

## <a name="check-indexing-policy"></a>Vérifier la stratégie d’indexation
Pour vérifier que la [stratégie d’indexation](index-policy.md) actuelle est optimale :

  * Pour garantir une lecture plus rapide, vérifiez que tous les chemins JSON utilisés dans les requêtes sont inclus dans la stratégie d’indexation.
  * Pour des écritures plus performantes, n’incluez pas les chemins non utilisés dans les requêtes.

Pour plus de détails, lisez l’article [Guide pratique pour gérer la stratégie d’indexation](how-to-manage-indexing-policy.md).

## <a name="spatial-data-check-ordering-of-points"></a>Données spatiales : Vérifier l'ordre des points
Les points dans un polygone doivent être spécifiés dans le sens antihoraire. Un polygone spécifié dans le sens horaire représente l’inverse de la région qu’il contient.

## <a name="optimize-join-expressions"></a>Optimiser les expressions JOIN
Les expressions `JOIN` peuvent être développées en produits croisés de grande taille. Dans la mesure du possible, utilisez un filtre plus spécifique pour effectuer une recherche sur un espace de recherche plus restreint.

Les sous-requêtes multivaleurs peuvent optimiser des expressions `JOIN` en envoyant les prédicats après chaque expression de sélection multiple, plutôt qu’après toutes les jointures croisées dans la clause `WHERE`. Pour obtenir un exemple détaillé, lisez l’article [Optimiser les expressions JOIN](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions).

## <a name="optimize-order-by-expressions"></a>Optimiser les expressions ORDER BY 
Les performances de la requête `ORDER BY` peuvent baisser si les champs sont partiellement alloués ou exclus de la stratégie d'indexation.

  * Pour les champs partiellement alloués comme le temps, réduisez l'espace de recherche autant que possible à l’aide de filtres. 
  * Pour une seule propriété `ORDER BY`, incluez la propriété dans la stratégie d'indexation. 
  * Pour les expressions `ORDER BY` à plusieurs propriétés, définissez un [index composite](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes) sur les champs triés.  

## <a name="many-large-documents-being-loaded-and-processed"></a>Nombreux documents volumineux en cours de chargement et de traitement
Le temps et les unités de requête requis par une requête dépendent non seulement de la taille de la réponse, mais aussi du travail effectué par le pipeline de traitement des requêtes. Le temps et les unités de requête augmentent proportionnellement avec la quantité de travail effectué par l'ensemble du pipeline de traitement des requêtes. Les documents volumineux nécessitant plus de travail, leur chargement et leur traitement requièrent plus de temps et d’unités de requête.

## <a name="low-provisioned-throughput"></a>Faible débit approvisionné
Vérifiez que le débit provisionné est capable de gérer la charge de travail. Augmentez le budget en unités de requête pour les collections impactées.

## <a name="try-upgrading-to-the-latest-sdk-version"></a>Tentative de mise à niveau vers la dernière version du Kit de développement logiciel (SDK)
Pour déterminer la dernière version du SDK, consultez l’article [SDK Download and release notes](sql-api-sdk-dotnet.md) (Téléchargement du SDK et notes de publication).

## <a name="next-steps"></a>Étapes suivantes
Reportez-vous aux documents ci-dessous sur la mesure des unités de requête par requête et pour obtenir des statistiques d’exécution afin d’optimiser vos requêtes, entre autres :

* [Obtenir des métriques sur l’exécution des requêtes SQL à l’aide du SDK .NET](profile-sql-api-query.md)
* [Réglage des performances de requête avec Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Conseils en matière de performances pour le Kit de développement logiciel (SDK) .NET](performance-tips.md)
