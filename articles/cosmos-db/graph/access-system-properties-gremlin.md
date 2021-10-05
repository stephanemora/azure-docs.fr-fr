---
title: Accéder aux propriétés des documents système via un graphe Azure Cosmos DB
description: Découvrir comment lire et écrire des propriétés des documents système Cosmos DB à l’aide de l’API Gremlin
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/16/2021
author: manishmsfte
ms.author: mansha
ms.openlocfilehash: 8a0574d5622ae0ceceb52be72ccd8c5d99a18529
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614804"
---
# <a name="system-document-properties"></a>Propriétés des documents système
[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

Azure Cosmos DB a des [propriétés système](/rest/api/cosmos-db/databases) comme ```_ts```, ```_self```, ```_attachments```, ```_rid``` et ```_etag``` sur chaque document. De plus, le moteur Gremlin ajoute les propriétés ```inVPartition``` et ```outVPartition``` sur les arêtes. Par défaut, ces propriétés sont disponibles pour la traversée. Toutefois, il est possible d’inclure des propriétés spécifiques, ou toutes les propriétés, dans la traversée Gremlin.

```console
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-Tag

Cette propriété est utilisée pour le contrôle de l’accès concurrentiel optimiste. Si l’application doit décomposer l’opération en plusieurs traversées distinctes, elle peut utiliser la propriété eTag pour éviter la perte de données dans des écritures simultanées.

```console
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>Durée de vie (TTL)

Si l’expiration de document est activée pour la collection et que la propriété `ttl` est définie sur les documents, cette propriété est disponible dans la traversée Gremlin en tant que propriété de sommet ou d’arête normale. `ProjectionStrategy` n’est pas nécessaire pour activer l’exposition de la propriété de durée de vie.

* Utilisez la commande suivante pour définir la durée de vie sur un nouveau Vertex :

  ```console
  g.addV(<ID>).property('ttl', <expirationTime>)
  ```

  Par exemple, un vertex créé avec la traversée suivante est automatiquement supprimé après *123 secondes* :

  ```console
  g.addV('vertex-one').property('ttl', 123)
  ```

* Utilisez la commande suivante pour définir la durée de vie sur un vertex existant :

  ```console
  g.V().hasId(<ID>).has('pk', <pk>).property('ttl', <expirationTime>)
  ```

* L’application de la propriété durée de vie sur les vertex ne l’applique pas automatiquement aux périphéries. Étant donné que les périphéries sont des enregistrements indépendants dans le magasin de base de données. Utilisez la commande suivante pour définir la durée de vie sur les vertex et toutes les périphéries entrantes et sortantes du vertex :

  ```console
  g.V().hasId(<ID>).has('pk', <pk>).as('v').bothE().hasNot('ttl').property('ttl', <expirationTime>)
  ```

Vous pouvez définir la durée de vie (TTL) sur le conteneur sur-1 ou lui affecter la valeur **On (aucune valeur par défaut)** à partir du portail Azure, ensuite la durée de vie sera infinie pour tout élément, sauf si la valeur TTL est définie explicitement pour l’élément.

## <a name="next-steps"></a>Étapes suivantes
* [Accès concurrentiel optimiste Cosmos DB](../faq.yml#how-does-the-sql-api-provide-concurrency-)
* [Durée de vie (TTL)](../time-to-live.md) dans Azure Cosmos DB
