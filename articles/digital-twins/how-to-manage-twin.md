---
title: Gérer des jumeaux numériques
titleSuffix: Azure Digital Twins
description: Consultez comment récupérer, mettre à jour et supprimer des jumeaux et des relations individuels.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0f4d9811dc288222c0a2190805a8b052cb1ae47b
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87563923"
---
# <a name="manage-digital-twins"></a>Gérer des jumeaux numériques

Les entités de votre environnement sont représentées par des [jumeaux numériques](concepts-twins-graph.md). La gestion de vos jumeaux numériques peut inclure la création, la modification et la suppression. Pour effectuer ces opérations, vous pouvez utiliser les [**API DigitalTwins**](how-to-use-apis-sdks.md), le [SDK .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) ou la [CLI Azure Digital Twins](how-to-use-cli.md).

Cet article se concentre sur la gestion des jumeaux numériques. Pour utiliser des relations et le [graphe des jumeaux](concepts-twins-graph.md) dans leur ensemble, consultez [*Procédure : Gérer le graphe de jumeaux avec des relations*](how-to-manage-graph.md).

> [!TIP]
> Toutes les fonctions du Kit de développement logiciel (SDK) sont disponibles en versions synchrone et asynchrone.

## <a name="create-a-digital-twin"></a>Créer un jumeau numérique

Pour créer un jumeau, vous utilisez la méthode `CreateDigitalTwin` sur le client de service comme suit :

```csharp
await client.CreateDigitalTwinAsync("myNewTwinID", initData);
```

Pour créer un jumeau numérique, vous devez fournir les éléments suivants :
* L’ID souhaité pour le jumeau numérique
* Le [modèle](concepts-models.md) à utiliser 

Si vous le souhaitez, vous pouvez fournir des valeurs initiales pour toutes les propriétés du jumeau numérique. 

Le modèle et les valeurs des propriétés initiales sont fournis par le biais du paramètre `initData`, qui est une chaîne JSON contenant les données pertinentes.

