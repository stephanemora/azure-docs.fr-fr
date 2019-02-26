---
title: Guide pratique pour configurer les fonctionnalités multimaîtres dans Azure Cosmos DB
description: Découvrez comment configurer les fonctionnalités multimaîtres dans vos applications au sein d’Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: 84c8e2921602bb653c0b1ef0adffd3d89e91bd78
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312138"
---
# <a name="how-to-configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Comment configurer les fonctionnalités multimaîtres dans les applications qui utilisent Azure Cosmos DB

Pour utiliser les fonctionnalités multimaîtres dans vos applications, vous devez activer les écritures multirégions et configurer la fonctionnalité de multirésidence. Pour configurer la multirésidence, définissez la région dans laquelle l’application est déployée.

## <a id="netv2"></a>Kit SDK .NET v2

Pour activer les fonctionnalités multimaîtres dans vos applications, affectez la valeur true à `UseMultipleWriteLocations`, puis configurez `SetCurrentLocation` en fonction de la région dans laquelle l’application est déployée et Azure Cosmos DB est répliqué.

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

Pour activer les fonctionnalités multimaîtres dans vos applications, configurez `UseCurrentRegion` en fonction de la région sur laquelle l’application est déployée et Cosmos DB répliquée.

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>Kit SDK Java Async

Pour activer les fonctionnalités multimaîtres dans vos applications, affectez la valeur true à `policy.setUsingMultipleWriteLocations(true)`, puis configurez `policy.setPreferredLocations` en fonction de la région sur laquelle l’application est déployée et Cosmos DB répliquée.

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

## <a id="javascript"></a>Kit SDK Node.js, JavaScript et TypeScript

Pour activer les fonctionnalités multimaîtres dans vos applications, affectez la valeur true à `connectionPolicy.UseMultipleWriteLocations`, puis configurez `connectionPolicy.PreferredLocations` en fonction de la région sur laquelle l’application est déployée et Cosmos DB répliquée.

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

Pour activer les fonctionnalités multimaîtres dans vos applications, affectez la valeur true à `connection_policy.UseMultipleWriteLocations`, puis configurez `connection_policy.PreferredLocations` en fonction de la région sur laquelle l’application est déployée et Cosmos DB répliquée.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les fonctionnalités multimaîtres ainsi que sur la distribution et la cohérence globale dans Azure Cosmos DB. Consultez les articles suivants :

* [Utiliser des jetons de session pour gérer la cohérence dans Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)

* [Types de conflit et stratégies de résolution dans Azure Cosmos DB](conflict-resolution-policies.md)

* [Haute disponibilité dans Azure Cosmos DB](high-availability.md)

* [Choix du niveau de cohérence approprié dans Azure Cosmos DB](consistency-levels-choosing.md)

* [Compromis entre cohérence, disponibilité et niveau de performance dans Azure Cosmos DB](consistency-levels-tradeoffs.md)
