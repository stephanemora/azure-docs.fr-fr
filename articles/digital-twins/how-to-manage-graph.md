---
title: Gérer le graphe de jumeaux avec des relations
titleSuffix: Azure Digital Twins
description: Consultez la procédure de gestion d’un graphique de jumeaux numériques via la connexion de ceux-ci à des relations.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7f7239e0c13478af712d8e8d9dad8fda23fe42c7
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87125530"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Gérer un graphique de jumeaux numériques à l’aide de relations

Azure Digital Twins consiste en un [graphique de jumeaux](concepts-twins-graph.md) représentant l’ensemble de votre environnement. Le graphique de jumeaux est constitué de jumeaux numériques individuels connectés via des **relations**.

Une fois que vous disposez d’une [instance Azure Digital Twins](how-to-set-up-instance-scripted.md) opérationnelle et que vous avez configuré un code d’[authentification](how-to-authenticate-client.md) dans votre application cliente, vous pouvez utiliser les [**API DigitalTwins**](how-to-use-apis-sdks.md) pour créer, modifier et supprimer des jumeaux numériques et leurs relations dans une instance Azure Digital Twins. Vous pouvez également utiliser le [Kit de développement logiciel (SDK) .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) ou l’[interface CLI Azure Digital Twins](how-to-use-cli.md).

Cet article se concentre sur la gestion des relations et du graphique dans son ensemble. Pour utiliser des jumeaux numériques individuels, consultez [*Guide pratique : Gérer des jumeaux numériques*](how-to-manage-twin.md).

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Créer des relations

Les relations décrivent les interconnexions entre différents jumeaux numériques, ce qui constitue la base du graphique de jumeaux.

Les relations sont créées à l’aide de l’appel `CreateRelationship`. 

Pour créer une relation, vous devez spécifier :
* l’ID de jumeau source (le jumeau à l’orignie de la relation) ;
* l’ID de jumeau cible (le jumeau de destination de la relation) ;
* un nom de relation
* un ID de relation.

L’ID de relation doit être unique au sein du jumeau source donné. Il ne doit pas être globalement unique.
Par exemple, pour le jumeau *foo*, chaque ID de relation spécifique doit être unique. Toutefois, un autre jumeau *bar* peut avoir une relation sortante qui correspond au même ID d’une relation *foo*. 

L’exemple de code suivant illustre la procédure d’ajout d’une relation à votre instance Azure Digital Twins.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

Pour plus d’information sur la classe d’assistance `BasicRelationship`, consultez [*Guide pratique : Utiliser les kits SDK et les API Azure Digital Twins*](how-to-use-apis-sdks.md).

## <a name="list-relationships"></a>Lister les relations

Pour accéder à la liste des relations d’un jumeau donné dans le graphique, vous pouvez utiliser :

```csharp
await client.GetRelationshipsAsync(id);
```

Ceci retourne un `Azure.Pageable<T>` ou `Azure.AsyncPageable<T>`, selon que vous utilisez la version synchrone ou asynchrone de l’appel.

Voici un exemple complet qui récupère une liste de relations :

```csharp
public async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw if an error occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);
        List<BasicRelationship> results = new List<BasicRelationship>();
        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            results.Add(rel);
        }
        return results;
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
        return null;
    }
}
```

Vous pouvez utiliser les relations récupérées pour accéder à d’autres jumeaux de votre graphique. Pour ce faire, lisez le champ `target` à partir de la relation retournée et utilisez-le comme ID de votre prochain appel à `GetDigitalTwin`. 

### <a name="find-relationships-to-a-digital-twin"></a>Rechercher des relations avec un jumeau numérique

Azure Digital Twins dispose également d’une API permettant de rechercher toutes les relations entrantes avec un jumeau donné. Cela s’avère souvent utile pour la navigation inverse ou lors de la suppression d’un jumeau.

L’exemple de code précédent se concentrait sur la recherche de relations sortantes. L’exemple suivant est similaire, mais recherche plutôt des relations entrantes. Ils les supprime également une fois qu’il les a trouvées.

Notez que les appels `IncomingRelationship` ne retournent pas le corps complet de la relation.

