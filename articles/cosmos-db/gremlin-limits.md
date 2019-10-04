---
title: Limites d’Azure Cosmos DB Gremlin
description: Documentation de référence pour les limitations du runtime du moteur de graphe
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: f1fe4cfac22a651f44338986d0a767fe3e1a360b
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910744"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Limites d’Azure Cosmos DB Gremlin
Cet article aborde les limites du moteur Azure Cosmos DB Gremlin et explique comment elles peuvent avoir un impact sur les traversées des clients.

Gremlin Cosmos DB étant basée sur l’infrastructure Cosmos DB, toutes les limites dans [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) s’appliquent. 

## <a name="limits"></a>limites

Quand une limite Gremlin est atteinte, la traversée est annulée par **x-ms-status-code** = 429, indiquant une erreur de limitation.

**Ressource**    | **Limite par défaut** | **Explication**
--- | --- | ---
*Mémoire par demande* | **2 Go** | Mémoire maximale qu’une demande peut consommer pendant le traitement. Les requêtes qui doivent calculer de grands jeux de données consommeront de la mémoire supplémentaire. Envisagez de limiter les requêtes à des jeux de données plus petits afin d’éviter de franchir cette limite, ou d’utiliser des solutions OLAP.
*Longueur du script* | **64 Ko** | Longueur maximale d’un script de traversée Gremlin par demande.
*Profondeur d’opérateurs* | **400** |  Nombre total d’étapes uniques dans une traversée. Par exemple, ```g.V().out()``` a un nombre d’opérateurs de 2 : V() et out(), ```g.V('label').repeat(out()).times(100)``` a une profondeur d’opérateurs de 3 : V(), repeat() et out(), car ```.times(100)``` est un paramètre pour l’opérateur ```.repeat()```.
*Degré de parallélisme* | **32** | Nombre maximal de partitions de stockage interrogées dans une demande unique au niveau de la couche de stockage. Les graphiques avec des centaines de partitions seront affectés par cette limite.
*Limite de répétition* | **32** | Nombre maximal d’itérations qu’un opérateur ```.repeat()``` peut exécuter. Dans la plupart des cas, chaque itération de l’étape ```.repeat()``` effectue une traversée en largeur, ce qui signifie que toute traversée est limitée au maximum à 32 tronçons entre des sommets.
*Délai d’expiration de la traversée* | **30 secondes** | La traversée est annulée quand il dépasse cette durée. Cosmos DB Graph est une base de données OLTP dont la grande majorité des traversées s’effectue en quelques millisecondes. Pour exécuter des requêtes OLAP sur Cosmos DB Graph, utilisez [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) avec des [dataframes de graphe](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) et le [connecteur Spark pour Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark).
*Limite de prédicat* | **20** | Nombre d’étapes ```.has()``` ou ```.hasNot()``` appliquées sur un sommet ou arête unique. Quand cette limite est atteinte, l’erreur visible dans l’application est ```The SQL query exceeded the maximum number of joins. The allowed limit is 20```. Il s’agit d’un désagrément temporaire ; l’équipe travaille au retrait de cette limite. 
*Délai d’expiration de connexion inactive* | **5 heures** | Intervalle de temps pendant lequel la connexion WebSocket est ouverte sans trafic sur le serveur. Les paquets TCP Keep-Alive ou les requêtes HTTP Keep-Alive n’étendent pas la durée de connexion au-delà de cette limite. Toutefois, s’ils ne sont pas envoyés, l’infrastructure Azure sous-jacente peut fermer la connexion encore plus tôt. Le moteur de graphe Cosmos DB considère qu’elle est inactive si aucune traversée Gremlin ne s’exécute dessus.
*Jetons de ressource par heure* | **100** | Nombre de jetons de ressource uniques utilisés par les clients Gremlin pour se connecter au compte Gremlin dans une région. Lorsque l’application dépasse la limite de jetons uniques horaire, `"Exceeded allowed resource token limit of 100 that can be used concurrently"` est renvoyé lors de la prochaine demande d’authentification.

## <a name="next-steps"></a>Étapes suivantes
* [En-têtes de réponse d’Azure Cosmos DB Gremlin](gremlin-headers.md) 
* [Jetons de ressource Azure Cosmos DB avec Gremlin](how-to-use-resource-tokens-gremlin.md)
