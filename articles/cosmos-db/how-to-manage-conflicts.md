---
title: Découvrez comment gérer les conflits entre les régions dans Azure Cosmos DB
description: Découvrez comment gérer les conflits dans Azure Cosmos DB
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 1f06b27868547dd448e3da547dd61b861db913d2
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034775"
---
# <a name="manage-conflicts-between-regions"></a>Gérer les conflits entre les régions

Avec les écritures multi-régions, lorsqu’il y a un conflit de données, vous pouvez le résoudre à l’aide de diverses stratégies de résolution de conflit. Cet article décrit la façon de gérer les stratégies de résolution de conflit à l’aide de différentes plateformes de langage.

## <a name="create-a-custom-conflict-resolution-policy"></a>Créer une stratégie de résolution de conflits personnalisée

Ces exemples montrent comment configurer un conteneur avec une stratégie de résolution de conflits personnalisée. Ces conflits apparaissent dans le flux de conflit.

### <a id="create-custom-conflict-resolution-policy-dotnet"></a>Kit SDK .NET

```csharp
DocumentCollection manualCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.manualCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
      },
  });
```

### <a id="create-custom-conflict-resolution-policy-java-async"></a>Kit SDK Java Async

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy();
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-java-sync"></a>Kit SDK Java Sync

```java
DocumentCollection manualCollection = new DocumentCollection();
manualCollection.setId(this.manualCollectionName);
ConflictResolutionPolicy customPolicy = ConflictResolutionPolicy.createCustomPolicy(null);
manualCollection.setConflictResolutionPolicy(customPolicy);
DocumentCollection createdCollection = client.createCollection(database.getSelfLink(), collection, null).getResource();
```

### <a id="create-custom-conflict-resolution-policy-javascript"></a>Kit SDK Node.js/JavaScript/TypeScript

```javascript
const database = client.database(this.databaseName);
const {
  container: manualContainer
} = await database.containers.createIfNotExists({
  id: this.manualContainerName,
  conflictResolutionPolicy: {
    mode: "Custom"
  }
});
```

### <a id="create-custom-conflict-resolution-policy-python"></a>Kit SDK Python

```python
database = client.ReadDatabase("dbs/" + self.database_name)
manual_collection = {
                    'id': self.manual_collection_name,
                    'conflictResolutionPolicy': {
                          'mode': 'Custom'
                        }
                    }
manual_collection = client.CreateContainer(database['_self'], collection)
```

## <a name="create-a-custom-conflict-resolution-policy-with-a-stored-procedure"></a>Créer une stratégie de résolution de conflits personnalisée avec une procédure stockée

Ces exemples montrent comment configurer un conteneur avec une stratégie de résolution de conflits personnalisée et une procédure stockée pour résoudre le conflit. Ces conflits n’apparaissent pas dans le flux de conflit, sauf s’il existe une erreur dans votre procédure stockée.

### <a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>Kit SDK .NET

```csharp
DocumentCollection udpCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.udpCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
          ConflictResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver"),
      },
  });
```

Après la création de votre conteneur, vous devez créer la procédure stockée `resolver`.

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-async"></a>Kit SDK Java Async

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy("resolver");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

Après la création de votre conteneur, vous devez créer la procédure stockée `resolver`.

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-sync"></a>Kit SDK Java Sync

```java
DocumentCollection udpCollection = new DocumentCollection();
udpCollection.setId(this.udpCollectionName);
ConflictResolutionPolicy udpPolicy = ConflictResolutionPolicy.createCustomPolicy(
        String.format("dbs/%s/colls/%s/sprocs/%s", this.databaseName, this.udpCollectionName, "resolver"));
udpCollection.setConflictResolutionPolicy(udpPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, udpCollection);
```

Après la création de votre conteneur, vous devez créer la procédure stockée `resolver`.

### <a id="create-custom-conflict-resolution-policy-stored-proc-javascript"></a>Kit SDK Node.js/JavaScript/TypeScript

```javascript
const database = client.database(this.databaseName);
const { container: udpContainer } = await database.containers.createIfNotExists(
  {
    id: this.udpContainerName,
    conflictResolutionPolicy: {
      mode: "Custom",
      conflictResolutionProcedure: `dbs/${this.databaseName}/colls/${
        this.udpContainerName
      }/sprocs/resolver`
    }
  }
);
```