> [!TIP]
> Après la création ou la mise à jour d’un jumeau, il peut y avoir une latence allant jusqu’à 10 secondes avant que les modifications soient reflétées dans les [requêtes](how-to-query-graph.md). L’API `GetDigitalTwin` (décrite [plus loin dans cet article](#get-data-for-a-digital-twin)) ne subit pas ce délai. Utilisez l’appel d’API au lieu d’une interrogation pour voir vos nouveaux jumeaux créés si vous avez besoin d’une réponse instantanée. 

### <a name="initialize-properties"></a>Initialiser les propriétés

L’API de création de jumeau accepte un objet qui peut être sérialisé dans une description JSON valide des propriétés du jumeau. Voir [*Concepts : Jumeaux numériques et graphe des jumeaux*](concepts-twins-graph.md) pour obtenir une description du format JSON pour un jumeau.

Vous pouvez créer un objet de paramètre manuellement ou à l’aide d’une classe d’assistance fournie. Voici un exemple de chaque méthode.

#### <a name="create-twins-using-manually-created-data"></a>Créer des jumeaux à l’aide de données créées manuellement

```csharp
// Define the model type for the twin to be created
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:com:contoso:Room;1" }
};
// Initialize the twin properties
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", temperature},
    { "Humidity", humidity},
};
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

#### <a name="create-twins-with-the-helper-class"></a>Créer des jumeaux avec la classe d’assistance

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
props.Add("Humidity", 50.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

## <a name="get-data-for-a-digital-twin"></a>Obtenir des données pour un jumeau numérique

Vous pouvez accéder à la totalité des données de n’importe quel jumeau numérique en appelant :

```csharp
object result = await client.GetDigitalTwin(id);
```

Cet appel retourne les données du jumeau sous forme de chaîne JSON. 

> [!TIP]
> Seules les propriétés qui ont été définies au moins une fois sont retournées lorsque vous récupérez un jumeau avec `GetDigitalTwin`.

Pour récupérer plusieurs jumeaux à l’aide d’un seul appel d’API, consultez les exemples d’API de requête dans [*Procédure : Interroger le graphique de jumeaux*](how-to-query-graph.md).

Prenez pour exemple le modèle suivant (écrit en [DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)) qui définit une *Lune* :

```json
{
    "@id": " dtmi:com:contoso:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "contents": [
        {
            "@type": "Property",
            "name": "radius",
            "schema": "double",
            "writable": true
        },
        {
            "@type": "Property",
            "name": "mass",
            "schema": "double",
            "writable": true
        }
    ]
}
```

Le résultat de l’appel de `object result = await client.DigitalTwins.GetByIdAsync("my-moon");` sur un jumeau de type *Lune* peut se présenter comme suit :

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:com:contoso:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

Les propriétés définies du jumeau numérique sont retournées en tant que propriétés de niveau supérieur sur le jumeau numérique. Les métadonnées ou les informations système qui ne font pas partie de la définition DTDL sont retournées avec un préfixe `$`. Les propriétés des métadonnées incluent :
* L’ID du jumeau numérique dans cette instance Azure Digital Twins, comme `$dtId`.
* `$etag`, un champ HTTP standard attribué par le serveur web
* Autres propriétés dans une section `$metadata`. notamment :
    - DTMI du modèle du jumeau numérique.
    - État de synchronisation de chaque propriété accessible en écriture. Cela est très utile pour les appareils, où il est possible que le service et l’appareil aient des états différents (par exemple, lorsqu’un appareil est hors connexion). Actuellement, cette propriété s’applique uniquement aux appareils physiques connectés à IoT Hub. Avec les données dans la section des métadonnées, il est possible de comprendre l’état complet d’une propriété, ainsi que les horodatages des dernières modifications. Pour plus d’informations sur l’état de la synchronisation, consultez [ce tutoriel IoT Hub](../iot-hub/tutorial-device-twins.md) sur la synchronisation de l’état de l’appareil.
    - Les métadonnées spécifiques au service, par exemple à partir d’IoT Hub ou d’Azure Digital Twins. 

Vous pouvez analyser le JSON retourné pour le jumeau à l’aide d’une bibliothèque d’analyse JSON de votre choix, par exemple `System.Text.Json`.

Vous pouvez également utiliser la classe d’assistance de sérialisation `BasicDigitalTwin` qui est incluse avec le kit SDK, qui retourne les propriétés et les métadonnées de base du jumeau dans un format préalablement analysé. Voici un exemple :

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

Pour plus d’informations sur les classes d’assistance de sérialisation, consultez [*Procédure : Utiliser les API et les kits de développement logiciel (SDK) Azure Digital Twins*](how-to-use-apis-sdks.md).

## <a name="update-a-digital-twin"></a>Mettre à jour un jumeau numérique

Pour mettre à jour les propriétés d’un jumeau numérique, vous écrivez les informations que vous souhaitez remplacer au format de [correctif JSON](http://jsonpatch.com/). De cette façon, vous pouvez remplacer plusieurs propriétés à la fois. Vous transmettez ensuite le document de correctif JSON dans une méthode `Update` :

```csharp
await client.UpdateDigitalTwin(id, patch);
```

> [!TIP]
> Après la création ou la mise à jour d’un jumeau, il peut y avoir une latence allant jusqu’à 10 secondes avant que les modifications soient reflétées dans les [requêtes](how-to-query-graph.md). L’API `GetDigitalTwin` (décrite [plus loin dans cet article](#get-data-for-a-digital-twin)) ne subit pas ce délai. Utilisez l’appel d’API au lieu d’une interrogation pour voir vos jumeaux mis à jour si vous avez besoin d’une réponse instantanée. 

Voici un exemple de code de correctif JSON. Ce document remplace les valeurs des propriétés *masse* et *rayon* du jumeau numérique auquel il est appliqué.

```json
[
  {
    "op": "replace",
    "path": "/mass",
    "value": 0.0799
  },
  {
    "op": "replace",
    "path": "/radius",
    "value": 0.800
  }
]
```

Vous pouvez créer des correctifs manuellement ou à l’aide d’une classe d’assistance de sérialisation dans le [kit SDK](how-to-use-apis-sdks.md). Voici un exemple de chaque méthode.

#### <a name="create-patches-manually"></a>Créer des correctifs manuellement
```csharp
List<object> twinData = new List<object>();
twinData.Add(new Dictionary<string, object>() {
    { "op", "add"},
    { "path", "/Temperature"},
    { "value", 25.0}
});

await client.UpdateDigitalTwinAsync(twinId, JsonConvert.SerializeObject(twinData));
```

#### <a name="create-patches-using-the-helper-class"></a>Créer des correctifs à l’aide de la classe d’assistance

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
```

### <a name="update-properties-in-digital-twin-components"></a>Mettre à jour les propriétés dans les composants des jumeaux numériques

Rappelez-vous qu’un modèle peut contenir des composants, ce qui lui permet d’être constitué d’autres modèles. 

Pour corriger les propriétés dans les composants d’un jumeau numérique, vous allez utiliser la syntaxe du chemin dans le correctif JSON :

```json
[
  {
    "op": "replace",
    "path": "/mycomponentname/mass",
    "value": 0.0799
  }
]
```

### <a name="update-a-digital-twins-model"></a>Mettre à jour le modèle d’un jumeau numérique

La fonction `Update` peut également être utilisée pour migrer un jumeau numérique vers un modèle différent. 

Par exemple, examinez le document de correctif JSON suivant qui remplace le champ `$model` des métadonnées du jumeau numérique :

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:contoso:foo;1"
  }
]
```

Cette opération réussit uniquement si le jumeau numérique modifié par le correctif est conforme au nouveau modèle. 

Prenons l’exemple suivant :
1. Imaginez un jumeau numérique avec un modèle *foo_old*. *foo_old* définit une propriété obligatoire : *masse*.
2. Le nouveau modèle *foo_new* définit une propriété de masse et ajoute une nouvelle propriété obligatoire : *température*.
3. Après le correctif, le jumeau numérique doit avoir une propriété de masse et de température. 

Le correctif pour cette situation doit mettre à jour le modèle et la propriété de température du jumeau, comme suit :

```json
[
  {
    "op": "replace",
    "path": "$metadata.$model",
    "value": "dtmi:com:contoso:foo_new"
  },
  {
    "op": "add",
    "path": "temperature",
    "value": 60
  }
]
```

## <a name="delete-a-digital-twin"></a>Supprimer un jumeau numérique

Vous pouvez supprimer des jumeaux à l’aide de `DeleteDigitalTwin(ID)`. Toutefois, vous ne pouvez supprimer un jumeau que s’il n’a plus de relations. Vous devez d’abord supprimer toutes les relations. 

Voici un exemple de code pour cette opération :

```csharp
static async Task DeleteTwin(string id)
{
    await FindAndDeleteOutgoingRelationshipsAsync(id);
    await FindAndDeleteIncomingRelationshipsAsync(id);
    try
    {
        await client.DeleteDigitalTwin(id);
    } catch (RequestFailedException exc)
    {
        Console.WriteLine($"*** Error:{exc.Error}/{exc.Message}");
    }
}

public async Task FindAndDeleteOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);

        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
            Log.Ok($"Deleted relationship {rel.Id} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
    }
}

async Task FindAndDeleteIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipssAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        await foreach (IncomingRelationship incomingRel in incomingRels)
        {
            await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
            Log.Ok($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

### <a name="delete-all-digital-twins"></a>Supprimer tous les jumeaux numériques

Pour obtenir un exemple de suppression simultanée de tous les jumeaux numériques, téléchargez l’exemple d’application utilisé dans le [*Tutoriel : Explorer les bases avec un exemple d’application cliente*](tutorial-command-line-app.md). Le fichier *CommandLoop.cs* fait cela dans une fonction `CommandDeleteAllTwins`.

## <a name="manage-twins-with-cli"></a>Gérer des jumeaux avec l’interface CLI

Les jumeaux peuvent également être gérés à l’aide de l’interface CLI Azure Digital Twins. Les commandes se trouvent dans [*Guide pratique : Utiliser l’interface CLI Azure Digital Twins*](how-to-use-cli.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment créer et gérer des relations entre vos jumeaux numériques :
* [*Guide pratique : Gérer le graphe de jumeaux avec des relations*](how-to-manage-graph.md)