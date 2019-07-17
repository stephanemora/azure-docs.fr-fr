---
title: Création d’un point de terminaison RESTful pour des fournisseurs personnalisés
description: Ce tutoriel passe en revue la création d’un point de terminaison RESTful pour des fournisseurs personnalisés. Il décrit en détail comment gérer les demandes et les réponses des méthodes HTTP RESTful prises en charge.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 176e3b02cbda7577e306d86363cfe5b41335fb6e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799178"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Création d’un point de terminaison RESTful pour des fournisseurs personnalisés

Les fournisseurs personnalisés vous permettent de personnaliser des workflows dans Azure. Un fournisseur personnalisé implique qu’un contrat soit passé entre Azure et un point de terminaison (`endpoint`). Ce tutoriel vous guide tout au long du processus de création d’un `endpoint` RESTful pour un fournisseur personnalisé. Si vous ne savez pas encore ce qu’est un fournisseur personnalisé Azure, consultez la [vue d’ensemble des fournisseurs de ressources personnalisés](./custom-providers-overview.md).

Dans ce tutoriel, vous allez effectuer les étapes suivantes :

- Utiliser des actions et des ressources personnalisées
- Partitionner des ressources personnalisées dans le stockage
- Prendre en charge les méthodes RESTful de fournisseur personnalisé
- Intégrer des opérations RESTful

Ce tutoriel s’appuie sur les tutoriels suivants :

- [Configurer des fonctions Azure pour des fournisseurs personnalisés Azure](./tutorial-custom-providers-function-setup.md)

> [!NOTE]
> Ce tutoriel repose sur le tutoriel précédent. Certaines des étapes du tutoriel ne fonctionnent que si une fonction Azure a été configurée pour utiliser des fournisseurs personnalisés.

## <a name="working-with-custom-actions-and-custom-resources"></a>Utiliser des actions et des ressources personnalisées

Dans ce tutoriel, nous allons mettre à jour la fonction de manière à ce qu’elle fonctionne en tant que point de terminaison RESTful pour notre fournisseur personnalisé. Dans Azure, les ressources et les actions sont modélisées selon la spécification RESTful de base : PUT - crée une ressource ; GET (instance) - récupère une ressource existante ; DELETE - supprime une ressource existante ; POST - déclenche une action ; GET (collection) - liste toutes les ressources existantes. Dans ce tutoriel, nous allons utiliser Table Azure pour le stockage, mais n’importe quel service de stockage ou base de données fait l’affaire.

## <a name="how-to-partition-custom-resources-in-storage"></a>Partitionner des ressources personnalisées dans le stockage

Dans la mesure où nous créons un service RESTful, nous devons stocker les ressources créées dans le stockage. Pour le stockage Table Azure, nous devons générer des clés de partition et de ligne pour nos données. Dans le cas de fournisseurs personnalisés, les données doivent être partitionnées par le fournisseur personnalisé. Quand une demande entrante est envoyée au fournisseur personnalisé, ce dernier ajoute l’en-tête `x-ms-customproviders-requestpath` à la demande sortante à destination de `endpoint`.

Exemple d’en-tête `x-ms-customproviders-requestpath` pour une ressource personnalisée :

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

D’après l’exemple d’en-tête `x-ms-customproviders-requestpath` ci-dessus, nous pouvons créer les paramètres partitionKey et rowKey pour notre stockage comme ceci :

Paramètre | Modèle | Description
---|---
partitionKey | '{subscriptionId}:{resourceGroupName}:{resourceProviderName}' | Le paramètre partitionKey correspond à la façon dont les données sont partitionnées. Dans la plupart des cas, les données doivent être partitionnées par l’instance de fournisseur personnalisé.
rowKey | '{myResourceType}:{myResourceName}' | Le paramètre rowKey est l’identificateur individuel des données. La plupart du temps, il s’agit du nom de la ressource.

Nous devons également créer une classe pour modéliser notre ressource personnalisée. Dans ce tutoriel, nous allons ajouter la classe `CustomResource` à notre fonction. Cette classe générique accepte toutes les données entrées :

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```

Cette opération crée une classe de base basée sur `TableEntity`, qui sert à stocker les données. La classe `CustomResource` hérite deux propriétés de `TableEntity` : partitionKey et rowKey.

## <a name="support-custom-provider-restful-methods"></a>Prendre en charge les méthodes RESTful de fournisseur personnalisé

> [!NOTE]
> Si vous ne copiez pas le code directement à partir du tutoriel, le contenu de la réponse doit être un code JSON valide qui définit `application/json` comme en-tête de `Content-Type`.

Maintenant que la configuration du partitionnement des données est en place, structurons les méthodes CRUD et de déclenchement de base pour les ressources et actions personnalisées. Dans la mesure où les fournisseurs personnalisés agissent comme proxy, la demande et la réponse doivent être modélisées et gérées par le `endpoint` RESTful. Suivez les extraits de code ci-dessous pour gérer les opérations RESTful de base :

### <a name="trigger-custom-action"></a>Déclencher une action personnalisée

Pour les fournisseurs personnalisés, une action personnalisée est déclenchée par le biais de demandes `POST`. Une action personnalisée peut éventuellement accepter un corps de demande qui contient un ensemble de paramètres d’entrée. L’action doit ensuite retourner une réponse signalant le résultat de l’action et si elle a réussi ou échoué. Dans ce tutoriel, nous allons ajouter la méthode `TriggerCustomAction` à notre fonction :

```csharp
/// <summary>
/// Triggers a custom action with some side effect.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <returns>The http response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

La méthode `TriggerCustomAction` accepte une demande entrante et renvoie simplement la réponse avec un code d’état de réussite. 

### <a name="create-custom-resource"></a>Créer une ressource personnalisée

