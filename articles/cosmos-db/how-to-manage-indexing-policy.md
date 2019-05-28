---
title: Gérer les stratégies d’indexation dans Azure Cosmos DB
description: Découvrir comment gérer les stratégies d’indexation dans Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/06/2019
ms.author: thweiss
ms.openlocfilehash: 0b47ffd77ee23f997bb7de2ea41f83c2854cba72
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550903"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Gérer les stratégies d’indexation dans Azure Cosmos DB

Dans Azure Cosmos DB, les données sont indexées suivant les [stratégies d’indexation](index-policy.md) définies pour chaque conteneur. La stratégie d’indexation par défaut pour les conteneurs nouvellement créés met en œuvre des index de plage pour les chaînes ou les nombres et des index spatiaux pour les objets GeoJSON de type Point. Cette stratégie peut être remplacée :

- à partir du portail Azure
- à l’aide de l’interface Azure CLI
- à l’aide de l’un des SDK

Une [mise à jour de la stratégie d’indexation](index-policy.md#modifying-the-indexing-policy) déclenche une transformation d’index. La progression de cette transformation peut également être suivie à partir des SDK.

> [!NOTE]
> Dans le cadre de la mise à niveau du kit de développement logiciel (SDK) et du portail, nous faisons évoluer la stratégie d’indexation pour l’aligner sur une nouvelle disposition d’index que nous avons déployée sur les nouveaux conteneurs. Avec cette nouvelle disposition, tous les types de données primitifs sont indexés en tant que plage avec précision maximale (-1). Par conséquent, les types d'index et la précision ne sont pas exposés à l’utilisateur. À l’avenir, les utilisateurs devront simplement ajouter des chemins d’accès à la section includedPaths, et ignorer indexKinds et la précision. Cette modification n'affecte en rien le niveau de performance et vous pouvez continuer à mettre à jour la stratégie d’indexation en utilisant la même syntaxe. Vous pouvez continuer à utiliser tous les exemples de notre documentation existante pour mettre à jour la stratégie d'indexation.

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

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// set the indexing mode to Consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/headquarters/employees/?" });
// update the container with our changes
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

```java
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
    DocumentCollection container = result.getResource();
    IndexingPolicy indexingPolicy = container.getIndexingPolicy();
    // set the indexing mode to Consistent
    indexingPolicy.setIndexingMode(IndexingMode.Consistent);
    Collection<ExcludedPath> excludedPaths = new ArrayList<>();
    ExcludedPath excludedPath = new ExcludedPath();
    excludedPath.setPath("/*");
    // add an excluded path
    excludedPaths.add(excludedPath);
    indexingPolicy.setExcludedPaths(excludedPaths);
    // update the container with our changes
    client.replaceCollection(container, null);
});
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

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read();
// set the indexing mode to Consistent
containerResponse.body.indexingPolicy.indexingMode = "consistent";
// add an excluded path
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/headquarters/employees/?' });
// update the container with our changes
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

```python
containerPath = 'dbs/database/colls/collection'
# retrieve the container's details
container = client.ReadContainer(containerPath)
# set the indexing mode to Consistent
container['indexingPolicy']['indexingMode'] = 'consistent'
# add an excluded path
container['indexingPolicy']['excludedPaths'] = [{"path" : "/headquarters/employees/?"}]
# update the container with our changes
response = client.ReplaceContainer(containerPath, container)
```

## <a name="indexing-policy-examples"></a>Exemples de stratégie d’indexation

Voici quelques exemples de stratégies d’indexation affichées dans leur format JSON, qui est la façon dont elles sont exposées dans le portail Azure. Les mêmes paramètres peuvent être définis par le biais de l’interface Azure CLI ou de n’importe quel SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Stratégie de refus pour exclure de façon sélective certains chemins de propriété
```
    {
        "indexingPolicy": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    },
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
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
```
    {
        "indexingPolicy": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
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

Remarque : Il est généralement recommandé d’utiliser une stratégie d’indexation de **refus** pour permettre à Azure Cosmos DB d’indexer de manière proactive toute nouvelle propriété qui peut être ajoutée à votre modèle.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Utilisation d’un index spatial uniquement sur un chemin de propriété spécifique
```
    {
        "indexingPolicy": "consistent",
        "includedPaths": [
            {
                "path": "/*",
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
                "path": "/path/to/geojson/property/?",
                "indexes": [
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
                    }
                ]
            }
        ],
        "excludedPaths": []
    }
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Exclusion de tous les chemins de propriété mais maintien de l’indexation active

Cette stratégie peut être utilisée dans les situations où la [fonctionnalité de durée de vie (TTL)](time-to-live.md) est active, mais aucun index secondaire n’est nécessaire (pour utiliser Azure Cosmos DB comme un magasin de clés-valeurs pur).
```
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Aucune indexation
```
    {
        "indexingPolicy": "none"
    }
```

## <a name="composite-indexing-policy-examples"></a>Exemples de stratégies d’indexation composite

En plus d’inclure ou d’exclure des chemins pour les propriétés individuelles, vous pouvez également spécifier un index composite. Si vous souhaitez effectuer une requête qui a une `ORDER BY` clause pour plusieurs propriétés, un [index composite](index-policy.md#composite-indexes) sur ces propriétés est requis.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Index composite défini pour (name asc, age desc) :
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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

Cet index composite peut prendre en charge les deux requêtes suivantes :

Requête 1 :
```sql
    SELECT *
    FROM c
    ORDER BY name asc, age desc    
```

Requête 2 :
```sql
    SELECT *
    FROM c
    ORDER BY name desc, age asc
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Index composite défini pour (name asc, age asc) et (name asc, age desc) :

Vous pouvez définir plusieurs index composites différents au sein de la même stratégie d’indexation. 
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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
            ]
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

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Index composite défini pour (name asc, age asc) :

Il est facultatif de spécifier l’ordre. S’il n’est pas spécifié, l’ordre est croissant.
```
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’indexation, consultez les articles suivants :

- [Vue d’ensemble de l’indexation](index-overview.md)
- [Stratégie d’indexation](index-policy.md)
