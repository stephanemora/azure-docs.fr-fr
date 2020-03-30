---
title: Exécution de requêtes SQL dans Azure Cosmos DB
description: Découvrez comment créer une requête SQL et l’exécuter dans Azure Cosmos DB. Cet article explique comment créer et exécuter une requête SQL avec l’API REST, le SDK .Net, le SDK JavaScript et divers autres kits SDK.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871259"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Exécution de requêtes SQL Azure Cosmos DB

N’importe quel langage capable d’émettre des requêtes HTTP/HTTPS peut appeler l’API REST de Cosmos DB. Cosmos DB propose également des bibliothèques de programmation pour les langages de programmation .NET, Node.js, JavaScript et Python. L’API REST et les bibliothèques prennent toutes en charge l’interrogation par le biais de SQL, et le kit SDK .NET prend également en charge l’[interrogation LINQ](sql-query-linq-to-sql.md).

Les exemples suivants montrent comment créer une requête et la soumettre à un compte de base de données Cosmos.

## <a name="rest-api"></a><a id="REST-API"></a>API REST

Cosmos DB fournit un modèle de programmation RESTful ouvert sur HTTP. Le modèle de ressource se compose d’un ensemble de ressources sous un compte de base de données, que provisionne un abonnement Azure. Le compte de base de données se compose d’un ensemble de *bases de données*, chacune pouvant contenir plusieurs *conteneurs* qui, pour leur part, contiennent des *éléments*, des fonctions définies par l’utilisateur et d’autres types de ressource. Chaque ressource Cosmos DB est adressable à l’aide d’un URI stable et logique. Un ensemble de ressources est appelé *flux*. 

Le modèle d’interaction de base avec ces ressources consiste à utiliser les verbes HTTP `GET`, `PUT`, `POST` et `DELETE`, avec leurs interprétations standard. Utilisez `POST` pour créer une ressource, exécuter une procédure stockée ou émettre une requête Cosmos DB. Les requêtes sont toujours des opérations en lecture seule sans effets secondaires.

Les exemples suivants montrent un `POST` pour une requête API SQL par rapport à des exemples d’éléments. La requête contient un simple filtre sur la propriété `name` JSON. Les en-têtes `x-ms-documentdb-isquery` et Content-Type: `application/query+json` indiquent que l’opération est une requête. Remplacez `mysqlapicosmosdb.documents.azure.com:443` par l’URI de votre compte Cosmos DB.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

Les résultats sont :

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

La requête suivante, plus complexe, retourne plusieurs résultats à partir d’une jointure :

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": []
    }
```

Les résultats sont : 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

Si les résultats d’une requête ne tiennent pas sur une seule page, l’API REST retourne un jeton de continuation via l’en-tête de réponse `x-ms-continuation-token`. Les clients peuvent paginer les résultats en incluant l’en-tête dans les résultats suivants. Vous pouvez aussi contrôler le nombre de résultats par page via l’en-tête de nombre `x-ms-max-item-count`.

Si une requête inclut une fonction d’agrégation telle que COUNT, la page de requête peut retourner une valeur partiellement agrégée sur une seule page de résultats. Les clients doivent effectuer une agrégation de deuxième niveau sur ces résultats pour produire les résultats finaux. Par exemple, ils peuvent additionner les nombres retournés dans les pages individuelles pour retourner le nombre total.

Pour gérer la stratégie de cohérence des données des requêtes, utilisez l’en-tête `x-ms-consistency-level` comme dans toutes les requêtes d’API REST. En outre, le maintien de la cohérence par session nécessite l’application de l’écho sur le dernier en-tête de cookie `x-ms-session-token` dans la demande de requête. La stratégie d’indexation du conteneur interrogé peut également influencer la cohérence des résultats de la requête. Avec les paramètres de stratégie d’indexation par défaut pour les conteneurs, l’index est toujours actualisé avec le contenu de l’élément, et les résultats de la requête correspondent à la cohérence choisie pour les données. Pour plus d’informations, consultez [Niveaux de cohérence dans Azure Cosmos DB][consistency-levels].

Si la stratégie d’indexation configurée pour le conteneur ne peut pas prendre en charge la requête spécifiée, le serveur Azure Cosmos DB retourne le code d’état 400 « Demande incorrecte ». Ce message d’erreur est retourné pour les requêtes ayant des chemins explicitement exclus de l’indexation. Vous pouvez spécifier l’en-tête `x-ms-documentdb-query-enable-scan` pour permettre à la requête d’effectuer une analyse quand un index n’est pas disponible.

Vous pouvez obtenir les indicateurs de performance détaillés sur l’exécution des requêtes en définissant l’en-tête `x-ms-documentdb-populatequerymetrics` sur `true`. Pour en savoir plus, consultez la section relative aux [métriques de requête SQL pour Azure Cosmos DB](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C# (SDK .NET)

Le kit SDK .NET prend en charge l'interrogation LINQ et SQL. L’exemple suivant montre comment exécuter la requête de filtre précédente avec .NET :

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

L’exemple suivant détermine s’il y a égalité entre les deux propriétés de chaque élément et utilise des projections anonymes.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

Le prochain exemple illustre des jointures, exprimées via l’opérateur LINQ `SelectMany`.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

Le client .NET effectue une itération automatique à travers l’ensemble des pages des résultats de la requête dans les blocs `foreach`, comme indiqué dans l’exemple précédent. Les options de requête présentées dans la section sur l’[API REST](#REST-API) sont également disponibles dans le kit SDK .NET à l’aide des classes `FeedOptions` et `FeedResponse` dans la méthode `CreateDocumentQuery`. Vous pouvez contrôler le nombre de pages à l’aide du paramètre `MaxItemCount`.

Vous pouvez également contrôler explicitement la pagination en créant `IDocumentQueryable` à l’aide de l’objet `IQueryable`, puis en lisant les valeurs `ResponseContinuationToken` et en les retransférant en tant que `RequestContinuationToken` dans `FeedOptions`. Vous pouvez définir `EnableScanInQuery` pour autoriser les analyses quand la stratégie d’indexation configurée ne prend pas en charge la requête. Dans le cas des conteneurs partitionnés, vous pouvez utiliser `PartitionKey` pour exécuter la requête sur une seule partition, même si Azure Cosmos DB peut extraire automatiquement cet élément du texte de la requête. Vous pouvez utiliser `EnableCrossPartitionQuery` pour exécuter des requêtes sur plusieurs partitions.

Pour découvrir plus d’exemples .NET avec des requêtes, consultez [Exemples .NET Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3) dans GitHub.

## <a name="javascript-server-side-api"></a><a id="JavaScript-server-side-API"></a>API JavaScript côté serveur

Azure Cosmos DB fournit un modèle de programmation pour l’[exécution de la logique d’application JavaScript](stored-procedures-triggers-udfs.md) directement sur des conteneurs par le biais de procédures stockées et de déclencheurs. La logique JavaScript inscrite au niveau du conteneur peut alors émettre des opérations de base de données sur les éléments du conteneur donné, encapsulées dans des transactions ACID ambiantes.

L’exemple suivant illustre l’utilisation de `queryDocuments` dans l’API JavaScript côté serveur pour créer des requêtes depuis l’intérieur des procédures stockées et des déclencheurs :

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a name="next-steps"></a>Étapes suivantes

- [Présentation d’Azure Cosmos DB](introduction.md)
- [Exemples .NET Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Niveaux de cohérence Azure Cosmos DB](consistency-levels.md)
