---
title: Gérer les stratégies d’indexation dans Azure Cosmos DB
description: Découvrir comment gérer les stratégies d’indexation dans Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: thweiss
ms.openlocfilehash: ede4266457aaa76bdd9f1141df5c2981bb722326
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915909"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Gérer les stratégies d’indexation dans Azure Cosmos DB

Dans Azure Cosmos DB, les données sont indexées suivant les [stratégies d’indexation](index-policy.md) définies pour chaque conteneur. La stratégie d’indexation par défaut pour les conteneurs nouvellement créés applique des index de plage pour les chaînes ou les nombres. Vous pouvez remplacer cette stratégie par votre propre stratégie d’indexation personnalisée.

## <a name="indexing-policy-examples"></a>Exemples de stratégie d’indexation

Voici quelques exemples de stratégies d’indexation affichées dans leur format JSON, qui est la façon dont elles sont exposées dans le portail Azure. Les mêmes paramètres peuvent être définis par le biais de l’interface Azure CLI ou de n’importe quel SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Stratégie de refus pour exclure de façon sélective certains chemins de propriété

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

Cette stratégie d’indexation est équivalente à celle ci-dessous, qui permet de définir manuellement les valeurs par défaut de ```kind```, ```dataType``` et ```precision```. Il n’est plus nécessaire de définir explicitement ces propriétés. Vous pouvez les omettre entièrement dans votre stratégie d’indexation (comme indiqué dans l’exemple ci-dessus).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number",
                        "precision": -1
                    },
                    {
                        "kind": "Range",
                        "dataType": "String",
                        "precision": -1
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Stratégie d’acceptation pour inclure de façon sélective certains chemins de propriété

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

Cette stratégie d’indexation est équivalente à celle ci-dessous, qui permet de définir manuellement les valeurs par défaut de ```kind```, ```dataType``` et ```precision```. Il n’est plus nécessaire de définir explicitement ces propriétés. Vous pouvez les omettre entièrement dans votre stratégie d’indexation (comme indiqué dans l’exemple ci-dessus).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

> [!NOTE] 
> Il est généralement recommandé d’utiliser une stratégie d’indexation de **refus** pour permettre à Azure Cosmos DB d’indexer de manière proactive toute nouvelle propriété qui peut être ajoutée à votre modèle.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Utilisation d’un index spatial uniquement sur un chemin de propriété spécifique

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/path/to/geojson/property/?",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ]
}
```

## <a name="composite-indexing-policy-examples"></a>Exemples de stratégies d’indexation composite

En plus d’inclure ou d’exclure des chemins pour les propriétés individuelles, vous pouvez également spécifier un index composite. Si vous souhaitez effectuer une requête qui a une `ORDER BY` clause pour plusieurs propriétés, un [index composite](index-policy.md#composite-indexes) sur ces propriétés est requis. De plus, les index composites présentent un avantage en matière de performances pour les requêtes qui ont un filtre et dont la clause ORDER BY spécifie des propriétés différentes.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Index composite défini pour (name asc, age desc) :

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

L’index composite ci-dessus sur le nom et l’âge est requis pour les requêtes 1 et 2 :

Requête 1 :

```sql
    SELECT *
    FROM c
    ORDER BY c.name ASC, c.age DESC
```

Requête 2 :

```sql
    SELECT *
    FROM c
    ORDER BY c.name DESC, c.age ASC