Après la création de votre conteneur, vous devez créer la procédure stockée `resolver`.

### <a id="create-custom-conflict-resolution-policy-stored-proc-python"></a>Kit SDK Python

```python

```

Après la création de votre conteneur, vous devez créer la procédure stockée `resolver`.

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>Créer une stratégie de résolution de conflit de type last-writer-wins (dernière version valide)

Ces exemples montrent comment configurer un conteneur avec une stratégie de résolution de conflit de type last-writer-wins (dernière version valide). Si le chemin n’est pas défini ou n’est pas valide, il suit par défaut la propriété `_ts`. Cette propriété est le champ timestamp. Ces conflits n’apparaissent dans le flux de conflit.

### <a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>Kit SDK .NET

```csharp
DocumentCollection lwwCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.lwwCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.LastWriterWins,
          ConflictResolutionPath = "/regionId",
      },
  });
```

### <a id="create-custom-conflict-resolution-policy-lww-java-async"></a>Kit SDK Java Async

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createLastWriterWinsPolicy(conflictResolutionPath);
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-lww-java-sync"></a>Kit SDK Java Sync

```java
DocumentCollection lwwCollection = new DocumentCollection();
lwwCollection.setId(this.lwwCollectionName);
ConflictResolutionPolicy lwwPolicy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/regionId");
lwwCollection.setConflictResolutionPolicy(lwwPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, lwwCollection);
```

### <a id="create-custom-conflict-resolution-policy-lww-javascript"></a>Kit SDK Node.js/JavaScript/TypeScript

```javascript
const database = client.database(this.databaseName);
const { container: lwwContainer } = await database.containers.createIfNotExists(
  {
    id: this.lwwContainerName,
    conflictResolutionPolicy: {
      mode: "LastWriterWins",
      conflictResolutionPath: "/regionId"
    }
  }
);
```

Si vous omettez la propriété `conflictResolutionPath`, la propriété `_ts` est prise par défaut.

### <a id="create-custom-conflict-resolution-policy-lww-python"></a>Kit SDK Python

```python
udp_collection = {
                'id': self.udp_collection_name,
                'conflictResolutionPolicy': {
                    'mode': 'Custom',
                    'conflictResolutionProcedure': 'dbs/' + self.database_name + "/colls/" + self.udp_collection_name + '/sprocs/resolver'
                    }
                }
udp_collection = self.try_create_document_collection(create_client, database, udp_collection)
```

## <a name="read-from-conflict-feed"></a>Lire le flux de conflit

Ces exemples montrent comment lire le flux de conflit d’un conteneur. Les conflits apparaissent dans le flux de conflit uniquement s’ils n’ont pas été résolus automatiquement.

### <a id="read-from-conflict-feed-dotnet"></a>Kit SDK .NET

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
```

### <a id="read-from-conflict-feed-java-async"></a>Kit SDK Java Async

```java
FeedResponse<Conflict> response = client.readConflicts(this.manualCollectionUri, null)
                    .first().toBlocking().single();
for (Conflict conflict : response.getResults()) {
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-java-sync"></a>Kit SDK Java Sync

```java
Iterator<Conflict> conflictsIterartor = client.readConflicts(this.collectionLink, null).getQueryIterator();
while (conflictsIterartor.hasNext()) {
    Conflict conflict = conflictsIterartor.next();
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-javascript"></a>Kit SDK Node.js/JavaScript/TypeScript

```javascript
const container = client
  .database(this.databaseName)
  .container(this.lwwContainerName);

const { result: conflicts } = await container.conflicts.readAll().toArray();
```

### <a id="read-from-conflict-feed-python"></a>Python

```python
conflicts_iterartor = iter(client.ReadConflicts(self.manual_collection_link))
conflict = next(conflicts_iterartor, None)
while conflict:
    # Do something with conflict
    conflict = next(conflicts_iterator, None)
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts Azure Cosmos DB suivants :

* [Partitionnement et distribution des données](partition-data.md)
* [Indexation dans Azure Cosmos DB](indexing-policies.md)