```csharp
async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        List<IncomingRelationship> results = new List<IncomingRelationship>();
        await foreach (IncomingRelationship incomingRel in incomingRels)
            results.Add(incomingRel);
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

## <a name="delete-relationships"></a>Supprimer des relations

Vous pouvez supprimer des relations à l’aide de `DeleteRelationship(source, relId);`.

Le premier paramètre spécifie le jumeau source (le jumeau à l’origine de la relation). L’autre paramètre est l’ID de relation. Vous avez besoin de l’ID de jumeau et de l’ID de relation, car les ID de relation ne sont uniques que dans l’étendue d’un jumeau.

## <a name="create-a-twin-graph"></a>Créer un graphique de jumeaux 

L’extrait de code suivant utilise les opérations de relation de cet article pour créer un graphique de jumeaux en dehors des relations et des jumeaux numériques.

```csharp
static async Task CreateTwins()
{
    // Create twins - see utility functions below 
    await CreateRoom("Room01", 68, 50, false, "");
    await CreateRoom("Room02", 70, 66, true, "EId-00124");
    await CreateFloorOrBuilding("Floor01", makeFloor:true);

    // Create relationships
    await AddRelationship("Floor01", "contains", "Floor-to-Room01", "Room01");
    await AddRelationship("Floor01", "contains", "Floor-to-Room02", "Room02");
}

static async Task<bool> AddRelationship(string source, string relationship, string id, string target)
{
    var relationship = new BasicRelationship
    {
        TargetId = target,
        Name = relationship
    };

    try
    {
        string relId = $"{source}-contains->{target}";
        await client.CreateRelationshipAsync(source, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
        return true;
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
        return false;
    }
}

static async Task<bool> CreateRoom(string id, double temperature, double humidity)
{
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = "dtmi:com:contoso:Room;2";
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("Temperature", temperature);
    props.Add("Humidity", humidity);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin)); 
        return true;       
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}

static async Task<bool> CreateFloorOrBuilding(string id, bool makeFloor=true)
{
    string type = "dtmi:com:contoso:Building;3";
    if (makeFloor==true)
        type = "dtmi:com:contoso:Floor;2";
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = type;
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("AverageTemperature", 0);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));  
        return true;      
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}
```

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>Créer un graphique de jumeaux à partir d’une feuille de calcul

Dans des cas d’usage pratiques, les hiérarchies de jumeaux sont souvent créées à partir des données stockées dans une base de données différente ou peut-être dans une feuille de calcul. Cette section illustre les options d’analyse d’une feuille de calcul.

Examinez le tableau de données suivant, qui décrit un ensemble de jumeaux numériques et de relations à créer.

| Modèle    | id | Parent | Nom de la relation | Autres données |
| --- | --- | --- | --- | --- |
| floor    | Floor01 | | | … |
| room    | Room10 | Floor01 | contains | … |
| room    | Room11 | Floor01 | contains | … |
| room    | Room12 | Floor01 | contains | … |
| floor    | Floor02 | | | … |
| room    | Room21 | Floor02 | contains | … |
| room    | Room22 | Floor02 | contains | … |

Le code suivant utilise l’[API Microsoft Graph](https://docs.microsoft.com/graph/overview) pour lire une feuille de calcul et construire un graphique de jumeaux Azure Digital Twins à partir des résultats.

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string type = row[0].GetString();
    string id = row[1].GetString();
    string relSource = row[2].GetString();
    string relName = row[3].GetString();
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (relSource != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = relSource,
            TargetId = id,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.CustomProperties = initData;
    // Set the type of twin to be created
    switch (type)
    {
        case "room":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Room;2" };
            break;
        case "floor":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Floor;2" };
            break;
        ... handle additional types
    }
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
    }
    catch (RequestFailedException e)
    {
        Log.Error($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            client.CreateRelationship(rec.SourceId, Guid.NewGuid().ToString(), JsonSerializer.Serialize<BasicRelationship>(rec));
        }
        catch (RequestFailedException e)
        {
            Log.Error($"Error {e.Status}: {e.Message}");
        }
    }
}
```
## <a name="manage-relationships-with-cli"></a>Gérer les relations avec une interface CLI

Les jumeaux et leurs relations peuvent également être gérés à l’aide de l’interface CLI Azure Digital Twins. Les commandes se trouvent dans [*Guide pratique : Utiliser l’interface CLI Azure Digital Twins*](how-to-use-cli.md).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l’interrogation d’un graphique de jumeaux Azure Digital Twins :
* [*Concepts : langage de requête*](concepts-query-language.md)
* [*Guide pratique : Interroger le graphe de jumeaux*](how-to-query-graph.md)