Pour les fournisseurs personnalisés, une ressource personnalisée est créée au moyen de demandes `PUT`. Le fournisseur personnalisé accepte un corps de demande JSON qui contient un ensemble de propriétés pour la ressource personnalisée. Dans Azure, les ressources suivent un modèle RESTful. La même URL de demande qui a été utilisée pour créer une ressource doit également pouvoir récupérer et supprimer la ressource. Dans ce tutoriel, nous allons ajouter la méthode `CreateCustomResource` pour créer des ressources :

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="azureResourceId">The parsed Azure resource Id.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

La méthode `CreateCustomResource` met à jour la demande entrante en y ajoutant les champs spécifiques à Azure : `id`, `name` et `type`. Il s’agit des propriétés de niveau supérieur utilisées par les services dans Azure. Elles permettent au fournisseur personnalisé de s’intégrer à d’autres services comme Azure Policy, Modèles Azure Resource Manager et Journaux d’activité Azure.

Propriété | Exemple | Description
---|---|---
Nom | '{myCustomResourceName}' | Nom de la ressource personnalisée.
Type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | Espace de noms du type de ressource.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName}' | ID de la ressource.

Outre l’ajout des propriétés, nous enregistrons le document dans Stockage Table Azure. 

### <a name="retrieve-custom-resource"></a>Récupérer une ressource personnalisée

Pour les fournisseurs personnalisés, une ressource personnalisée est récupérée au moyen de demandes `GET`. Le fournisseur personnalisé *n’accepte pas* un corps de demande JSON. Dans le cas de demandes `GET`, le **point de terminaison** doit utiliser l’en-tête `x-ms-customproviders-requestpath` pour retourner la ressource déjà créée. Dans ce tutoriel, nous allons ajouter la méthode `RetrieveCustomResource` pour récupérer des ressources existantes :

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

Dans Azure, les ressources doivent suivre un modèle RESTful. L’URL de la demande ayant créé la ressource doit également retourner la ressource si une demande `GET` est effectuée.

### <a name="remove-custom-resource"></a>Supprimer une ressource personnalisée

Pour les fournisseurs personnalisés, une ressource personnalisée est supprimée au moyen de demandes `DELETE`. Le fournisseur personnalisé *n’accepte pas* un corps de demande JSON. Dans le cas de demandes `DELETE`, le **point de terminaison** doit utiliser l’en-tête `x-ms-customproviders-requestpath` pour supprimer la ressource déjà créée. Dans ce tutoriel, nous allons ajouter la méthode `RemoveCustomResource` pour supprimer des ressources existantes :

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the result of the delete.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

Dans Azure, les ressources doivent suivre un modèle RESTful. L’URL de la demande ayant créé la ressource doit également supprimer la ressource si une demande `DELETE` est effectuée.

### <a name="list-all-custom-resources"></a>Lister toutes les ressources personnalisées

Pour les fournisseurs personnalisés, une liste des ressources personnalisées existantes peut être obtenue au moyen de demandes `GET` de collecte. Le fournisseur personnalisé *n’accepte pas* un corps de demande JSON. Dans le cas de demandes `GET` de collecte, le `endpoint` doit utiliser l’en-tête `x-ms-customproviders-requestpath` pour énumérer les ressources déjà créées. Dans ce tutoriel, nous allons ajouter la méthode `EnumerateAllCustomResources` pour énumérer les ressources existantes.

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The http response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> Table Azure utilise la syntaxe de clé de ligne « supérieure à » et « inférieure à » pour effectuer une requête « startswith » sur les chaînes. 

Pour lister toutes les ressources existantes, nous générons une requête Table Azure qui vérifie que les ressources existent sous la partition de notre fournisseur personnalisé. La requête vérifie ensuite que la clé de ligne commence par le même `{myResourceType}`.

## <a name="integrate-restful-operations"></a>Intégrer des opérations RESTful

Une fois toutes les méthodes RESTful ajoutées à la fonction, nous pouvons mettre à jour la méthode `Run` principale pour appeler les fonctions et gérer les différentes demandes REST :

```csharp
/// <summary>
/// Entry point for the Azure Function webhook and acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <returns>The http response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for an custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
``` 

La méthode `Run` mise à jour inclut désormais la liaison d’entrée `tableStorage` qui a été ajoutée pour le stockage Table Azure. La première partie de la méthode lit désormais l’en-tête `x-ms-customproviders-requestpath` et utilise la bibliothèque `Microsoft.Azure.Management.ResourceManager.Fluent` pour analyser la valeur comme ID de ressource. L’en-tête `x-ms-customproviders-requestpath` est envoyé par le fournisseur personnalisé et désigne le chemin de la demande entrante. À l’aide de l’ID de ressource analysé, nous pouvons à présent générer partitionKey et rowKey pour les données afin de rechercher ou de stocker des ressources personnalisées.

Outre l’ajout de classes et de méthodes, nous devons mettre à jour les méthodes using de la fonction. Ajoutez ce qui suit au début du fichier :

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent.dll"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Si vous avez perdu le fil à un moment ou un autre de ce tutoriel, vous pouvez obtenir l’exemple de code complet dans la [référence sur le point de terminaison RESTful C# pour les fournisseurs personnalisés](./reference-custom-providers-csharp-endpoint.md). Une fois la fonction terminée, enregistrez l’URL de fonction servant à déclencher la fonction, car vous en aurez besoin dans les tutoriels suivants.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé un point de terminaison RESTful qui fonctionne avec le point de terminaison (`endpoint`) d’un fournisseur personnalisé Azure. Passez à l’article suivant pour apprendre à créer un fournisseur personnalisé.

- [Tutoriel : Création d’un fournisseur personnalisé](./tutorial-custom-providers-create.md)