```

Cet index composite tire parti des requêtes 3 et 4 et optimise les filtres :

Requête 3 :

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

Requête 4 :

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Index composite défini pour (name ASC, age ASC) et (name ASC, age DESC) :

Vous pouvez définir plusieurs index composites différents au sein de la même stratégie d’indexation.

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ],
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Index composite défini pour (name ASC, age ASC) :

Il est facultatif de spécifier l’ordre. S’il n’est pas spécifié, l’ordre est croissant.

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Exclusion de tous les chemins de propriété mais maintien de l’indexation active

Cette stratégie peut être utilisée dans les situations où la [fonctionnalité de durée de vie (TTL)](time-to-live.md) est active, mais aucun index secondaire n’est nécessaire (pour utiliser Azure Cosmos DB comme un magasin de clés-valeurs pur).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Aucune indexation

Cette stratégie désactive l’indexation. Si `indexingMode` a la `none`valeur, vous ne pouvez pas définir de TTL sur le conteneur.

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>Mie à jour d’une stratégie d’indexation

Dans Azure Cosmos DB, vous pouvez mettre à jour la stratégie d’indexation à l’aide de l’une des méthodes ci-dessous :

- à partir du portail Azure
- à l’aide de l’interface Azure CLI
- à l’aide de l’un des SDK

Une [mise à jour de la stratégie d’indexation](index-policy.md#modifying-the-indexing-policy) déclenche une transformation d’index. La progression de cette transformation peut également être suivie à partir des SDK.

> [!NOTE]
> Lors de la mise à jour de la stratégie d’indexation, les écritures dans Azure Cosmos DB sont ininterrompues. Lors de la réindexation, les requêtes peuvent retourner des résultats partiels au fur et à mesure de la mise à jour de l’index.

## <a name="use-the-azure-portal"></a>Utilisation du portail Azure

Les conteneurs Azure Cosmos stockent leur stratégie d’indexation sous la forme d’un document JSON que le portail Azure vous permet de modifier directement.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. Créez un compte Azure Cosmos ou sélectionnez un compte existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez le conteneur avec lequel vous voulez travailler.

1. Cliquez sur **Mise à l’échelle et paramètres**.

1. Modifiez le document JSON de stratégie d’indexation (voir les exemples [ci-dessous](#indexing-policy-examples)).

1. Cliquez sur **Enregistrer** quand vous avez terminé.

![Gérer l’indexation avec le portail Azure](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Microsoft Azure

La commande [az cosmosdb collection update](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-update) à partir de l’interface Azure CLI vous permet de remplacer la définition JSON de la stratégie d’indexation d’un conteneur :

```azurecli-interactive
az cosmosdb collection update \
    --resource-group-name $resourceGroupName \
    --name $accountName \
    --db-name $databaseName \
    --collection-name $containerName \
    --indexing-policy "{\"indexingMode\": \"consistent\", \"includedPaths\": [{ \"path\": \"/*\", \"indexes\": [{ \"dataType\": \"String\", \"kind\": \"Range\" }] }], \"excludedPaths\": [{ \"path\": \"/headquarters/employees/?\" } ]}"
