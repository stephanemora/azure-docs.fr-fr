---
title: Jetons de ressource Azure Cosmos DB avec Gremlin
description: Découvrir comment créer des jetons de ressource et les utiliser pour accéder à la base de données de graphes
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/06/2019
ms.author: olignat
ms.openlocfilehash: fcb18fb14cf787713735da07ca2048d0853fa46c
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70807010"
---
# <a name="azure-cosmos-db-resource-tokens-with-gremlin"></a>Jetons de ressource Azure Cosmos DB avec Gremlin
Cet article explique comment utiliser des [jetons de ressource Cosmos DB](secure-access-to-data.md) pour accéder à la base de données de graphes par le biais du SDK Gremlin.

## <a name="create-a-resource-token"></a>Créer un jeton de ressources

Le SDK TinkerPop Gremlin ne dispose pas d’API pour créer des jetons de ressource. Le jeton de ressource est un concept de Cosmos DB. Pour créer des jetons de ressource, téléchargez le [SDK Azure Cosmos DB](sql-api-sdk-dotnet.md). Si votre application doit créer des jetons de ressource et les utiliser pour accéder à la base de données de graphes, il a besoin de 2 SDK distincts.

Hiérarchie du modèle objet au-dessus des jetons de ressource :
- **Compte Cosmos DB** : entité de niveau supérieur à laquelle est associé le système DNS ; par exemple, `contoso.gremlin.cosmos.azure.com`
  - **Base de données Cosmos DB**
    - **Utilisateur**
      - **Permission**
        - *Jeton* : propriété de l’objet **Autorisation** qui indique les actions autorisées ou refusées.

Le format du jeton de ressource est `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`. Cette chaîne est opaque pour les clients et doit être utilisée en l’état sans modification ou d’interprétation.

```csharp
// Notice that document client is created against .NET SDK end-point rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // Token will not be returned during ReadPermissionReedAsync() call.
  // This call will succeed only if database id, user id and permission id already exist. 
  // Note that <database id> is not a database name, it is a base64 string that represents database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Utiliser un jeton de ressources
Les jetons de ressource peuvent être utilisés directement comme propriété « password » lors de la construction de la classe `GremlinServer`.

```csharp
// Gremlin application needs to be given a resource token. It can't discover the token on its own.
// Token can be obtained for a given permission using Cosmos DB SDK or passed into the application as command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure gremlin servier to use resource token rather than master key
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

La même approche fonctionne dans tous les SDK TinkerPop Gremlin.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Limite

Un compte Gremlin unique peut émettre un nombre illimité de jetons. Cependant, un maximum de **100** jetons peuvent être utilisés simultanément dans un délai d’**1 heure**. Si l’application dépasse la limite de jetons par heure, la demande d’authentification est refusée avec le message d’erreur `"Exceeded allowed resource token limit of 100 that can be used concurrently"`. La fermeture des connexions actives avec des jetons spécifiques pour libérer des emplacements pour de nouveaux jetons n’est pas fructueuse. Le moteur de base de données Gremlin de Cosmos DB effectue le suivi des différents jetons au cours de la dernière heure précédant la demande d’authentification.

## <a name="permission"></a>Autorisation

L’erreur courante que rencontrent les applications lors de l’utilisation de jetons de ressource est `"Insufficient permissions provided in the authorization header for the corresponding request. Please retry with another authorization header."`. Cette erreur est retournée quand une traversée Gremlin tente d’écrire une arête ou un sommet, mais que le jeton de ressource accorde uniquement des autorisations `Read`. Inspectez votre traversée pour voir si elle contient l’une des étapes suivantes : `.addV()`, `.addE()`, `.drop()` ou `.property()`.

## <a name="next-steps"></a>Étapes suivantes
* [Contrôle d’accès en fonction du rôle (RBAC)](role-based-access-control.md) dans Azure Cosmos DB
* [Découvrir comment sécuriser l’accès aux données](secure-access-to-data.md) dans Azure Cosmos DB
