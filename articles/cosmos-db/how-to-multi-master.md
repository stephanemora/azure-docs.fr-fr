---
title: Guide pratique pour configurer les fonctionnalités multimaîtres dans Azure Cosmos DB
description: Découvrez comment configurer les fonctionnalités multimaîtres dans vos applications au sein d’Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: 1d9fa7380f62165d360888fd8cb03919f1736297
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244744"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Configurer les fonctionnalités multimaîtres dans les applications qui utilisent Azure Cosmos DB

Pour utiliser la fonctionnalité multimaître dans votre application, vous devez activer les écritures multirégions et configurer la fonctionnalité de multihébergement dans Azure Cosmos DB. Pour configurer le multihébergement, définissez la région dans laquelle l’application est déployée.

## <a id="netv2"></a>Kit SDK .NET v2

Pour configurer la fonctionnalité multimaître dans votre application, définissez `UseMultipleWriteLocations` sur `true`. Définissez également `SetCurrentLocation` en fonction de la région sur laquelle l’application est déployée et où Azure Cosmos DB est répliquée :

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>Kit SDK .NET v3 (préversion)

Pour activer la fonctionnalité multimaître dans votre application, définissez `UseCurrentRegion` en fonction de la région sur laquelle l’application est déployée et où Cosmos DB est répliquée :

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>Kit SDK Java Async

Pour activer la fonctionnalité multimaître dans votre application, définissez `policy.setUsingMultipleWriteLocations(true)` et `policy.setPreferredLocations` en fonction de la région sur laquelle l’application est déployée et où Cosmos DB est répliquée :

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a id="javascript"></a>Kits SDK Node.js, JavaScript et TypeScript

Pour activer la fonctionnalité multimaître dans votre application, définissez `connectionPolicy.UseMultipleWriteLocations` sur `true`. Définissez également `connectionPolicy.PreferredLocations` en fonction de la région sur laquelle l’application est déployée et où Cosmos DB est répliquée :

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a id="python"></a>Kit SDK Python

Pour activer la fonctionnalité multimaître dans votre application, définissez `connection_policy.UseMultipleWriteLocations` sur `true`. Définissez également `connection_policy.PreferredLocations` en fonction de la région sur laquelle l’application est déployée et où Cosmos DB est répliquée.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants :

* [Utiliser des jetons de sessions pour gérer la cohérence dans Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Types de conflit et stratégies de résolution dans Azure Cosmos DB](conflict-resolution-policies.md)
* [Haute disponibilité dans Azure Cosmos DB](high-availability.md)
* [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md)
* [Choisir le niveau de cohérence approprié dans Azure Cosmos DB](consistency-levels-choosing.md)
* [Compromis entre cohérence, disponibilité et niveau de performance dans Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Compromis entre disponibilité et performance pour différents niveaux de cohérence](consistency-levels-tradeoffs.md)
* [Mise à l’échelle du débit provisionné au niveau global](scaling-throughput.md)
* [Distribution mondiale : Sous le capot](global-dist-under-the-hood.md)
