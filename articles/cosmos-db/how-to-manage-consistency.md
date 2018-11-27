---
title: Découvrez comment gérer la cohérence dans Azure Cosmos DB
description: Découvrez comment gérer la cohérence dans Azure Cosmos DB
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 68c8c3767ff3a3d2873c1ff50928ab8d2cada4b1
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263742"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Gérer les niveaux de cohérence dans Azure Cosmos DB

Cet article décrit différentes manières de définir la cohérence par défaut, de remplacer cette cohérence sur le client, de gérer manuellement les jetons de session, et de comprendre la métrique de probabilités en fonction de l’obsolescence limitée (PBS).

## <a name="configure-the-default-consistency-level"></a>Configurer le niveau de cohérence par défaut

Le niveau de cohérence par défaut correspond au niveau de cohérence que les clients utiliseront par défaut. Il peut être remplacé par les clients.

### <a name="cli"></a>Interface de ligne de commande

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Strong

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level BoundedStaleness
```

### <a name="powershell"></a>PowerShell

L’exemple ci-dessous crée un compte Cosmos DB avec la fonction multimaître activée dans les régions USA Est et USA Ouest et la stratégie de cohérence par défaut définie en tant que Session.

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="Session"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="portal"></a>Portail

Pour afficher ou modifier le niveau de cohérence par défaut, connectez-vous au portail Azure. Recherchez votre compte Cosmos DB et ouvrez le volet **Cohérence par défaut**. Dans ce volet, sélectionnez le nouveau niveau de cohérence que vous souhaitez définir par défaut, puis cliquez sur « Enregistrer ».

![Image du menu de cohérence dans le portail Azure](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Remplacer le niveau de cohérence par défaut

Les clients peuvent remplacer le niveau de cohérence par défaut, qui est défini par le service. Cette opération peut être effectuée pour le client dans son ensemble ou pour une requête en particulier.

### <a id="override-default-consistency-dotnet"></a>Kit SDK .NET

```csharp
// Override consistency at the client level
ConsistencyPolicy consistencyPolicy = new ConsistencyPolicy
    {
        DefaultConsistencyLevel = ConsistencyLevel.BoundedStaleness,
        MaxStalenessIntervalInSeconds = 5,
        MaxStalenessPrefix = 100
    };
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, consistencyPolicy);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a id="override-default-consistency-java-async"></a>Kit SDK Java Async

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

### <a id="override-default-consistency-java-sync"></a>Kit SDK Java Sync

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Strong);
```

### <a id="override-default-consistency-javascript"></a>Kit SDK Node.js/JavaScript/TypeScript

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Strong
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>Kit SDK Python

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Strong)
```

## <a name="utilize-session-tokens"></a>Utiliser des jetons de session

Si vous souhaitez gérer manuellement des jetons de session, vous pouvez les obtenir à partir de réponses et les définir par requête. Si vous n’avez pas besoin de gérer manuellement des jetons de session, il ne vous est pas nécessaire d’utiliser les exemples ci-dessous. Le kit de développement logiciel (SDK) effectue automatiquement le suivi des jetons de session, et utilise le jeton de session le plus récent si vous ne définissez pas le jeton de session vous-même.

### <a id="utilize-session-tokens-dotnet"></a>Kit SDK .NET

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-java-async"></a>Kit SDK Java Async

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

### <a id="utilize-session-tokens-java-sync"></a>Kit SDK Java Sync

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```

### <a id="utilize-session-tokens-javascript"></a>Kit SDK Node.js/JavaScript/TypeScript

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a id="utilize-session-tokens-python"></a>Kit SDK Python

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>Surveiller la métrique de probabilités en fonction de l’obsolescence limitée (PBS)

Pour afficher la métrique PBS, accédez à votre compte Cosmos DB dans le portail Azure, puis ouvrez le volet **Métriques**. Dans ce volet, cliquez sur l’onglet **Cohérence**, puis observez le graphique intitulé **Probability of strongly consistent reads based on your workload (see PBS)** (Probabilité de lectures fortement cohérentes en fonction de votre charge de travail (voir PBS)).

![Image du graphique PBS dans le portail Azure](./media/how-to-manage-consistency/pbs-metric.png)

Utilisez le menu des métriques de Cosmos DB pour afficher cette métrique. Elle ne s’affichera pas dans l’interface de surveillance des métriques d’Azure.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la gestion des conflits de données ou pour passer au concept clé suivant dans Cosmos DB, consultez la documentation suivante :

* [Comment gérer les conflits entre les régions](how-to-manage-conflicts.md)
* [Partitionnement et distribution des données](partition-data.md)