```

## <a name="use-the-net-sdk-v2"></a>Utiliser le SDK .NET V2

L’objet `DocumentCollection` du [SDK .NET v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (voir [ce Guide de démarrage rapide](create-sql-api-dotnet.md) concernant son utilisation) expose une propriété `IndexingPolicy` qui vous permet de changer la valeur `IndexingMode`, et d’ajouter ou de supprimer des `IncludedPaths` et `ExcludedPaths`.

Récupérer les détails du conteneur

```csharp
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
```

Définir le mode d’indexation sur cohérent

```csharp
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
```

Ajouter un chemin inclus

```csharp
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/age/*" });
```

Ajouter un chemin exclu

```csharp
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
```

Ajouter un index spatial

```csharp
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
```

Ajouter un Index composite

```csharp
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
```

Mettre à jour le conteneur avec les modifications

```csharp
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Pour suivre la progression de la transformation de l’index, vous devez passer un objet `RequestOptions` qui définit la propriété `PopulateQuotaInfo` sur `true`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-java-sdk"></a>Utiliser le SDK Java

L’objet `DocumentCollection` du [SDK Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (voir [ce Guide de démarrage rapide](create-sql-api-java.md) concernant son utilisation) expose les méthodes `getIndexingPolicy()` et `setIndexingPolicy()`. L’objet `IndexingPolicy` qu’elles manipulent vous permet de changer le mode d’indexation, et d’ajouter ou de supprimer des chemins inclus ou exclus.

Récupérer les détails du conteneur

```java
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();
```

Définir le mode d’indexation sur cohérent

```java
indexingPolicy.setIndexingMode(IndexingMode.Consistent);
```

Ajouter un chemin inclus

```java
Collection<IncludedPath> includedPaths = new ArrayList<>();
ExcludedPath includedPath = new IncludedPath();
includedPath.setPath("/age/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);
```

Ajouter un chemin exclu

```java
Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);
```

Ajouter un index spatial

```java
Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);          
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

```

Ajouter un Index composite

```java
Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name/*");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age/*");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);
```

Mettre à jour le conteneur avec les modifications

```java
 client.replaceCollection(container, null);
```

Pour suivre la progression de la transformation de l’index sur un conteneur, passez un objet `RequestOptions` qui demande les informations de quota à remplir, puis récupérez la valeur à partir de l’en-tête de réponse `x-ms-documentdb-collection-index-transformation-progress`.

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>Utiliser le SDK Node.js

L’interface `ContainerDefinition` du [SDK Node.js](https://www.npmjs.com/package/@azure/cosmos) (voir [ce Guide de démarrage rapide](create-sql-api-nodejs.md) concernant son utilisation) expose une propriété `indexingPolicy` qui vous permet de changer la valeur `indexingMode`, et d’ajouter ou de supprimer des `includedPaths` et `excludedPaths`.

Récupérer les détails du conteneur

```javascript
const containerResponse = await client.database('database').container('container').read();
```

Définir le mode d’indexation sur cohérent

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

Ajouter un chemin inclus comprenant un index spatial

```javascript
containerResponse.body.indexingPolicy.includedPaths.push({
    includedPaths: [
      {
        path: "/age/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.String
          },
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.Number
          }
        ]
      },
      {
        path: "/locations/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Spatial,
            dataType: cosmos.DocumentBase.DataType.Point
          }
        ]
      }
    ]
  });
```

Ajouter un chemin exclu

```javascript
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/name/*' });
```

Mettre à jour le conteneur avec les modifications

```javascript
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Pour suivre la progression de la transformation de l’index sur un conteneur, passez un objet `RequestOptions` qui définit la propriété `populateQuotaInfo` sur `true`, puis récupérez la valeur à partir de l’en-tête de réponse `x-ms-documentdb-collection-index-transformation-progress`.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>Utiliser le SDK Python

Quand vous utilisez le [SDK Python](https://pypi.org/project/azure-cosmos/) (voir [ce Guide de démarrage rapide](create-sql-api-python.md) concernant son utilisation), la configuration du conteneur est gérée comme un dictionnaire. À partir de ce dictionnaire, il est possible d’accéder à la stratégie d’indexation et à tous ses attributs.

Récupérer les détails du conteneur

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

Définir le mode d’indexation sur cohérent

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

Définir une stratégie d’indexation avec un chemin inclus et un index spatial

```python
container["indexingPolicy"] = {

    "indexingMode":"consistent",
    "spatialIndexes":[
                {"path":"/location/*","types":["Point"]}
             ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Définir une stratégie d’indexation avec un chemin exclu

```python
container["indexingPolicy"] = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Ajouter un Index composite

```python
container['indexingPolicy']['compositeIndexes'] = [
                [
                    {
                        "path": "/name",
                        "order": "ascending"
                    },
                    {
                        "path": "/age",
                        "order": "descending"
                    }
                ]
                ]
```

Mettre à jour le conteneur avec les modifications

```python
response = client.ReplaceContainer(containerPath, container)
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’indexation, consultez les articles suivants :

- [Vue d’ensemble de l’indexation](index-overview.md)
- [Stratégie d’indexation](index-policy.md)
