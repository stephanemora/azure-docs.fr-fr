---
title: Accéder aux propriétés des documents système via un graphe Azure Cosmos DB
description: Découvrir comment lire et écrire des propriétés des documents système Cosmos DB à l’aide de l’API Gremlin
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/10/2019
author: manishmsfte
ms.author: mansha
ms.openlocfilehash: fa76454ced3c54054be159e471f381d16be951ee
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113360754"
---
# <a name="system-document-properties"></a>Propriétés des documents système
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB a des [propriétés système](/rest/api/cosmos-db/databases) comme ```_ts```, ```_self```, ```_attachments```, ```_rid``` et ```_etag``` sur chaque document. De plus, le moteur Gremlin ajoute les propriétés ```inVPartition``` et ```outVPartition``` sur les arêtes. Par défaut, ces propriétés sont disponibles pour la traversée. Toutefois, il est possible d’inclure des propriétés spécifiques, ou toutes les propriétés, dans la traversée Gremlin.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-Tag

Cette propriété est utilisée pour le contrôle de l’accès concurrentiel optimiste. Si l’application doit décomposer l’opération en plusieurs traversées distinctes, elle peut utiliser la propriété eTag pour éviter la perte de données dans des écritures simultanées.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>Durée de vie (TTL)

Si l’expiration de document est activée pour la collection et que la propriété ```ttl``` est définie sur les documents, cette propriété est disponible dans la traversée Gremlin en tant que propriété de sommet ou d’arête normale. ```ProjectionStrategy``` n’est pas nécessaire pour activer l’exposition de la propriété de durée de vie.

Le sommet créé avec la traversée ci-après est automatiquement supprimé dans **123 secondes**.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>Étapes suivantes
* [Accès concurrentiel optimiste Cosmos DB](faq.yml#how-does-the-sql-api-provide-concurrency-)
* [Durée de vie (TTL)](time-to-live.md) dans Azure Cosmos DB
